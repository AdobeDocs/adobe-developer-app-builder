---
keywords:
  - Logging
  - Log Forwarding
  - Monitoring
title: Managing Application Logs
description: >-
  Logging is one of the most important tools in a developer's tool kit.
  Application logs allow a developer to debug an application in development as
  well as monitor it in production.
---

# Managing Application Logs

Logging is one of the most important tools in a Developer's tool kit. Application logs allow Developers to debug applications in development and monitor them in production. The App Builder [SDK](https://github.com/adobe/aio-sdk) and [CLI](https://github.com/adobe/aio-cli) provide significant capabilities to facilitate application logging from code, and viewing or forwarding them.

## Logging with App Builder SDK

The App Builder [SDK](https://github.com/adobe/aio-sdk) provides a [core logging library](https://github.com/adobe/aio-lib-core-logging) that defines an [API](https://github.com/adobe/aio-lib-core-logging/blob/master/doc/api.md) on top of the popular [WinstonJS](https://www.npmjs.com/package/winston) and [Debug](https://www.npmjs.com/package/debug) npm packages. The core logging library can be used in a Runtime action or in other libraries.

The App Builder core logging library can be also used with the App Builder [core errors library](https://github.com/adobe/aio-lib-core-errors). The core errors library streamlines error management across the [AIO SDK](https://github.com/adobe/aio-sdk) and App Builder applications. When the core errors library is used in conjunction with the core logging library, error log messages output specific error codes, as defined by each App Builder [SDK](https://github.com/adobe/aio-sdk), that further aid debugging.

Both the core logging library and the core errors library can be used directly in an App Builder application, a Runtime action, and other libraries. For example, both libraries are used in:

- The App Builder [SDK](https://github.com/adobe/aio-sdk) itself
- Higher-level SDK libraries such as the [Real-time Customer Profile](https://github.com/adobe/aio-lib-customer-profile) SDK library
- Shared Runtime actions such as the [Token Vending Machine](https://github.com/adobe/aio-tvm)

<InlineAlert slots="text" />

When a new App Builder application is bootstrapped from the [AIO CLI](https://github.com/adobe/aio-cli) using the `aio app init` command, the [generated boilerplate action code](https://github.com/adobe/generator-aio-app) integrates with both the core and errors logging libraries by default.

## Accessing logs with App Builder CLI

App Builder [CLI](https://github.com/adobe/aio-cli) exposes application logs to Developers at different levels:

- At an App Builder application level, using the `aio app` plugin
- At a Runtime action level, using the `aio runtime` plugin

### App Builder app plugin

The AIO CLI [App plugin](https://github.com/adobe/aio-cli-plugin-app) provides the `aio app logs` command, which fetches the logs for an App Builder application deployed to the App Builder Workspace configured in the current working folder. The command:

- Accepts an integer argument with the `--limit` flag that allows fetching logs from the last `n` Runtime activations
- Accepts an action name with the `--action` flag that allows fetching logs from a particular action within the App Builder application
- Allows a `--tail` or `--watch` flag that continuously fetches logs as they appear

To see more command options, run `aio app logs --help` on your terminal.

The `aio app logs` command can be used by developers or by scripts running in a [CI/CD pipeline](../deployment/cicd_for_app_builder_apps.md). The command can also be used for App Builder Applications deployed on Runtime, or running locally through the `aio app run --local` command as discussed in the [Deployment Guide](../deployment/deployment.md).

### Runtime plugin

The AIO CLI [Runtime plugin](https://github.com/adobe/aio-cli-plugin-runtime) operates at the level of a Runtime action. When a [Runtime action is invoked](https://github.com/adobe/aio-cli-plugin-runtime#aio-runtime-action-create-actionname-actionpath), the corresponding [activation's logs](https://github.com/adobe/aio-cli-plugin-runtime#aio-runtime-activation-logs-activationid) can be fetched using the `aio runtime logs <activation_id>` command.
 
This command also offers options to `--watch` or `--tail` the logs, and other options you can review by running `aio runtime logs --help`. 

As with `aio app logs`, the `aio runtime logs` command can be used by Developers or by scripts running in a [Deployment Guide](../deployment/deployment.md). The command can also be used for App Builder applications deployed on Runtime or  running locally through the `aio app run --local` command as discussed in the [Deployment Guide](../deployment/deployment.md).

## Forwarding Application logs

In addition to viewing application logs using the AIO CLI, Developers can configure App Builder applications to forward all application logs to a customer-owned log management solution. Forwarding logs has several benefits over retrieving them through the AIO CLI, especially for applications deployed in Production or Staging environments. 

Supported log management solutions include:

- Adobe I/O Runtime (default)
- [Splunk Cloud](splunk_cloud.md)
- [Splunk Enterprise](splunk_enterprise.md)
- [Azure Log Analytics](azure_log_analytics.md)
- [New Relic](new_relic.md)

Follow these links to see how to set up log forwarding using each solution.

### When to use log forwarding

This table compares storing application logs in Adobe I/O Runtime and forwarding them to a log management solution:

|              | Storing logs in I/O Runtime                                                                                                                                                                  | Forwarding logs                                                                                                                                                                                                                                        |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Setup        | Workspaces default to storing logs in Runtime. Included in App Builder SKU.                                                                                                                  | Log Forwarding must be configured per App Builder application workspace. Requires a separate log management solution.                                                                                                                                  |
| Use cases    | Good for local development, or when Developers work in separate workspaces.                                                                                                                  | Ideal for shared environments such as `Stage` and `Production` workspaces.                                                                                                                                                                             |
| Access       | Stores application logs only for failed or asynchronous activations.                                                                                                                         | Allows access to all application logs.                                                                                                                                                                                                                 |
| Retrieval    | The AIO CLI can be used to fetch logs.                                                                                                                                                       | Logs must be retrieved logs from the separate log management solution.                                                                                                                                                                                 |
| Limits       | Maximum 10 MB per activation, beyond which I/O Runtime truncates log lines.                                                                                                                  | Maximum 10 MB per activation, beyond which I/O Runtime truncates log lines.                                                                                                                                                                            |
| Throttling   | The `x-ow-extra-logging` header directs Adobe I/O Runtime to store all application logs. But it's meant to be used only in local development, and  overuse is throttled at high log volumes. | Use of `x-ow-extra-logging` is unnecessary: logs are forwarded by design.                                                                                                                                                                              |
| Retention    | Logs are retained for 7 days.                                                                                                                                                                | Forwarded logs may be retained for any duration specified.                                                                                                                                                                                             |
| Capabilities | You may view or tail application logs on a per-application or per-activation level using AIO CLI.                                                                                            | Most log management solutions support viewing logs, search through unstructured logs, and charting of data extracted from logs. Forwarding logs also allows consolidation and correlation of App Builder logs with the logs from other infrastructure. |

### Log forwarding commands

#### Setting log forwarding

This command allows you to configure log forwarding for your workspace or reset it to store logs in Adobe I/O Runtime:

```
aio app config set log-forwarding
```

Once a log forwarding configuration is set, it is stored in the `.aio` and `.env` files. Any changes made to the configuration in the `.aio` or `.env` file will be deployed when you run `aio app deploy`.

Running `aio app deploy --no-actions` or `aio app deploy --no-log-forwarding-update` skips deployment of these changes.

Please visit the Guide for your log management solution of interest to see how to set up log forwarding.

- [Splunk Cloud](splunk_cloud.md)
- [Splunk Enterprise](splunk_enterprise.md)
- [Azure Log Analytics](azure_log_analytics.md)
- [New Relic](new_relic.md)

#### Viewing the current log forwarding configuration

This command displays the current log forwarding configuration for your workspace and compares it to the locally set log forwarding configuration:

```
aio app config get log-forwarding
```

If log forwarding is not configured for your workspace, Adobe I/O Runtime will be returned by default. Note that for security reasons, Adobe never returns configured secrets from our servers.

#### Debugging log forwarding

This command outputs any errors that occurred when application logs were being forwarded to your configured log management solution:

```
aio app config get log-forwarding errors
```

It returns the most recent 10 log forwarding errors for the current log forwarding configuration, to a maximum of 30 days.

## Next steps

Return to [Guides Index](../../guides_index.md).

Return to [App Builder Overview](../../../intro_and_overview/app_builder_overview.md).
