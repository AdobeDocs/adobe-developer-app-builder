---
keywords:
  - Adobe I/O
  - Extensibility
  - API Documentation
  - Developer Tooling
title: Troubleshooting Common Issues
---

# Troubleshooting Common Issues

This is a guide for troubleshooting some of the most common issues you may encounter when developing App Builder apps.

## Before you proceed

- Check your Node and tool versions to make sure they are supported by App Builder and up to date. We recommend [nvm](https://github.com/nvm-sh/nvm/blob/master/README.md) for version management.
- If your application is on Dropbox or OneDrive, consider moving it: file watchers there sometimes cause unexpected errors.

## General debugging

When your action code doesn't work as expected, you may want to check the logs to investigate what went wrong. App Builder provides the [Logging SDK](https://github.com/adobe/aio-lib-core-logging) for this; please check out [App Builder's Application Logging](../guides/application_logging.md) for more details.

To see the latest activations of your project, run this command:

```bash
aio runtime activation list
```

It lists the most recent activations and a summary with ID, start and end times, duration, status, and so on. The four most common status conditions of finished activations are:

* `success`: the action executed successfully; you can obtain its result with `aio runtime activation result activationID`

* `developer error`: the most likely reasons for this are:
  
  * compilation errors such as missing variables or modules not found
  * action time outs due to issues internal issues within the action or time outs of the back-end services to which the action connects
  
  You can get activation details to see the underlying error by running the command `aio runtime activation get activationID`

* `application error`, usually due to runtime issues such as thrown exceptions or attempts to get the value of an `undefined` variable. With appropriate try-catch blocks and logging, you can see what goes wrong from the logs through the command, `aio runtime activation logs activationID`

* `internal error`caused by external factors unrelated to the action itself, for example not enough resources to run the action. Since I/O Runtime is a scalable platform, you should never see this error with the default action settings. If you do, please let us know by [email](mailto:iodev@adobe.com) so we can help you troubleshoot its cause.

You could also try running your actions locally with the `aio app dev` command.  This is very similar to `aio app run` except it runs your action code on localhost in a node process.  Not all API calls work in this context because of cors restrictions, but it is still useful for catching syntax and logic errors, and it allows step debugging of your actions without timeouts.

## Action logs

[Web actions](../../guides/runtime_guides/creating_actions.md/#invoking-web-actions) in your app are blocking requests; their activation results are not recorded if they are invoked successfully. To enforce persistence of activation results. pass the `x-ow-extra-logging: on` flag in the request headers. In the development mode of an SPA, you can add this flag directly to the "invoking action" function so you will have the activation results and logs recorded for all requests. They could then be retrieved as shown in the [General debugging](#general-debugging) section above.

```javascript
headers['x-ow-extra-logging'] = 'on'
```

## Action authentication errors

When you make requests to an action with Adobe authentication and authorization checks enabled with the `require-adobe-auth` annotation set to `true`, you may see the following errors:

1. `request is invalid, failed authorization. Please use a valid user token for this SPA.`
2. `request is invalid, failed authorization. Please use a valid JWT or user access token for this headless application.`

SPAs are applications with web UI components, located in the `web-src/` folder; headless app are back-end microservices without web UI.
For authentication and authorization checks, the back-end actions of SPAs are validated against valid user tokens  passed directly from tje Adobe Experience Cloud (ExC) Shell.

Actions of a headless app can also be validated against a valid user token from ExC Shell or generated from the [JWT (Service Account) Authentication](https://developer.adobe.com/developer-console/docs/guides/authentication/ServerToServerAuthentication/#service-account-jwt-credential-deprecated). Please review the [App Builder Security Overview](../../guides/app_builder_guides/security/index.md) for more details about SPA vs. headless app authentication.

If you are developing a headless app but accidentally have the `web-src/` folder added during the app initialization process, you could remove it by executing the command `aio app delete web-assets` at the root of your application source code folder. This will also assure that your actions are validated against the appropriate JWT auth.

## Debugging errors with State and Files SDK

If your code uses App Builder [State](https://github.com/adobe/aio-lib-state) or [Files](https://github.com/adobe/aio-lib-files) SDKs, you cannot use `aio app dev` to debug it. The reason is that State and Files services have additional security which limits calls from outside of Adobe Runtime actions. Your action code is run on localhost, which is not authorized to access the out-of-the-box cloud storage behind State and Files SDKs.

Note: This is not a problem if you configure the State or Files SDKs to connect to your own cloud storage, for example [Azure Cosmos DB](https://azure.microsoft.com/en-us/products/cosmos-db/).

## NodeJS with Mac M1 chip

There are no precompiled NodeJS binaries for versions prior to 15.x of Apple's new M1 chip with ARM64 architecture. One solution to this is to change the architecture of your shell from arm64 to x86.

We recommend using [Node Version Manager (nvm)](https://github.com/nvm-sh/nvm) over [Homebrew](https://brew.sh/) and following their [troubleshooting guides for macOS](https://github.com/nvm-sh/nvm#macos-troubleshooting), section "Macs with M1 chip."

## Next steps

This completes the "Getting Started" tutorial series for App Builder. 

To learn how to extend App Builder capabilities using the Adobe I/O Runtime platform, proceed to the [Get Started with Adobe I/O Runtime](../runtime_getting_started/runtime_intro.md) tutorial.

For in-depth review of App Builder architecture, development, deployment, integration, and security, visit the [Guides Index](../../guides/guides_index.md) and select your topic of interest.
