---
keywords:
  - Adobe I/O
  - Extensibility
  - API Documentation
  - Developer Tooling
title: 'Interface: PageApi'
---

# Interface: PageAPI

Defines page-level APIs available to solutions.

## Hierarchy

* [PageApiProperties](page.pageapiproperties.md)
  
  ↳ **PageApi**

## Properties

### favicon

**favicon**: *string*

*Inherited from [PageApiProperties.favicon](page.pageapiproperties.md#favicon)*

Gets or set the favicon for the page. If this isn't set, then the default Experience Cloud favicon will be used.

***Example:***

```typescript
page.favicon = "https://img.icons8.com/color/48/000000/thumb-up.png";
```

___

### modal

• **modal**: *boolean*

*Inherited from [PageApiProperties.modal](page.pageapiproperties.md#modal)*

Configuration to show or hide a modal with full-screen overlay. Defaults to false.

***Example:***

```typescript
page.modal = true;
```

___

### preventDefaultCombos

• **preventDefaultCombos**: *object[]*

*Inherited from [PageApiProperties.preventDefaultCombos](page.pageapiproperties.md#preventdefaultcombos)*

An array of key combinations for the shell to prevent default browser behavior, on in cases where an application performs some other action.

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

*Inherited from [PageApiProperties.spinner](page.pageapiproperties.md#spinner)*

Gets or sets a value indicating whether or not to show a spinner on the page. This configuration value is not used for the initial loading spinner (see Route Configuration hideInitialSpinner for that), but can be used to dismiss it if the spinner needs to be dismissed before a solution invokes runtime.done().

***Example:***

```typescript
page.spinner = true;
```

___

### title

• **title**: *string*

*Inherited from [PageApiProperties.title](page.pageapiproperties.md#title)*

Gets or sets the title of the page.

***Example:***

```typescript
page.title = 'Adobe Experience Cloud';
```

___

### viewportTakeover

• **viewportTakeover**: *boolean*

*Inherited from [PageApiProperties.viewportTakeover](page.pageapiproperties.md#viewporttakeover)*

Configuration to alter the z-index ordering of the Shell and iframe based on whether viewport takeover is needed.

Some applications will need to open a view that takes up the entire viewport of the browser. This works by increasing the size of the iframe to the full height of the viewport, and using the `z-index` property to alternate between header and content (iframe) on top. To make this work seamlessly, a DOM element with `id="shell-placeholder"` is automatically added to the top of the `document.body` container. The Unified Shell adds a `height` property to the DOM element, effectively pushing down the rest of the application's UI below the Shell UI.

By default, the Shell will have a higher `z-index` than the iframe, so it will be usable. In order to alternate the `z-index` values, putting the iframe above the Shell, set:

```typescript
page.viewportTakeover = true;
```

Now the solution application is 100% of the browser's viewport and can open any views at 100%. When the takeover is complete, set the value back to `false` to return the Shell to the top of the application.

## Methods

### done

▸ **done**(): *void*

Tells the Shell that the Solution has loaded and is ready to be used by a user and dismisses the initial loading spinner.

***Example:***

```typescript
page.done();
```

**Returns:** *void*

___

### generateShellUrl

▸ **generateShellUrl**(`location`: [LocationLike](../modules/page.md#locationlike): *string*

Method to take a relative path or full iframe URL and generate a unified shell url.

***Example:***

```typescript
// returns `https://experience.adobe.com/#/@tenant/solution/abc`
page.generateShellUrl({path: '/abc'});

// returns `https://experience.adobe.com/#/@tenant/solution/abc`
page.generateShellUrl({href: 'https://example.com/abc'});
```

**Parameters:**

| Name       | Type                                            | Description                                                                                         |
| ---------- | ----------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| `location` | [LocationLike](../modules/page.md#locationlike) | Object with either a path or href key and corresponding value from which to generate the shell URL. |

**Returns:** *string*

The shell URL for the specified view of the solution.

___

### iframeReload

▸ **iframeReload**(): *void*

Triggers the reload of the solution iframe. Calling this function will regenerate the iframe source, triggering the discovery URL flow if configured.

***Example:***

```typescript
page.iframeReload();
```

**Returns:** *void*

___

### openInNewTab

▸ **openInNewTab**(`path`: string): *void*

Opens the specified URL in the shell in a new tab. This is useful in scenarios where an element won't be an anchor or link and solution needs to open the URL.

***Example:***

```typescript
page.openInNewTab('/path');
```

**Parameters:**

| Name   | Type   | Description                            |
| ------ | ------ | -------------------------------------- |
| `path` | string | The relative path within the solution. |

**Returns:** *void*

___

### shellRedirect

▸ **shellRedirect**(`path`: string): *void*

Redirects to another unified shell solution. Path should be the complete relative path of a valid unified shell solution url - for example, if shellRedirect is called from /target to /analytics, the path paremeter would need to start with /analytics. Query and hash are optional.

***Example:***

```typescript
page.shellRedirect('/path?a=b#workspace');
```

**Parameters:**

| Name   | Type   | Description                                                 |
| ------ | ------ | ----------------------------------------------------------- |
| `path` | string | Path including search and hash to a unified shell solution. |

**Returns:** *void*

## Next steps

Explore details of the [Index](../modules/index.md), [Page](../modules/page.md), [TopBar](../modules/topbar.md), and [User](../modules/user.md) external modules.

Explore the attributes and behavior of Adobe Experience Cloud Interfaces:

[Modules](modules.md)

[Runtime](runtime.md)

| Page                                        | Topbar                                                     | User                     |
| ------------------------------------------- | ---------------------------------------------------------- | ------------------------ |
| [Object with Href](page.objectwithhref.md)  | [Callback](topbar.callback.md)                             | [API](user.userapi.md)   |
| [Object with Path](page.objectwithpath.md)  | [Custom Feedback](topbar.customfeedbackconfig.md)          | [Info](user.userinfo.md) |
| [API Properties](page.pageapiproperties.md) | [Custom Search](topbar.customsearchconfig.md)              |                          |
|                                             | [Help Center Feedback](topbar.helpcenterfeedbackconfig.md) |                          |
|                                             | [External Feedback](topbar.externalfeedbackconfig.md)      |                          |
|                                             | [Solution](topbar.solution.md)                             |                          |
|                                             | [API](topbar.topbarapi.md)                                 |                          |
|                                             | [API Properties](topbar.topbarapiproperties.md)            | `                        |

Return to the [Guides Index](../../../guides_index.md).
