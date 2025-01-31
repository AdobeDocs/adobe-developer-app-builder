---
keywords:
  - Adobe I/O
  - Extensibility
  - API Documentation
  - Developer Tooling
title: 'Interface: TopbarAPIProperties'
---

# Interface: TopbarAPIProperties

## Hierarchy

* **TopbarAPIProperties**
  
  ↳ [TopbarAPI](topbar.topbarapi.md)

## Properties

### customEnvLabel

• **customEnvLabel**: *string*

Gets or sets a custom environment label in the shell.

***Example:***

```typescript
topbar.customEnvLabel = 'Beta';
```

___

### solution

• **solution**: *[Solution](topbar.solution.md)*

Configuration for solution name and hero.

***Example:***

```typescript
topbar.solution = {
  icon: 'AdobeExperienceCloud',
  title: 'Adobe Experience Cloud22',
  shortTitle: 'AEC'
};
```

___

### workspaces

• **workspaces**: *WorkspaceMenu[]*

Configuration for the Shell workspaces. Workspace names should be unique, and should be localized using the unified-shell locale prior to setting runtime.workspaces.

```typescript
topbar.workspaces = [
  {name: 'Home', url: '/'},
  {name: 'ABC', url: '/abc'},
  {name: 'DEF', url: '/def'}
];
```

Unified shell also supports workspace flyout menus, where clicking on a workspace displays a dropdown menu. The top-level workspace is present in the dropdown menu as the first menu item. Menus may be nested multiple times, and used in combination with normal workspaces. By default, parent menu items are automatically added to the sub-menu because of the way the user interaction works. To prevent this, remove the url property on the parent item; it will then not be injected into the sub-menu.

```typescript
topbar.workspaces = [
  {name: 'Home', url: '/'},
  {name: 'ABC', url: '/abc'},
  {
    name: 'DEF',
    url: '/def',
    menu: [
      {
        name: 'GHI',
        url: '/def/ghi',
        menu: [
          {name: 'JKL', url: '/def/ghi/jkl'}
        ]
      }
    ]
  }
];
```

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

Return to the [Guides Index](../../../guides_index.md).
