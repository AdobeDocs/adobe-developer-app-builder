---
keywords:
  - Adobe I/O
  - Extensibility
  - API Documentation
  - Developer Tooling
title: Dealing with Application State
---

# Dealing with Application State

Application state could be the pre-defined variables of an action accessible across all invocations, or the dynamic values or files uploaded by web users while the app is running. App Builder provides appropriate tools to handle each of these conditions.

## Default parameters

Sometimes you want to bind the same parameter values for all invocations, or just set default values for an action. In either case, there are two options: setting parameters at the action level, or at the package level so all actions in that package inherit them. These parameters are set in the `manifest.yaml` file, as in this example:

```yaml
hello-world:
  function: actions/hello/index.js
  inputs:
    name: Joe
```

These variables may be different depending on the build environment of the app, for example different tenant names in Development, Stage, and Production environments. To make it work seamlessly with Git commits, you could store the real value of the variables in the `.env` file (which is not committed to Git), and reference them in the `manifest.yaml` file:

```bash
# in .env
NAME=Joe
```

```yaml
# in manifest.yaml
hello-world:
  function: actions/hello/index.js
  inputs:
    name: $NAME
```

### Considerations about security

For authentication with Adobe APIs, you should use [App Builder Security Guidelines](../security/index.md) and Adobe-supported SDKs.

For other third-party systems and APIs when provisioning actions with secrets and passwords is necessary, you can use the default parameters as demonstrated above. To support this use case, all default parameters are automatically encrypted, and decrypted just before the action code is executed. Thus, the only time you will have access to the decrypted value is while executing the action code.

## Persistence at runtime

App Builder includes access to *Files* and *State*, two storage services for persisting data dynamically from Runtime actions.

No pre-configuration is required, just install the libraries and use them in your project. Adobe will transparently use your App Builder credentials to authorize and entitle your requests.

### When should I use Files vs State?

- Files is a good choice for bandwidth-oriented transfer of large payloads; State for latency-oriented fast access
- Files supports sharing data using presigned URLs; State supports setting expirations
- As a rule of thumb, if you expect your data to grow larger than 100KBs use Files, otherwise State.

Please refer to the [feature matrix](#feature-matrix) below for a detailed comparison.

## State

We have released a [new version of State](https://github.com/adobe/aio-lib-state/releases/tag/4.0.0) built on top of Adobe's own storage service. [Legacy State](https://github.com/adobe/aio-lib-state/tree/3.x) (`@adobe/aio-lib-state` < v4, based on CosmosDB) is still available. However, we strongly advise new users to use the latest version to avoid migrating later. Note that this applies also to `State` imported from `@adobe/aio-sdk` < v6. The [feature matrix](#feature-matrix) below provides a detailed comparison of both versions.

### How is data stored?

- State is a multi-tenant storage. Data is isolated in a "State container" which maps to your I/O Runtime  and application workspaces. This means each application workspace has its own isolated data.
- You may store data in the `amer`, `emea` or `apac` region. Since these regions operate independently, they should be treated as separate instances. You may prefer a region close to your users to minimize latency, comply with the General Data Protection Regulation (GDPR), or meet other regulatory requirements. 
- Your data is not eternal. There is a configurable time-to-live (TTL) for each key-value pair: the default is one day, the maximum one year (365 days).

Region acronyms are abbreviations for business regions:

- `amer`: North, Central, and South America; data is stored in the US
- `apac`: Asia and Pacific; data is stored in Japan
- `emea`: Europe, the Middle East, and Africa; data is stored in the EU

### Getting started

#### Library usage from an I/O Runtime actions

```bash
npm install @adobe/aio-lib-state
```

```js
  const stateLib = require('@adobe/aio-lib-state')

  // init with implicit I/O Runtime credentials, default region is 'amer'.
  const state = await stateLib.init()
  // set an explicit region
  const state2 = await stateLib.init({ region: 'emea' })

  // get
  const res = await state.get('key') // res = { value, expiration }
  const value = res.value
  // put
  await state.put('key', 'value') // with default ttl of 1 day
  await state.put('another key', 'another value', { ttl: 200 }) // in seconds, use stateLib.MAX_TTL for 365 days.
  // delete
  await state.delete('key')

  // list keys using an iterator, with glob pattern support, omit the match option to list all keys
  for await (const { keys } of state.list({ match: 'ke*' })) {
    console.log(keys)
  }

  // returns true if you have at least one key and value
  await state.any()
  // returns usage statistics (storage)
  await state.stats()

  // delete selected keys matching a glob pattern
  // Note: the match option is required!
  await state.deleteAll({ match: 'ke*' })
```

Explore the [full API](https://github.com/adobe/aio-lib-state/blob/main/doc/api.md).

#### Using CLI from your local machine

Available for `aio --version` `10.2` or greater, the CLI must be run from within a valid App Builder application folder; it uses Runtime credentials to authenticate your requests to State. Each namespace has its own State container, so ensure that you access the expected instance by looking in your `.env` file for the `AIO_RUNTIME_NAMESPACE` variable.

```bash
> aio app state
Manage your App Builder State storage

USAGE
  $ aio app state COMMAND

COMMANDS
  app state delete  Delete key-values
  app state get     Get a key-value
  app state list    List key-values
  app state put     Put a key-value
  app state stats   Display stats
```

The default region is `amer`, so to access another region, use the `--region` flag or add the `AIO_STATE_REGION=emea` variable to your `.env`.

Navigate the CLI usage documentation from the repo's [README](https://github.com/adobe/aio-cli-plugin-app-storage?tab=readme-ov-file#usage) or by using the `--help` flag on the desired command.

### Limits and validation

Limits are enforced and can't be changed on a per-user basis:

- State is available only to Runtime namespaces created via the Developer Console, which follow the App Builder format: `orgId-project(-workspace)?`. Legacy namespaces are not supported.
- Maximum state value size is `1MB`.
- Maximum state key size is `1024 bytes`.
- Maximum supported TTL is `365 days`.
- Values format may be any `string|binary`.
- Keys format is `string` only `alphanumeric` with `-`,`_`,`.`.

### Quotas

Quotas are limits that depend on an organization's entitlements. Every organization with App Builder access is entitled to at least one State quota.

At the organization level, one quota provides:

- 200GB/month bandwidth usage (~5MB/min): `bandwidth usage = bytes uploaded + bytes downloaded`
- 1GB storage: `storage usage = 2 * key_sizes + value_sizes`

The quota is shared for all State containers in an organization, across all regions. They are currently tracked but not enforced.

For example: if an organization is entitled to three quotas, its total bandwidth usage should not exceed 600GB/month, and the storage across regions should not exceed 3GB.

We enforce rate-limiting at the State container (workspace) level within a region. Rate-limiting per quota unit is defined as:

- 10MB/min with up to 1MB/sec peaks for production workspaces
- 2MB/min with up to 1MB/sec peaks for non-production workspaces

If the rate-limiting quota is exceeded, the State service will return a 429 (Too Many Requests) error. However, a retry mechanism in the State library will mitigate the propagation of the error for short time windows.

For example: if an organization is entitled to 5 quotas, a production workspace will not be throttled before it consumes 50MB/min or 5MB/sec bandwidth in a single region.

### `match` option

`state.deleteAll` and `state.list` support the `match` option to filter keys.

`match` supports a glob-style pattern using the `*` character. If you have the keys `key`, `base.key`, and `key-1`:

- `match=key` will match `key`
- `match=k*` will match `key`
- `match=*k*` will match `key`, `base.key`, `key-1`
- `match=*-1` will match `key-1`
- `match=base.*-1` will match none

### List guarantees

Using `state.list`, you can iterate over the keys stored in your State container. State implements listing with a cursor-based iterator; it requires multiple calls to the State service to traverse all your keys. Please note that `list` is subject to bandwidth rate-limiting quotas, so listing many keys may result in 429 errors.

`list` offers these guarantees:

- A full iteration always returns all keys present in the container from the start to the end of a full iteration
- A full iteration never returns any key that was deleted prior to an iteration

But `list` has some limitations:

- Keys not present in a collection throughout a full iteration may or may not be returned; the behavior is undefined.
- A key may be returned multiple times across iterations, but not within the
  same iteration. You can mitigate this either by performing operations that are
  safe when applied multiple times (this is recommended when you have many keys), or by first collecting all keys in an array and removing duplicates.
- In some rare cases, `list` may return expired keys.

You can also control `list` behavior using:

- [Match to filter keys](#match-option) using a glob-style pattern.
- `countHint` to specify an approximate amount of keys returned per iteration. State doesn't guarantee the number of elements returned per iteration, attempts to return at least `countHint` elements per iteration.

### Troubleshooting

Set `DEBUG=@adobe/aio-lib-state*` to see debugging logs.

## Files

Files is currently implemented as an abstraction layer over Azure Blob. Major changes and additional features are planned: please monitor this page for changes, and visit the [Adobe I/O File Storage library](https://github.com/adobe/aio-lib-files?tab=readme-ov-file#adobe-io-lib-files) repository for additional information.

## Feature matrix

|                               | Files                           | State                                    | State Legacy                   |
| ----------------------------- | ------------------------------- | ---------------------------------------- | ------------------------------ |
| read <br/> write <br/> delete | Y                               | Y                                        | Y                              |
| list                          | Y                               | Y                                        | N                              |
| streams                       | Y                               | N                                        | N                              |
| copy                          | Y                               | N                                        | N                              |
| deleteAll                     | N                               | Y                                        | N                              |
| sharing                       | Y (pre-signed URLs)             | N                                        | N                              |
| Time-To-Live                  | N                               | Y                                        | Y                              |
| max TTL                       | infinite                        | 365 days                                 | infinite                       |
| max file/value size           | 200GB                           | 1MB                                      | 2MB                            |
| max key size                  | 1KB                             | 1KB                                      | 1KB                            |
| key charset                   | open                            | `alphanumeric` with `_-.`                | any but `/\?#`                 |
| max request load              | N/A                             | 10MB/min, 1MB/s <br/>(scalable)          | 900 RU/min (~KB/min)           |
| max storage                   | 1TB                             | 1GB (scalable)                           | 10GB                           |
| regions                       | East US <br/> West US read-only | Amer (US) <br/>Emea (EU)<br/> Apac (JPN) | East US <br/> Europe read-only |
| consistency                   | strong                          | strong                                   | eventual                       |

## Next steps

Continue to [Introduction to React Spectrum](introduction_to_react_spectrum.md).

Return to [Architecture Overview](architecture_overview.md).

Return to the [Guides Index](../../../guides/guides_index.md).
