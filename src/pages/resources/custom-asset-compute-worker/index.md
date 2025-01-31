---
keywords:
  - Adobe I/O
  - Extensibility
  - API Documentation
  - Developer Tooling
title: Custom Asset Compute Worker
description: Building a Custom Asset Compute Worker leveraging 3rd-party services to generate intelligent renditions in AEM.
contributors: 
  - https://github.com/marcinczeczko 
---

# Build Custom Asset Compute Worker

![front-banner](assets/hero-banner.jpg)
<a href="https://www.freepik.com/vectors/banner">Hero banner created by starline - www.freepik.com</a>

> Note: This Code Lab is based on the [How to generate intelligent renditions with AEM as a Cloud](https://experienceleaguecommunities.adobe.com/t5/adobe-experience-manager/how-to-generate-intelligent-renditions-with-aem-as-a-cloud/m-p/379588) blog post.

Image resizing or format conversion is a relatively easy task when doing renditions in Adobe Experience Manager. 
Your project might require more robust approaches supported by intelligent image services. For example, you might need to change images to greyscale, or intelligently crop the images around faces. 

This Code Lab will guide you through the creating a [custom worker for Asset Compute][asset-compute-extensions] using App Builder, and how to use it in [Adobe Experience Manager as a Cloud service][aem-cloud].
[asset-compute-extensions]: https://docs.adobe.com/content/help/en/asset-compute/using/extend/understand-extensibility.html
[aem-cloud]: https://docs.adobe.com/content/help/en/experience-manager-cloud-service/landing/home.html
