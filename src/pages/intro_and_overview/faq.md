---
keywords:
  - App Builder
  - Extensibility
  - API Documentation
  - Developer Tooling
title: Frequently Asked Questions
---

# Frequently Asked Questions

## About App Builder

### What is App Builder?

App Builder is a complete design, application, and runtime framework for building third-party cloud-native applications that run on Adobe infrastructure to extend the functionality of Adobe Experience Platform and Adobe Experience Cloud. Developers use App Builder to create custom integrations between Adobe and third-party products, to improve operational efficiency and workflow.

### What is it used for?

App Builder is ideal for creating small actions that run repeatedly in response to events or other triggers - for example, publishing details of new pages published in AEM to a Slack channel, to keep the extended team up to date.

App Builder can also be used to create more complex applications. For example, Adobe's marketing team uses an App Builder application to manage tracking codes for marketing campaigns, and makes it available to the extended team using the same Experience Cloud URL they use to access other Adobe Experience Cloud solutions.

Adobe Experience Manager and Adobe Commerce customers can use App Builder to create UI extensions that let users work their own way during AEM authoring or in the Commerce Admin UI.

Adobe Commerce customers can also create an API Mesh, a powerful capability that uses a GraphQL mesh to aggregate API requests from a variety of back-end services. This dramatically increases performance by calling only required objects from APIs. 

### How does it compare to other application frameworks?

App Builder is not a generic serverless framework for creating cloud-native apps. It is designed specifically to help third parties extend and integrate Adobe Experience Cloud and Adobe Experience Platform. It comes pre-integrated with Adobe APIs, Events, Adobe I/O Runtime, and Adobe authentication and authorization.

### What is “in the box”?

App Builder offers a complete application framework for extending Adobe solutions:

* Support for headful (single-page) or headless apps (microservices, service-to-service integrations)
* Default UI toolkit React-Spectrum, with the option to use other toolkits
* Command-line interface and code generators for creating and managing code
* Enterprise-grade security for data and end-user access control, so System Admins can control which App Builder apps are published and who can access them
* A CI/CD pipeline, and building blocks to create custom pipelines
* An execution environment so you do not need to bring your own infrastructure:
  * Adobe I/O Runtime serverless platform for backend
  * Content Delivery Network for static assets
  * experience.adobe.com for surfacing the UI
  * Custom events for publishing events through Adobe infrastructure
  * File and data storage to persist application state
* App Builder apps are surfaced within [experience.adobe.com](https://experience.adobe.com), so end users can move seamlessly between Adobe and App Builder apps

### How is App Builder different than Adobe I/O Runtime?

App Builder builds on top of existing Adobe technologies, one of which is Adobe I/O Runtime. It makes developers more productive: by adding state storage, CDN, and custom events, App Builder makes it easy to address complex use cases that would otherwise have required purchasing these services from other vendors.

### Are the tools open source?

Yes; tools are developed under Apache License Version 2.0; I/O Runtime is built on Apache OpenWhisk, another open source project. Developers are welcome to submit pull requests; we are always happy to see external contributions.

## How to get App Builder

### How can customers license App Builder?

There are several ways to license App Builder. First, customers can purchase App Builder with Adobe Experience Manager or Adobe Commerce, or to use with Adobe Experience Platform apps such as Adobe Journey Optimizer, Customer Journey Analytics, or Real-Time Customer Data Platform.

Customers may also be entitled to App Builder as part of a previously purchased product. One pack of App Builder is included with the purchase of Adobe Experience Manager Sites Cloud Service, and one or several packs with Adobe Commerce on Cloud. If you have Adobe Experience Manager Sites Cloud Service but do not have access to App Builder, please contact your Adobe sales team to request a pack of App Builder. Adobe Commerce packs will be added automatically by Adobe.

Finally, customers can purchase a license for use with any Adobe Experience Cloud product, or add extra packs to increase capacity. Please contact your Adobe sales team for details.

### Are trials available?

Yes. App Builder Trial is offered as part of [Adobe Experience Manager Headless Trial](https://commerce.adobe.com/business-trial/sign-up?items%5B0%5D%5Bid%5D=649A1AF5CBC5467A25E84F2561274821&cli=headless_exl_banner_campaign&co=US&lang=en).

### What is included with an App Builder license?

App Builder is licensed in packs that include runtime, event, storage, and other entitlements. Please refer to our [product description page](https://helpx.adobe.com/legal/product-descriptions/adobe-developer-app-builder.html), and contact your sales team for licensing details.

### How can I get access to App Builder if my company is already licensed?

App Builder is available from either the Adobe I/O Command Line Interface (CLI) or the Adobe Developer Console to Adobe enterprise users. Users must have Developer or System Admin permissions from an Adobe Identity Management System (IMS) organization licensed for App Builder. Please contact the system administrator of your Adobe IMS organization for help getting Developer access.

### Is App Builder available to individuals who are not part of a company?

No. App Builder is availabile only to Adobe enterprise customers and partners. 

### **How can I get access to I/O Runtime?**

Since I/O Runtime is now part of App Builder, you may already have access. If not, consider an [App Builder Trial](https://commerce.adobe.com/business-trial/sign-up?items%5B0%5D%5Bid%5D=649A1AF5CBC5467A25E84F2561274821&cli=headless_exl_banner_campaign&co=US&lang=en).

## Using App Builder

### Can I extend App Builder?

Yes. App Builder was built for extensibility, from Adobe's open-source development of core functionalities to components that are fully swappable with developers' own components.

### What languages are supported by App Builder?

We currently support the JavaScript language and Node.js environment, which cover the majority of code used on the web today. The free npm Registry contains ready-made libraries developers can bring into most applications.

App Builder supports the three latest Node.js versions. When a new Node version is added to the system, old versions are still available. So old actions will still work, but new ones can't be created with the old versions. We encourage developers to update actions to the latest version in order to take advantage of the pre-warmed containers feature.

### Can I use App Builder to extend Document Cloud or Creative Cloud products?

Not now. App Builder's current focus is the Adobe Experience Platform and Adobe Experience Cloud. We may include App Builder access in the future for Adobe Document Cloud and Adobe Creative Cloud.

### How can I simplify distribution of custom applications across my company?

Enterprises can use Adobe Exchange to control application lifecycles (test, publish, unpublish), and AEM User Access Control to manage access to App Builder apps.

### How do I request support for App Builder?

App Builder uses the same support process as other Adobe Experience Cloud products. Details can be found in Adobe's [Enterprise Support Programs](https://helpx.adobe.com/support/programs/enterprise-support-programs.html).

The [Adobe Experience Leage App Builder Community](https://experienceleaguecommunities.adobe.com/t5/app-builder/ct-p/adobe-app-builder) page offers Developer support for I/O Runtime questions.

## Using I/O Runtime

### What limits does I/O Runtime impose on actions?

Limits and their default values are listed under [System Settings](../guides/runtime_guides/system_settings.md); the most important are function timeouts and the maximum payloads that can be posted to functions.

### Do ingress IP addresses for Adobe I/O Runtime ever change?

Yes, IP addresses returned when looking up an Adobe I/O Runtime endpoint's DNS record can change at any time. To avoid issues connecting and routing to the platform, clients should not cache DNS-lookup responses for Adobe I/O Runtime endpoints beyond the TTL of the DNS record. For more information, see [Secure communication with back-end services](../guides/runtime_guides/security_general.md#secure-communication-with-back-end-services) in the [Security Guide](../guides/runtime_guides/security_general.md).

### What ports and outbound connections does I/O Runtime support?

When retrieving data from external systems, your code may need to connect to an SFTP server, SMTP server, or HTTP service. As long as the external system uses one of these ports, your code should work:

`21, 22, 25, 53, 80, 123, 143, [200-299], 389, 443, 445, 465, 587, 636, 1433, [2000-2999], 3000, 3306, 4242, 4317, 4343, 5400, 5432, 5671, 5672, 6061, 6062, 6379, 6380, 6651, 8000, 8020, 8080, 8085, 8088, 8089, 8300, 8500, 8600, 9090, 9092, 9093, 9094, [9096-9352], 9354, [10000-20000], 27016, 27017, 27018, 27019, 30303, 50010, 60020`.

If you need a port not on the list, please share the use case with us.

### Where does I/O Runtime execute my actions?

I/O Runtime runs in multiple regions, deploys code in all of them, and executes it in the region closest to the caller (latency-based routing). Execution cannot be restricted to a specific region or regions.

For more information about regions and where actions are executed, see [Multiple Regions](../guides/runtime_guides/reference_docs/multiple_regions.md).
