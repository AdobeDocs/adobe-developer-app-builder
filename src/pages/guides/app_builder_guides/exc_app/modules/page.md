---
keywords:
  - Adobe I/O
  - Extensibility
  - API Documentation
  - Developer Tooling
title: 'External module: page'
---

# External Module: Page

Page APIs let solutions interact with and personalize the main page, for example by setting the title or favicon, or refreshing the solution iframe.

### Interfaces

- [ObjectWithHref](../interfaces/page.objectwithhref.md)
- [ObjectWithPath](../interfaces/page.objectwithpath.md)
- [PageApi](../interfaces/page.pageapi.md)
- [PageApiProperties](../interfaces/page.pageapiproperties.md)

## Type aliases

### LocationLike

Ƭ **LocationLike**: *[ObjectWithHref](../interfaces/page.objectwithhref.md) | [ObjectWithPath](../interfaces/page.objectwithpath.md)*

Defines the location-like object for which to get the shell URL. You may specify either a path or an absolute URL.

***Example:***

`{path: '/abc'}` or `{href: 'https://example.com/abc'}`

***Import:***

```typescript
import page from '@adobe/exc-app/page';
```

***Default export:***

[PageApi](../interfaces/page.pageapi.md)

***Usage:***

```typescript
import page from '@adobe/exc-app/page';

page.title = 'Experience Cloud';

// Show spinner while performing an async operation
page.spinner = true;
try {
  await performOperation();
} finally {
  page.spinner = false;
}

// Generate a shell URL that directly opens the specified solution URL
const shellUrl = page.generateShellUrl('/relative/path');

// Navigate to another solution
page.shellRedirect('/target');
```

## Next steps

Explore details of the [Index](index.md), [TopBar](topbar.md), and [User](user.md) external modules.

Explore the attributes and behavior of Adobe Experience Cloud Interfaces:

[Modules](../interfaces/modules.md)

[Runtime](../interfaces/runtime.md)

| Page                                             | Topbar                                                     | User                          |
| ------------------------------------------------ | ---------------------------------------------------------- | ----------------------------- |
| [Object with Href](../interfaces/page.objectwithhref.md) | [Callback](../interfaces/topbar.callback.md)          | [User API](../interfaces/user.userapi.md)   |
| [Object with Path](../interfaces/page.objectwithpath.md)  | [Custom Feedback](../interfaces/topbar.customfeedbackconfig.md)        | [User Info](../interfaces/user.userinfo.md) |
| [Page API](../interfaces/page.pageapi.md)                      | [Custom Search](../interfaces/topbar.customsearchconfig.md)              |                               |
| [Page API Properties](../interfaces/page.pageapiproperties.md) | [Help Center Feedback](../interfaces/topbar.helpcenterfeedbackconfig.md) |                               |
|                                                  | [External Feedback](../interfaces/topbar.externalfeedbackconfig.md)      |                               |
|                                                  | [Solution](../interfaces/topbar.solution.md)                             |                               |
|                                                  | [Topbar API](../interfaces/topbar.topbarapi.md)                          |                               |
|                                                  | [Topbar API Properties](../interfaces/topbar.topbarapiproperties.md)     |                               |

Return to the [Guides Index](../../../guides_index.md).
