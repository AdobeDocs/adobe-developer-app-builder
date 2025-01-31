---
keywords:
  - Adobe I/O
  - Extensibility
  - API Documentation
  - Developer Tooling
title: Extension Migration Guide
---

# Migrating Legacy Applications

> Note: This information applies **only** to applications that:
> 
> * Were created before July 28, 2021
> 
> * Have not yet been migrated to the new App Builder file structure and configuration protocol
> 
> * Have not been used successfully since the October 28, 2021 retirement of the legacy App Builder file structure and configuration protocol
> 
> Unless you are trying to recover a legacy application that was created before July 28, 2021 and remained unused since October 28, 2021, this information does not apply to you.

## Understanding configuration changes

The 2021 release of support for [Extensions](extensions.md) in App Builder allowed Developers to extend Adobe Experience Cloud through App Builder applications in a more native and integrated fashion. It also required changes to App Builder project file structures and the way we compile configurations. Before starting this migration, please read through all the changes so you can make an informed decision on how to refactor your project.

### Old file structure

Under the old file structure, initialization of a new App Builder Project in the CLI created these folders and files: 

1. `actions`: folder for back-end source code of all serverless actions.
2. `web-src`: folder for front-end source code such as html templates, react components, JS, CSS.
3. `test`: folder for back-end action unit and integration tests.
4. `e2e`:  folder for end-to-end tests.
5. `manifest.yml`: file describing back-end actions to deploy or redeploy
   - `manifest` file contents should adhere to the [OpenWhisk deployment YAML specification](https://github.com/apache/openwhisk-wskdeploy/tree/master/specification#package-specification). Once defined, the [CLI](https://github.com/adobe/aio-cli) uses this file to deploy or redeploy actions. You might see values like `$CUSTOMER_PROFILE_TENANT` listed on this page; they are environment variables that can be defined in the `.env` file. 
6. `package.json`: file with the project definition and relevant metadata. 
   - `package.json` gives informs npm so it can identify the project and handle its dependencies. Learn more [here](https://nodejs.org/docs/v22.12.0/api/packages.html#packagejson-and-file-extensions).
7. `.aio`: file containing configuration variables used by the [CLI](https://github.com/adobe/aio-cli) to facilitate the application, for example supported API services. This file can be committed to a source-code versioning system.
   - You may manually update the file, or use `aio config` commands to add or to configurations. Learn more about the [Config Plugin](https://github.com/adobe/aio-cli-plugin-config). 
8. `.env`: file containing environment variables useful for application development, for example Adobe I/O Runtime credentials and Adobe Product API tenant specifics  such as API key and secrets.
   - The environment variables defined here can be used in the application (for example  in `manifest.yml`). If credentials are set up for the selected workspaces, you should see some of these values such as `AIO_runtime_auth` and `AIO_runtime_namespace`pre-populated upon initialization. 
   - This file is automatically included in `.gitignore`: the listed credentials and secrets are not intended to be shared. 
9. `console.json`: file containing credentials set up through your App Builder project. 
   - This file is also automatically included in `.gitignore`, and contains credentials and secrets not intended to be shared.
   - This file can be also downloaded directly from the Adobe Developer Console,  by going to a workspace, and clicking  the `Download all` button. 

### New file structure

When extensions were introduced, the file structure changed to:

1. `src`: Instead of one folder for all `actions` and all `web-src`, there are individual folders under `src` for each selected extension point, for example, a `dx-excshell-1` folder for Experience Cloud SPA actions and front-end resources.
   - Under each folder will be both the actions and the front-end code for the application. You should also see `ext.config.yaml`, a file containing all the action and extension configuration for the extension point corresponding to its folder. This configuration allows more flexibility in defining and managing extension points. This file is also imported to `app.config.yaml`, the master config file. 
   - The action definition in this file should adhere to the [OpenWhisk deployment YAML specification](https://github.com/apache/openwhisk-wskdeploy/tree/master/specification#package-specification).
   - Once defined, the [CLI](https://github.com/adobe/aio-cli) uses this file to deploy or redeploy actions. You might see values like `$CUSTOMER_PROFILE_TENANT` listed on this page; they are environment variables that can be defined in the `.env` file.
2. `app.config.yaml`,  the master configuration file. It follows the same principle as the individual `ext.config.yaml`, and compiles these individual files into one comprehensive configuration upon application build. 
3. `lib`: a folder containing all the shared utility actions across different extension points. 
4. `package.json`: file describes project definition and various metadata relevant to the project. 
   - It is used to give information to npm that allows it to identify the project as well as handle the project's dependencies. Learn more [here](https://nodejs.org/en/knowledge/getting-started/npm/what-is-the-file-package-json/).
5. `.aio`: this file contains config variables that are useful for the [CLI](https://github.com/adobe/aio-cli) to facilitate the app, e.g. supported API services.
   - You can manually update the file or use the `aio config` commands to add or to remove configurations. Learn more about the [Config Plugin](https://github.com/adobe/aio-cli-plugin-config). 
6. `.env`: file containing environment variables useful for application development, for example Adobe I/O Runtime credentials and Adobe Product API tenant specifics such as API key and secrets.
   - The environment variables defined here can be used in the application (for example in `manifest.yml`). If credentials are set up for the selected workspaces, you should see some of these values such as `AIO_runtime_auth` and `AIO_runtime_namespace`pre-populated upon initialization.
   - This file is automatically included in `.gitignore`: the listed credentials and secrets are not intended to be shared.

## Why migrate?

When you migrate your application:

1. You can take advantage of Extensions and new, more flexible configuration setup.
2. Your application will be protected by Adobe's validator, which offers more granular access control. Read more about this in our [Security Guide](../security/index.md).

If you don't migrate, your application will not work using the current CLI and configuration protocol.

## Step-by-step migration instructions

Please follow these steps to migrate your application:

### 1. Update tooling

Make sure your local tooling and environment setup is current, as described [here](../../../get_started/app_builder_get_started/set_up.md#local-environment-setup). 

### 2. Update configuration

Applications that are candidates for migration may extend: 

1. **No extension points**, for example standalone Headless applications or standalone Single-Page Applications that do not integrate with Experience Cloud UI
2. **One extension point**, for example Single-Page Applications accessed through the Experience Cloud UI or a Custom Processing Profile for AEM Asset Microservices
3. **Two or more extension points**, for example Custom Processing Profiles for AEM Asset Microservices that also contain one or more Single-Page Applications accessible through Experience Cloud UI

Learn more about extension points in [Introduction to Extensions](extensions.md), and then follow the instruction set below that corresponds to your project.

#### 2.1 Application with no extension points

If your application has no extension points, refactoring is not mandatory: the CLI will continue to support the old configuration protocol. However, if you plan to integrate with extension points later or simply want to bring your project up to date, refactor your codebase following the instructions below. 

> **Note:** for headless applications with `require-adobe-auth` set to `true`, refactoring the code base and redeploying the application using the new CLI will switch it to the new validator. This means Adobe will validate that tokens passed in belong to your IMS organization and contain the product profile required for your application. For instance, if your application uses the Adobe Analytics API, the token you pass in must also have access to Adobe Analytics.

##### Instructions:

1. Create the file `app.config.yaml` in the project's root directory
2. Move the content of `manifest.yml` to `app.config.yaml` under the `application.runtimeManifest` property, and delete the `manifest.yml` file
3. Move any application hooks under `scripts` in `package.json` into `applications.hooks` in the `app.config.yaml` configuration file
4. Move any configuration found under the `.app` section of the `.aio` configuration file into the `applications` section of the new `app.config.yaml` configuration file

##### Sample `app.config.yaml` file after refactoring:

```
# standalone application
application:
  hostname: 'customhost'
  runtimeManifest:
    packages:
      application-pkg:
        license: Apache-2.0
        actions:
          count-apples:
            function: actions/count-apples/index.js
            web: 'yes'
            annotations:
              require-adobe-auth: true
  hooks:
    post-app-build: 'echo hook'
```

#### 2.2 Application with one extension point

Follow the instructions in this section if your application is a Single-Page Application accessed through Experience Cloud UI or a Custom Processing Profile for AEM Asset Microservices.

##### Instructions:

1. Create the file `app.config.yaml` in the project's root directory.
2. Create a definition for the extension in your `app.config.yaml`. If you have a Single-Page Application in Experience Cloud UI, use `dx/exchshell/1:` with `operations` set to `view`. If you have a AEM Asset Microservices Custom Profile, use `dx/sset-compute/worker/1:` with `operations` set to `workerProcess` - see the sample configurations below.
3. Move the content of the `manifest.yml` under the `app.config.yaml` extensions, and then delete the `manifest.yml` file.
4. Move any application hooks under `scripts` in `package.json` into `extensions.<extensionname>.hooks` in `app.config.yaml`.
5. Move any configuration under `.app` in `.aio` into `extensions.<extensionname>` in `app.config.yaml`.

##### Sample `app.config.yaml` file after refactoring:

**SPA in Experience Cloud UI**

```
extensions:
  dx/excshell/1:
    operations:
      view:
        - type: web
          impl: index.html
    runtimeManifest:
      packages:
        <newpackagename>:
          license: Apache-2.0
          actions:
            todolist:
              function: actions/todolist/index.js
              web: 'yes'
              runtime: 'nodejs:14'
              inputs:
                LOG_LEVEL: debug
              annotations:
              require-adobe-auth: true
              final: true
  hooks:
    post-app-build: 'echo hook'
```

**AEM Asset Microservices**

```
extensions:
  dx/asset-compute/worker/1:
    operations:
      workerProcess:
        - type: web
          impl: worker
    runtimeManifest:
      packages:
        <newpackagename>:
          license: Apache-2.0
          actions:
            worker:
              function: actions/worker/index.js
              web: 'yes'
              runtime: 'nodejs:14'
              inputs:
                LOG_LEVEL: debug
              annotations:
              require-adobe-auth: true
              final: true
  hooks:
    post-app-build: 'echo hook'
```

#### 2.3 Applications with two or more extension points

Follow this section if your application is a Custom Processing Profile for AEM Asset Microservices, and contains a Single-Page Application accessible through Experience Cloud UI. 

There are multiple ways to structure your configuration:

- You could refactor your code into folders for each extension, create extension-specific configurations, and then import them into your main `app.config.yaml` using `$include: path/to/myfile.yaml`

- You could manage them all in one file in the root directory `app.config.yaml` 

- You could try to initialize a new project with multiple extension points using `aio app init` to see how Adobe structures the code and configuration file by default 

The instructions below show the easiest way to refactor by merging all config into `app.config.yaml`, but you are welcome to explore alternatives. 

##### Instructions:

1. Create the file `app.config.yaml` in the project's root directory.
2. Create definitions for the extensions in your `app.config.yaml` file. For the Single-Page Application in Experience Cloud UI, use `dx/exchshell/1:` with `operations` set to `view`. For the AEM Asset Microservices Custom Profile, use `dx/sset-compute/worker/1:` with `operations` set to `workerProcess` - see the sample configurations below.
3. Move the content of the `manifest.yml` under the `app.config.yaml` extensions, then delete `manifest.yml` file.
4. Move any application hooks under `scripts` in `package.json` into `extensions.<extensionname>.hooks` in `app.config.yaml`
5. Move any configuration under `.app` in `.aio` into `extensions.<extensionname>` in `app.config.yaml`.

##### Sample `app.config.yaml` file after refactoring:

**App Builder project extending multiple extension points**

```
extensions:
  dx/excshell/1:
    operations:
      view:
        - type: web
          impl: index.html
    runtimeManifest:
      packages:
        <newpackagename>:
          license: Apache-2.0
          actions:
            todolist:
              function: actions/todolist/index.js
              web: 'yes'
              runtime: 'nodejs:14'
              inputs:
                LOG_LEVEL: debug
              annotations:
              require-adobe-auth: true
              final: true
  dx/asset-compute/worker/1:
    operations:
      workerProcess:
        - type: web
          impl: worker
    runtimeManifest:
      packages:
        <newpackagename>:
          license: Apache-2.0
          actions:
            worker:
              function: actions/worker/index.js
              web: 'yes'
              runtime: 'nodejs:14'
              inputs:
                LOG_LEVEL: debug
              annotations:
              require-adobe-auth: true
              final: true
  hooks:
    post-app-build: 'echo hook'
```

### 3. Testing

Once refactoring is complete, test it using `aio app run` or `aio app deploy`. Everything should work normally. If you encounter any issues, please contact us through the [Experience League Developer Community](https://experienceleaguecommunities.adobe.com/t5/app-builder/ct-p/adobe-app-builder) and we'll get you the support you need as quickly as we can.

## Adding or removing endpoints from existing projects

As with actions, you can edit your extension configuration directly by modifying your code. Alternatively, you could use:

- `aio app add ext` to add a new extension point to your application
- `aio app delete ext` to delete an extension point project and configuration

## Next steps

Return to [Introduction to Extensions](extensions.md).

Return to [Guides Index](../../guides_index.md).
