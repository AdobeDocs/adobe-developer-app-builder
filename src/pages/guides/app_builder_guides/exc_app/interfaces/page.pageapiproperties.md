---
keywords:
  - Adobe I/O
  - Extensibility
  - API Documentation
  - Developer Tooling
title: 'Interface: PageAPIProperties'
---

# Interface: PageAPIProperties

Subset of page-level APIs available to solutions that are settable attributes.

## Hierarchy

* **PageAPIProperties**
  
  ↳ [PageAPI](page.pageapi.md)

## Properties

### favicon

• **favicon**: *string*

Gets or set the favicon for the page. If this isn't set, the default Experience Cloud favicon will be used.

***Example:***

```typescript
page.favicon = "https://img.icons8.com/color/48/000000/thumb-up.png";
```

___

### modal

• **modal**: *boolean*

Configuration to show or hide a modal with full-screen overlay. Defaults to false.

***Example:***

```typescript
page.modal = true;
```

___

### preventDefaultCombos

• **preventDefaultCombos**: *object[]*

An array of key combinations for the shell to prevent default browser behavior, or in cases where an application performs some other action.

***Example:***

```typescript
page.preventDefaultCombos = [
  {
    ctrlKey: true,
    key: 's'
  }
];
```

___

### spinner

• **spinner**: *boolean*

Gets or sets a value indicating whether or not to show a spinner on the page. This configuration value is not used for the initial loading spinner (see Route Configuration hideInitialSpinner for that), but can be used to dismiss it if the spinner needs to be dismissed before a solution invokes runtime.done().

***Example:***

```typescript
page.spinner = true;
```

___

### title

• **title**: *string*

Gets or sets the title of the page.

***Example:***

```typescript
page.title = 'Adobe Experience Cloud';
```

___

### viewportTakeover

• **viewportTakeover**: *boolean*

Configuration to alter the z-index ordering of the Shell and iframe based on whether viewport takeover is needed.

Some applications will need to open a view that takes up the entire viewport of the browser. This works by increasing the size of the iframe to the full height of the viewport, and using the `z-index` property to alternate between header and content (iframe) on top. To make this work seamlessly, a DOM element with `id="shell-placeholder"` is automatically added to the top of the `document.body` container. The Unified Shell adds a `height` property to the DOM element, effectively pushing down the rest of the application's UI below the Shell UI.

By default, the Shell will have a higher `z-index` than the iframe, so it will be usable. In order to alternate the `z-index` values, putting the iframe above the Shell, set:

```typescript
page.viewportTakeover = true;
```

Now the solution application is 100% of the browser's viewport and can open any views at 100%. When the takeover is complete, set the value back to `false` to return the Shell to the top of the application.

## Next steps

Explore details of the [Index](../modules/index.md), [Page](../modules/page.md), [TopBar](../modules/topbar.md), and [User](../modules/user.md) external modules.

Explore the attributes and behavior of Adobe Experience Cloud Interfaces:

[Modules](modules.md)

[Runtime](runtime.md)

| Page                                        | Topbar                                                     | User                     |
| ------------------------------------------- | ---------------------------------------------------------- | ------------------------ |
| [Object with Href](page.objectwithhref.md)  | [Callback](topbar.callback.md)                             | [API](user.userapi.md)   |
| [Object with Path](page.objectwithpath.md)  | [Custom Feedback](topbar.customfeedbackconfig.md)          | [Info](user.userinfo.md) |
| [API](page.pageapi.md)                      | [Custom Search](topbar.customsearchconfig.md)              |                          |
| [API Properties](page.pageapiproperties.md) | [Help Center Feedback](topbar.helpcenterfeedbackconfig.md) |                          |
|                                             | [External Feedback](topbar.externalfeedbackconfig.md)      |                          |
|                                             | [Solution](topbar.solution.md)                             |                          |
|                                             | [API](topbar.topbarapi.md)                                 |                          |
|                                             | [API Properties](topbar.topbarapiproperties.md)            | `                        |

Return to the [Guides Index](../../../guides_index.md).
