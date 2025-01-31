---
keywords:
  - Adobe I/O
  - Extensibility
  - API Documentation
  - Developer Tooling
title: Introduction to Extensions
---

# Introduction to Extensions

App Builder originally supported only Single-Page Applications (SPAs) accessed in [Experience Cloud UI](https://experience.adobe.com) and standalone Headless Applications. In response to customer requests, we added services like [AEM Asset Microservices](https://experienceleague.adobe.com/docs/experience-manager-cloud-service/assets/manage/asset-microservices-configure-and-use.html?lang=en) to extend Adobe products through App Builder.  But Developers still had to set up connections manually between AEM and their custom processing profiles. 

Extensions provide a simpler, more integrated way for Developers to extend products across the Adobe ecosystem. 

## How do extensions work?

Extensions act like contracts between Adobe products and App Builder applications. An Adobe product like AEM Assets defines how customers may extend the product through App Builder applications: what kind of operations are allowed, what kind of data formats are expected, and so on. This definition is called an **extension point**. Developers build applications against these Extension Points that map to the product's requirements, and define **endpoints** that map to the Extension Point. 

![extension diagram](../../../images/extensions.png)

Adobe products may define extension points - just one or two in the illustration above, but in practice as many as needed. App Builder applications expose endpoints that implement these extension points. An application may implement multiple extension points across the Adobe ecosystem, or none at all. 

This mechanism lets Developers validate that their App Builder applications will work in the context of the Adobe products they extend, and gives them a flexible, integrated way to implement the applications. 

## Available extension points

App Builder currently supports two extension points, one each for AEM Asset Microservices and Experience Cloud UI.

### AEM Asset Microservices extension point

This extension point supports building AEM Asset Microservices Custom Processing Profiles.

- **Title**: "Asset Compute Worker"
- **Name**: `dx/asset-compute/worker`
- **Version**: `1`
- **Allowed operations**:
  - `workerProcess`
    - consumes `application/json`
    - produces `application/json`

### Experience Cloud UI extension point

This extension point supports building Single-Page Applications accessed through [Experience Cloud UI](https://experience.adobe.com).

- **Title**:  "App Builder Experience Cloud Shell Extension"
- **Name**: `dx/excshell`
- **Version**: `1`
- **Allowed Operations**: 
  - `view`
    - produces `text/html`

## How can I use extension points in my project?

These instructions will show you how to set up extension point information in new projects, migrate legacy applications, and edit endpoints.

### New projects

The instructions in [Creating your First App Builder Application](../../../get_started/app_builder_get_started/first_app.md) show how to set up extension point information by default in App Builder projects. 

### Migrating projects and editing endpoints

If you would like to migrate an App Builder application created before July 28, 2021 or edit an application's endpoints, please follow the [Extension Migration Guide](extension_migration_guide.md). 

## UI extensibility

App Builder also supports creation of User-Interface extensions - JavaScript applications that can be embedded in UI Applications running under the Adobe Experience Cloud unified shell. UI extensions allow Developers to customize and add their own logic to Adobe Experience Cloud Services. 

A complete discussion and hands-on sample project may be found in the [Adobe Developer documentation for UI Extensibility](https://developer.adobe.com/uix/docs/). 

## Next steps

Explore [Adobe Developer documentation for UI Extensibility](https://developer.adobe.com/uix/docs/).

Continue to the [Extension Migration Guide](extension_migration_guide.md).

Return to the [Guides Index](../../guides_index.md).
