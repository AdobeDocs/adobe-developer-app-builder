---
keywords:
  - Headless Application
  - Headful Application
  - SDK Components
  - SDK Library
  - Migration Guides
title: Architecture Overview
description: An App Builder Application is a serverless application that extends Adobe Product APIs. These applications can be headless or headful. Each of these types is described in more detail in the sections that follow.
---

# Architecture Overview

## Types of App Builder apps

An App Builder Application is a serverless application that extends [Adobe Product APIs](https://developer.adobe.com/apis).
These applications may be headless or headful, as described below.

### Headless applications

Headless applications consist of a set of serverless actions or sequences deployed to [I/O Runtime](../../../intro_and_overview/what_is_app_builder.md#what-is-adobe-io-runtime), Adobe's serverless platform.

Headless applications integrate well with remote scripts or processes that invoke them, for example [AEM Assets workflows](https://docs.adobe.com/content/help/en/experience-manager-65/assets/using/assets-workflow.html) or [ACS activities](https://docs.adobe.com/content/help/en/campaign-standard/using/managing-processes-and-data/data-management-activities/external-api.html).

### Headful applications

Headful applications are Single-Page Applications (SPAs) with full-fledged user interfaces (UIs) served from the Content Delivery Network included with App Builder. Headful applications call Adobe Product APIs directly from the client. When there is a need to orchestrate Adobe Product API calls with third-party API calls, or with the Adobe Identity Management System for authentication, you can deploy serverless actions and sequences using Runtime].

## JAMStack: anatomy of an App Builder app

Single-Page App Builder applications with full-fledged UIs should follow the [JAMStack Architecture](https://jamstack.org/).

The three main components of App Builder apps are:

- Adobe Product APIs, exposed to external Developers and consumers through Adobe I/O API Gateway
- [Javascript-based SDK](https://github.com/adobe/aio-sdk) and serverless actions, sequences, and APIs deployed to [I/O Runtime](../../../intro_and_overview/what_is_app_builder.md#what-is-adobe-io-runtime)
- [React-Spectrum](https://react-spectrum.adobe.com/), Adobe's front-end framework that applies Adobe's design system to React-based components

![JAMStack Architecture](../../../images/jamstack-anatomy-application-march2021.png)

## SDK components

### Command-line interface (CLI)

The [CLI](https://github.com/adobe/aio-cli) is one of the main touchpoints for App Builder Developers. It is based on [oclif](https://oclif.io/),  a popular framework to build extensible command-line tools.

The CLI is supplied with these capabilities:

- [Authentication](https://github.com/adobe/aio-cli-plugin-auth) to Adobe's Identity Management System
- [Certificate management](https://github.com/adobe/aio-cli-plugin-certificate)
- [Configuration management](https://github.com/adobe/aio-cli-plugin-certificate)
- [Interactions](https://github.com/adobe/aio-cli-plugin-console) with [Adobe Developer Console](https://developer.adobe.com/developer-console/)
- [Interactions](https://github.com/adobe/aio-cli-plugin-runtime) with [I/O Runtime](../../../intro_and_overview/what_is_app_builder.md#what-is-adobe-io-runtime), Adobe's serverless platform
- [Lifecycle management](https://github.com/adobe/aio-cli-plugin-app) for App Builder

### App Builder generators

[Generators](https://github.com/adobe/generator-aio-app) help Developers bootstrap App Builder apps when using the CLI. They can be used to create:

- Headless applications
- Full-fledged UI Single-Page Applications that deploy into Adobe's [Experience Cloud Unified shell](http://experiencecloud.adobe.com/)
- Runtime serverless actions extending specific Adobe APIs

### SDK libraries

App Builder also provides a collection of JavaScript-based SDK libraries designed to increase Developer productivity when implementing custom applications on top of Adobe APIs.

#### Main SDK library

The [main SDK library](https://github.com/adobe/aio-sdk) bundles smaller, reusable SDK libraries that serve a variety of use cases:

##### Integration with Adobe APIs

- The [Adobe Analytics](https://github.com/adobe/aio-lib-analytics) SDK library provides a client for the [Adobe Analytics 2.0 API](https://adobedocs.github.io/analytics-2.0-apis/)
- The [Adobe Target](https://github.com/adobe/aio-lib-target) SDK library provides a client for the [Adobe Target 1.0 API](https://Developers.adobetarget.com/api/)
- The [Adobe Campaign Standard](https://github.com/adobe/aio-lib-campaign-standard) SDK library provides a client for the [Adobe Campaign Standard API](https://experienceleague.adobe.com/docs/campaign-standard/using/working-with-apis/get-started-apis.html?lang=en)

##### Integration with Adobe's Identity Management System (IMS)

The [Adobe IMS SDK library](https://github.com/adobe/aio-lib-core-ims) adds authentication management capabilities to Adobe's Identity Management Services, for these scenarios:

- [User-based (OAuth 2.0)](https://github.com/adobe/aio-lib-core-ims-oauth)
- [Technical account-based (JWT Bearer-token)](https://github.com/adobe/aio-lib-core-ims-jwt)

##### Integration with additional services provided with App Builder

- The [Files SDK](https://github.com/adobe/aio-lib-files) provides a file-system-like abstraction on top of cloud storage provided with App Builder, to store large temporary files
- The [State SDK](https://github.com/adobe/aio-lib-state) provides a state-like abstraction on top of the cloud-based key-value store provided with App Builder

##### Technical framework for Developers

The [Core SDK library](https://github.com/adobe/aio-sdk-core) bundled into the [main SDK library](https://github.com/adobe/aio-sdk) provides a lower-level technical framework for Developers.

This framework must be used when contributing to the App Builder SDK, and is recommended for use when building App Builder applications.

#### Core SDK library

The [Core SDK library](https://github.com/adobe/aio-sdk-core) gives Developers these technical capabilities:

##### Configuration

The [Configuration SDK library](https://github.com/adobe/aio-lib-core-config) allows configurationmanagement of persistent and environment variables .

##### Logging

The [Logging SDK library](https://github.com/adobe/aio-lib-core-logging) provides a logger abstraction that can be used in [SDK libraries](https://github.com/adobe/aio-sdk) and serverless actions deployed to Runtime.

##### Errors

The [Errors SDK library](https://github.com/adobe/aio-lib-core-errors) is the base implementation for all errors thrown by the SDK libraries; Developers may use it to manage their own errors.

##### Networking

The [Networking SDK library](https://github.com/adobe/aio-lib-core-networking) provides low-level networking tools such as exponential back-off that can be used in SDK libraries and custom API clients.

### Token-Vending Machine

The [Token-Vending Machine](https://github.com/adobe/aio-tvm) is exposed as an API deployed to Runtime, provided with App Builder.

It lets Developers perform these actions on behalf of their App Builder application credentials:

- Deploy the web assets of their App Builder application to the out-of-the-box CDN
- Use the provided cloud storage through the [Files SDK](https://github.com/adobe/aio-lib-files)
- Use the provided key-value store through the [State SDK](https://github.com/adobe/aio-lib-state)

### CI/CD support

[CI/CD support](../deployment/cicd_for_app_builder_apps.md) provided with App Builder for App Builder applications includes:

- [GitHub Actions](https://github.com/features/actions) to [setup the CLI](https://github.com/adobe/aio-cli-setup-action) and use it to [perform actions](https://github.com/adobe/aio-apps-action) such as application testing, build, and deployment
- [GitHub Workflows](https://docs.github.com/en/actions/writing-workflows) to orchestrate the GitHub Actions upon specific events triggered against the application repository
- [GitHub Secrets](https://help.github.com/en/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets) to store application secrets required for the execution of the GitHub Workflows against specific environments

### Webpack

App Builder uses webpack for bundling I/O Runtime action code. See [Webpack Configuration](../configuration/webpack_configuration.md) for an overview on how to configure webpack.

### Migration guides

- [Migrating Apps to DX Experience Cloud v1 SPAs](../exc_app/migrate_app_to_exp_cloud_spa.md) shows how to migrate App Builder applications initialized as Standalone Applications to a DX Experience Cloud Single-Page Application v1. This is useful if you can't view your application in Adobe Experience Cloud's App Builder Catalog. 

## Next steps

Continue to [Dealing with Application State](application_state.md).

Return to [App Builder Overview](../../../intro_and_overview/app_builder_overview.md).

Return to [Guides Index](../../../guides/guides_index.md).
