---
keywords:
  - Adobe I/O
  - Extensibility
  - API Documentation
  - Developer Tooling
title: Using Client SDKs for Accessing Adobe APIs
---

# Using Client SDKs for Accessing Adobe APIs

To simplify interactions with Adobe APIs, we have created a number of libraries for Adobe Experience Cloud solution APIs. These libraries are npm modules that provide JavaScript abstractions on top of Adobe product APIs. 

You can try them out directly at:

- [Adobe Analytics 2.0 Library](https://github.com/adobe/aio-lib-analytics)
- [Adobe Audience Manager Customer Data](https://github.com/adobe/aio-lib-audience-manager-cd)
- [Adobe Campaign Standard Library](https://github.com/adobe/aio-lib-campaign-standard)
- [Adobe Experience Platform Real-Time Customer Profile](https://github.com/adobe/aio-lib-customer-profile)
- [Adobe Target Library](https://github.com/adobe/aio-lib-target)

You can also get started and generate project scaffolding automatically through the CLI, using the command ```aio app init``` and selecting one of the templates. 

The current implementation focuses on API wrapping. We expect to add business abstraction layers in the future.

## Client SDK Initialization

To initialize the client SDK, provide the API key and access token as mandatory parameters. APIs may also require:

* **Tenant name**: Analytics, Campaign Standard, Target

* **Organization ID**: Audience Manager

* **Both**: AEP Customer Profile

Here is an example initialization for AEP Customer Profile:

```javascript
const client = await CustomerProfile.init(params.tenant, orgId, params.apiKey, token)
```

Among the parameters, `orgId` and `token` can be obtained from the request headers as described in [Security Overview](../security/index.md). For `token`, we provide the `getBearerToken(params)`method in the auto-generated utilities under `actions/utils.js`:

```javascript
const token = getBearerToken(params)
const orgId = params.__ow_headers['x-gw-ims-org-id']
```

The remaining parameters, `tenant` and `apiKey`, must be passed as default parameters as detailed in [Application State](./application_state.md). The real values would be set as environment variables in the `.env` file, mapped to the action default parameters in the `manifest.yml` file. Here are sample snippets taken from these files:

```bash
# in .env
SERVICE_API_KEY=your_api_key
CUSTOMER_PROFILE_TENANT=your_tenant_name
```

```yaml
# in manifest.yml
customer-profile:
  function: actions/customer-profile/index.js
  web: 'yes'
  runtime: 'nodejs:12'
  inputs:
    LOG_LEVEL: debug
    tenant: $CUSTOMER_PROFILE_TENANT
    apiKey: $SERVICE_API_KEY
  annotations:
    require-adobe-auth: true
    final: true
```

Note that most of these steps are already taken care of after actions are created from the app template using `aio app init` or `aio app add action` commands, assuming that your project is properly set up with the relevant API services. You need only set the value of tenant if it is required, and customize the action code for your needs.

## Adobe Analytics API 1.4

While many features of Adobe Analytics API have been [migrated to the new 2.0 APIs](https://developer.adobe.com/analytics-apis/docs/2.0/#!AdobeDocs/analytics-2.0-apis/master/migration-guide.md), a few, including data sources, insertion, and real-time data, are available in 1.4 APIs only. In those cases, simply select the `Generic` template for your action, and use the `node-fetch` module to make an HTTP request to the API endpoint directly, as shown in the boilerplate code. For reference, please see [our example code snippet to retrieve real-time report data](https://github.com/AdobeDocs/adobeio-samples-firefly-basics/blob/master/actions/analytics14/index.js).

## Next steps

Continue to [App Builder Application Tooling Lifecycle Event Hooks](app_hooks.md).

Return to [Guides Index](../../../guides/guides_index.md).
