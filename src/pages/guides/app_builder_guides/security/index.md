---
keywords:
  - Adobe I/O
  - Extensibility
  - API Documentation
  - Developer Tooling
title: Security Overview
---

# Security Overview

Security requirements of application development projects vary widely. Meeting them often requires complex, time-consuming implementations with substantial support at the SDK and tools level. 

App Builder saves Developers' time with an [SDK](https://github.com/adobe/aio-sdk) and [CLI](https://github.com/adobe/aio-cli) designed to make it easier and faster to secure cloud-native applications deployed into Adobe's ecosystem.

## Securing access to App Builder applications

All interactions with [Adobe Product APIs](https://developer.adobe.com/apis) need to be authenticated against Adobe Identity Management Services (IMS). [Understanding Authentication](understanding_authentication.md) outlines Adobe's approach to authentication, with links to in-depth resources and a code library.

### Adobe IMS support for App Builder applications

App Builder [SDK](https://github.com/adobe/aio-sdk) and [CLI](https://github.com/adobe/aio-cli) help developers [bootstrap applications](../../../get_started/app_builder_get_started/set_up.md) easily from templates, using `aio app init`. Included are templates for Runtime web actions, which integrate with [Adobe Product APIs](https://developer.adobe.com/apis) that can be extended using App Builder.
Any generated action is initialized with boilerplate code based on App Builder [SDK libraries](https://github.com/adobe/aio-sdk). Out of the box, these steps are implemented:

- **Validation** that an Adobe IMS bearer token has been passed as Authorization header of the request that invoked the action
- **Extraction** of this bearer token for further use in the action
- **Instantiation** of an API client, by using the appropriate product SDK library.
- A pre-configured **API call** passing the required credentials, using the same product SDK library.

### Securing access to headless App Builder applications

Headless applications, for example Runtime actions or sequences, are usually executed as back-end services invoked by other services such as Adobe products or third-party systems. Example use cases include:

- A custom asset worker integrating with AEM Assets as a Cloud Service
- An Adobe Campaign marketing activity
- A third-party CRM workflow

![Headless Access Sequence Diagram](../../../images/security-headless-access-sequence-diagram.png)

To call Adobe Product APIs successfully, headless App Builder applications must pass Adobe IMS JWT access tokens. These can be obtained within the [Developer Console](https://developer.adobe.com/developer-console/) by accessing the corresponding App Builder project and workspace.

But the tokens have only a 24-hour lifetime. To avoid requiring developers to manually refresh tokens and update application configurations every day, the [IMS SDK Library](https://github.com/adobe/aio-lib-ims) can be used to generate or renew JWT access tokens directly from a custom Runtime action. This should not be a web action, to prevent access by unauthorized users.

The SDK library uses the [State SDK Library](https://github.com/adobe/aio-lib-state) behind the scenes, to persist the token in App Builder's cloud storage on behalf of the developer between invocations of the Runtime action.

### Securing access to App Builder single-page applications (SPAs)

These SPAs are business-to-employee custom applications that deploy into the [Experience Cloud Shell](https://experience.adobe.com) for end users in enterprise organizations.

![SPA Access Sequence Diagram](../../../images/security-spa-access-sequence-diagram.png)

The SPA front end interacts with Runtime web actions on specific events triggered at UI level.
In this scenario, the Experience Cloud Shell exposes a [client-side API](../exc_app/aec_integration.md), which can be used by the SPA to obtain the OAuth token generated for the logged-in user. 
This token will be used by back-end Runtime actions to call [Adobe Product APIs](https://developer.adobe.com/apis), which need to be integrated in the application.

SPAs bootstrapped from the [CLI](https://github.com/adobe/aio-cli) using `aio app init` automatically include a [React-Spectrum](https://react-spectrum.adobe.com/)-based front end that integrates with the Experience Cloud Shell [client-side API](../exc_app/aec_integration.md), and sends the user OAuth token from the client to the invoked Runtime actions.

### Authentication and authorization handling

Every App Builder application is integrated to an out-of-the-box Authentication and Authorization-handling layer when deployed from the [CLI](https://github.com/adobe/aio-cli) using `aio app deploy`.

Whether the application is headless or an SPA, this extra security layer checks that:

- A bearer token is passed as the Authorization header of the calling request
- The token is validated against Adobe IMS for authentication
- The token is validated against [Adobe Exchange](https://exchange.adobe.com/) for authorization

[Adobe Exchange](https://exchange.adobe.com/) is the distribution platform for App Builder Applications. It authorizes a token if and only if:

- The invoked back-end action belongs to the organization for which the token was emitted
- The token is authorized to use the Adobe Product APIs integrated in the App Builder Application

![Validator Architecture](../../../images/security-validator-architecture.png)

Authentication and authorization validation is enabled by default for every Runtime action bootstrapped from the [CLI](https://github.com/adobe/aio-cli) using `aio app init` or `aio app add action`. This results in a specific `require-adobe-auth` action annotation set to `true` in the application `manifest.yml` or `app.config.yaml` file:

```
packages:
  helloworld:
    actions:
      hello:
        function: hello.js
        web: 'yes'
        annotations:
          require-adobe-auth: true
```

Upon deployment using `aio app deploy`, the manifest is dynamically rewritten, replacing custom Runtime actions with Runtime sequences. The action definition is then seamlessly rewritten into:

```
packages:
  helloworld:
    actions:
      __secured_hello:
        # non-web
        function: hello.js
    sequences:
      hello: 
        actions: '/adobeio/shared-validators/<validator>,helloworld/__secured_hello'
        web: 'yes'
```

The first action in the sequence is an out-of-the-box shared action. All data required to authenticate and authorize the calling client is extracted from the incoming request.
The data is passed to an out-of-the-box service that performs the necessary validation against Adobe IMS and Adobe Exchange. The custom action invocation will be chained if and only if validation is successful, as shown here:

![Validator Architecture](../../../images/security-validator-detailed-sequence-diagram.png)

Developers can unprotect an action by setting its `require-adobe-auth` annotation to `false`, or by deleting it and redeploying the application using `aio app deploy`. But we strongly recommend evaluating any such changes in light of the application security requirements, and retaining the `require-adobe-auth` annotation value `true` for any action that integrates with one or more [Adobe Product APIs](https://developer.adobe.com/apis).

**Note:** App Builder does not currently offer third-party API management. Developers should therefore manage authentication and authorization handling against third-party services within their custom action codes for now.

#### Known issue 1: final and web annotations

The `require-adobe-auth` annotation is not compatible with the `final` annotation, which protects default parameters in web actions. More precisely, the `final` annotation has no effect when the `require-adobe-auth` annotation is set.
This issue also applies to other web action annotations such as `web-custom-options`.
See the [GitHub Issues discussion](https://github.com/adobe/aio-cli-plugin-runtime/issues/150) for more details.

A workaround that supports final parameters without relying on the `final` annotation is to set them using the [State](https://github.com/adobe/aio-lib-state) SDK. Parameters set in State are shared among actions running within the same namespace. You can set a permanent value in State from outside an Adobe I/O Runtime action by calling this endpoint: 

```bash
curl -X POST -u <owAuth> https://adobeio.adobeioruntime.net/api/v1/web/state/put \
-H 'Content-Type: application/json' \
--data '{"namespace":"<owNamespace>","key":"<stateKey>","value":"<stateValue>","ttl":"-1"}'
```

However, we strongly discourage using the [State](https://github.com/adobe/aio-lib-state) SDK to store secrets for reuse within Adobe I/O Runtime actions. Developers who need to store secrets to meet custom application requirements should use a Secret Vault instead.

#### Known issue 2: additional scope for JWT access token validation

The validator action enabled by `require-adobe-auth: true` annotation requires the provided IMS access token to have the `read_organizations` scope. User tokens used in SPAs always have this scope, but JWT access tokens used in headless applications may not. This condition applies when integrating with these services:

- Adobe Analytics
- Adobe Campaign Standard
- No API Service enabled

To resolve this issue, add the "I/O Management API" service to the App Builder workspace. This adds the required scope to the JWT access token used by the headless application.

#### Known issue 3: overwriting params.body

Actions designed to receive data in params.body will behave differently with the validator, because the validator action currently always passes the value:

```
"body": {
  "message": "validation success"
}
```

Please use a different variable until this issue is resolved.

## Securing App Builder Applications

### I/O Runtime Specific guidelines

[Security guidelines for I/O Runtime](../../runtime_guides/security_general.md) generally apply to the back-end actions of App Builder applications. The guidelines below are specific to App Builder applications.

### Transport Security

Developers building applications on top of App Builder out-of-the-box infrastructure will benefit from the HTTPS connections among all components of this infrastructure.

We strongly recommend that every third-party system or service that integrates with an App Builder application support HTTPS connections as well.

### Tenant isolation

App Builder applications and services provide tenant isolation by default. 
App Builder applications are deployed into App Builder workspaces defined within the Developer Console for each App Builder project, and each App Builder workspace owns its own Runtime namespace.

This combination of `Enterprise Organization`, `Project`, `Workspace`, and `Runtime Namespace` defines the granular tenant isolation for every deployed App Builder application.

#### Runtime actions

Back-end Runtime actions used by App Builder applications respect the [tenant isolation model](../../../get_started/runtime_getting_started/understanding_runtime.md#security-considerations) implemented by I/O Runtime.

#### Cloud storage and content delivery network (CDN) for SPA static files

When an App Builder single-page application (SPA) is deployed into the [Experience Cloud Shell](https://experience.adobe.com), the static assets of the SPA are deployed from the CLI or a [CI/CD pipeline](../deployment/cicd_for_app_builder_apps.md) to App Builder's cloud storage and CDN.

Both of these have a strict isolation according to the Runtime namespace. Cloud Storage containers hosting App Builder SPAs can be accessed only by configuring the [CLI](https://github.com/adobe/aio-cli) with appropriate workspace credentials.

The CDN serves these static assets from a subdomain dedicated exclusively to the Runtime namespace associated with the App Builder application workspace to which the SPA is deployed.

#### Files and State services

The [Files](https://github.com/adobe/aio-lib-files) and [State](https://github.com/adobe/aio-lib-state) libraries from the App Builder SDK offer an abstraction layer for interacting with App Builder's file storage and data tier from within a Runtime action. 
Access to data stored in these underlying services is restricted to the Runtime namespace in which the action is executed.

#### App Builder Apps service

The `App Builder Apps` application available within the Experience Cloud Shell is an App Builder Application deployed according to the access and isolation protocols documented in this guide.

## Summary

The App Builder [SDK](https://github.com/adobe/aio-sdk) and [CLI](https://github.com/adobe/aio-cli) provide out-of-the-box support for Developers to implement secure Adobe-native applications that deploy into App Builder serverless infrastructure and integrate with Adobe Product APIs.

Developers can build serverless processes and user-context-aware applications with only minimal familiarity with Adobe authentication and authorization mechanisms. And   they can save time doing so, because potentially complex security processes like tenant isolation are built into the product.

## Next steps

Continue to [Understanding Authentication](understanding_authentication.md).

Return to [Guides Index](../../../guides/guides_index.md).
