# App Builder Configuration Files

## Overview

An App Builder application has three configuration files defined in the root of the project folder:

- `app.config.yaml` is the main configuration file that defines the application's behavior and implementation
- `.env` is used to store secrets and environment variables available during build time.
- `.aio` is populated by the `aio` CLI to store the current Developer Console Workspace details.

For security reasons, `.env` and `.aio` files should not be committed to version control.

## Complete example: `app.config.yaml`

```yaml
# standalone application config
application:
  hostname: 'customhost'
  runtimeManifest:
    packages:
      application-pkg:
        actions:
          count-apples:
            function: actions/count-apples/index.js
            web: 'yes'
            runtime: nodejs:18
            annotations:
              require-adobe-auth: true
  hooks:
    post-app-build: 'echo hook'

# extension points config
extensions:
  dx/excshell/1:
    # $include directive stores config in a separate file
    $include: ./dx-excshell-1/ext.config.yaml
  dx/asset-compute/worker/1:
    operations:
      workerProcess:
        - type: action
          impl: dx-asset-compute-worker-1/myworker
    runtimeManifest:
      packages:
        dx-asset-compute-worker-1:
          actions:
            myworker:
              function: actions/worker/index.js
              web: 'yes'
              runtime: nodejs:18

# public distribution only - customer configuration
configSchema: 
  title: 'the title'
  description: 'the description'
  properties:
    - title: 'Slack Webhook'
      type: 'string'
      description: 'Please provide the webhook used by this application. Configure in slack.com'
      envKey: 'SLACK_WEBHOOK'

# public distribution only - required products
requiredProducts: 
  - code: AEP
    minVersion: 0.0.0
    maxVersion: 1.0.0
```

### Standalone application and extensions

The `app.config.yaml` file can contain two top level fields: `application` and `extensions`; only one is required.

```yaml
# app.config.yaml

application:
  <common-config>
extensions:
  <extension-type>:
    <extension-definition>
    <common-config>
```

A project can implement a standalone application and N extensions.

### Common configuration

Extensions and the standalone application behave in a similar way: both can contain a UI and actions, and both support a common configuration. Their common configuration contains these fields:

```yaml
# <common-config>

runtimeManifest:
  <runtime-manifest>
hooks:
  <app-hooks>
actions: <path to action folder>
web: <path to web folder>
unitTest: <path to unit test folder>
e2eTest: <path to e2e test folder>
dist: <path to build output folder>
htmlCacheDuration: <cache duration for UI .html files, default: 60s>
jsCacheDuration: <cache duration for UI .js files, default: about a week>
cssCacheDuration: <cache duration for UI .css files, default: about a week>
imageCacheDuration: <cache duration for UI image (.jpg, .png, .gif, etc.) files, default: about a week>
tvmurl: <alternative tvm url used to upload the UI>
awsaccesskeyid: <upload UI to own s3, provide credentials>
awssecretaccesskey: <upload UI to own s3, provide credentials>
s3bucket: <upload UI to own s3, provide credentials>
hostname: <alternative hostname for the UI>
```

#### Runtime Manifest

The `runtimeManifest` field holds the back-end configuration deployed into Adobe I/O Runtime; its full spec can be found [here](https://github.com/apache/openwhisk-wskdeploy/tree/master/specification/html). Acceptable values for the `limits` fields below can be found on the [Runtime System Settings](../../runtime_guides/system_settings.md) page.
Here is an example `runtimeManifest`:

```yaml
runtimeManifest
   packages:
     myapp:
       license: Apache-2.0
       actions:
         generic:
           # path relative to the configuration file
           function: src/myapp/actions/generic/index.js
           web: 'yes'
           runtime: nodejs:18
           annotations:
            require-adobe-auth: true
         target:
           function: src/myapp/actions/target/index.js
           web: 'yes'
           runtime: nodejs:18
           limits:
             timeout: 60000
             memory: 512
             concurrency: 1
             logs: 10
           include:
              - ["myfilestoinclude/*.txt", "text/"]        
```

> Note: the example above demonstrates the 'include' field of an action, for cases when you may want to have a file deployed with your action code and available to your code while it runs.
> The example copies all .txt files from the `myfilestoinclude/` directory and places them in a new dir `text/` available when your action is invoked via `fs.readFile('text/somefile.txt', 'utf8', callback);` 

> Note: the example above sets limit values.  Limits are defined as:
> 
> - `timeout`: the maximum time that an action may run before it is terminated (in msec., default 60000 [1 minute], min: 100 [0.1 second], max: 3600000 [60 hours])
>   - For web actions served from the Content Delivery Network, there is a hard timeout limit of 30 seconds.
> - `memory`: the maximum allocation of memory for an action (in MB, default: 256, min: 128, max: 4096)
>   - Specifying nonstandard values like 671 can increase the number of "cold start" container initializations: Runtime maintains pre-warmed containers only for common memory sizes (128, 256, 512, 1024, etc.)
> - `concurrency`: the maximum number of action invocations that can be sent in parallel to the same container (default 200, min: 1,max: 500)
> - `logs`: the maximum log size for the action (in MB, default 10, min: 0, max: 10)
> 
> More info on `limits` can be found on the [Runtime System Settings](../../runtime_guides/system_settings.md) page.

##### Annotations

Annotations may be added to Runtime actions to modify their behavior. 

```yaml
runtimeManifest:
   packages:
     myapp:
       license: Apache-2.0
       actions:
         generic:
           annotations:
             require-adobe-auth: true
             disable-download: true  
```

In addition to the [base annotations](https://github.com/adobe-apiplatform/incubator-openwhisk/blob/master/docs/annotations.md) provided by Runtime, there are two special ones: 

- **disable-download** (default: false) determines whether action code can be downloaded. Once this annotation is set to true, it cannot be set back to false. 
- **require-adobe-auth** (Default: false) determines whether the action will require Adobe authentication to invoke. See [here](../../app_builder_guides/security/index.md#authentication-and-authorization-handling) for more.

##### API Gateway Configuration

A Runtime API Gateway configuration can be added to expose web actions over specific paths and HTTP verbs. 

> Note: It can take 5 to 10 minutes for new gateway configurations to provision. Until they do, you may see 404 errors from your API.

```yaml
runtimeManifest:
   packages:
     petsapp:
       license: Apache-2.0
       actions:
         get-pets: # Note the name of the action
           function: actions/get-pets/index.js
           web: 'yes'
           runtime: nodejs:18
         get-pet: # Note the name of the action
           function: actions/get-pet/index.js
           web: 'yes'
           runtime: nodejs:18
       apis: 
        get-pets: # API Name
          v1: # Base Path
            pets: # Relative Path
              get-pets: # Name of the action to connect this path to
                method: get
                response: http
        get-pet: # API Name
          v1: # Base Path
            pets/{petName}: # Relative Path, with a path parameter
              get-pet: # Name of the action to connect this path to
                method: get
                response: http
```

> Note: The configuration above will result in the following: 
> 
> - `GET https://adobeioruntime.net/apis/[namespace]/v1/pets`
> - `GET https://adobeioruntime.net/apis/[namespace]/v1/pets/{petName}`

> Note: The second API above defines a path parameter in the relative path by using curly braces, i.e `pets/{petName}`
> 
> - APIs using path parameters must use the `http` response type

- These options are available for `method`: get, post, put, delete, patch
- These options are available for `response`: http (default), json, text, or html

Learn more about API Gateway Configuration with the [Action APIs QuickStart](https://github.com/adobe/appbuilder-quickstarts/tree/master/action-apis).

#### Hooks to customize the tooling

Hooks can be used to customize `aio app` commands, as documented [here](https://github.com/AdobeDocs/project-firefly/blob/main/src/pages/guides/app-hooks.md).

### Extension-specific configuration

#### Extension types

The `<extension-type>` indicates which product the extension is extending. Adobe currently supports:

- `dx/excshell/1` to implement Experience Cloud Shell single-page applications
- `dx/asset-compute/worker/1` to implement AEM Asset Compute workers

#### Definition of `dx/excshell/1`

The Experience Cloud Shell extension supports a `view` operation that points to the entry html file of the SPA.
In this example, the `impl` field points to an `index.html` file stored in the `web/` folder of the extension:

```yaml
extensions
  dx/excshell/1:
    operations:
      view:
        - type: web
          impl: index.html
     web-src: web/
```

#### Definition of  `dx/asset-compute/worker/1`

The AEM Asset Compute worker extension supports a `workerProcess` operation that points to the back-end Adobe I/O Runtime action implementing the worker logic.
In this example, the `impl` field points to the  `dx-asset-compute-worker-1/worker` action defined in the `runtimeManifest`.

```yaml
extensions
  dx/asset-compute/worker/1:
    operations:
      workerProcess:
        - type: action
          impl: dx-asset-compute-worker-1/myworker
    runtimeManifest:
      packages:
        dx-asset-compute-worker-1:
          actions:
            myworker:
              function: actions/worker/index.js
              web: 'yes'
              runtime: nodejs:18
```

### The `$include` directive

The `$include` directive allows any part of `app.config.yaml` to defer to another file.
In this example, the `dx/excshell/1` configuration is stored in another `./src/dx-excshell-1/ext.config.yaml` file:

```yaml
extensions:
  dx/excshell/1:
    $include: ./src/dx-excshell-1/ext.config.yaml
```

Configuration paths defined in `./src/dx-excshell-1/ext.config.yaml` must be relative to that file.

### Public distribution configuration

A subset of configuration options is specific to publicly distributable apps, as detailed in the [Distribution](../distribution.md#public) Guide.

```yaml
configSchema: 
  title: 'the title'
  description: 'the description'
  properties:
    - title: 'Slack Webhook'
      type: 'string'
      description: 'Please provide the webhook used by this application. Configure in slack.com'
      envKey: 'SLACK_WEBHOOK'

requiredProducts: 
  - code: AEP
    minVersion: 0.0.0
    maxVersion: 1.0.0
```

## The `.env` file

The `.env` file is used to store:

- Secrets to be injected into I/O Runtime Actions
- Environment variables available to `hooks`
- Auto-generated secrets used by the `aio` CLI and prefixed by `AIO_`. These should not be edited.

```
# User secrets
ENABLE_DEV_DEBUG=true

# Auto-generated secrets
AIO_runtime_namespace=
AIO_runtime_auth=
AIO_runtime_apihost=
```

### Using environment variables in the front end

Environment variables set in .env can be accessed directly using `process.env`:

```jsx
<View showDevDebugOverlay={process.env.ENABLE_DEV_DEBUG}></View>
```
Environment variables are replaced during build time by the [parcel bundler](https://parceljs.org/).

### Using environment variables in Runtime actions

Environment variables set in .env need to be passed as inputs to an action; they are then available through the action parameters.

#### app.config.yaml

```yaml
runtimeManifest:
   packages:
     myapp:
       actions:
         generic:
           function: src/myapp/actions/generic/index.js
           web: 'yes'
           runtime: nodejs:18
           inputs:
               ENABLE_DEV_DEBUG: $ENABLE_DEV_DEBUG
```

#### Action code

```javascript
async function main (params) {
  if (params.ENABLE_DEV_DEBUG) {
    console.debug("Enabling dev tools, extra usage data will be captured...")
  }
}

exports.main = main
```

Alternatively, environment variables defined in a .env file can be configured as inputs to a package, making them accessible to all actions within the package through their parameters. 
Keep in mind that package-level inputs can still be overridden at the action level.

#### app.config.yaml
```yaml
runtimeManifest:
   packages:
     myapp:
       inputs:
         ENABLE_DEV_DEBUG: $ENABLE_DEV_DEBUG
       actions:
         generic:
           function: src/myapp/actions/generic/index.js
           web: 'yes'
           runtime: nodejs:18
```

**Note:** You can specify package-level parameters directly via the command line without redeploying. 
Refer to `aio rt package bind --help` for more details.

## `.aio`

The `.aio` file is auto-generated; it contains Developer Console-specific configuration.
This file is updated by the `aio app use` command; it should not be edited manually.

## Legacy configuration system

Apps initialized using a `@adobe/aio-cli` CLI version prior to 8.x use a legacy configuration system that we still support in newer CLI versions. These older apps do not support extensions, and may be deployed only as standalone applications.

The legacy configuration system does not have an `app.config.yaml` and instead uses:

- `.aio` to store common configuration bits, but hooks and Runtime Manifest, such as `actions` path.
- `manifest.yaml` to stores the Runtime Manifest.
- `package.json` to store hooks.
- `.env` behaves the same.

## Next steps

If you can't view your application in the App Builder Catalog of Adobe Experience Cloud, this migration protocol may be useful: [Migrating Apps to DX Experience Cloud v1 SPAs](../exc_app/migrate_app_to_exp_cloud_spa.md).

Proceed to [Webpack Configuration](webpack_configuration.md).

Return to [Guides Index](../../../guides/guides_index.md).
