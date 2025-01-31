---
keywords:
  - Adobe I/O
  - Extensibility
  - API Documentation
  - Developer Tooling
title: Integration Guide with Adobe Experience Cloud
---

# Integration with Adobe Experience Cloud

> Note: this section shows how to integrate custom App Builder applications with Adobe Experience Cloud (AEC). App Builder can also be used to extend the AEC User Interface, as discussed [here](../extensions/extensions.md), with links to Developer documentation and a sample project.

Adobe Experience Cloud (AEC) consists of solutions to power insights, content, engagement, commerce, optimization, and more.  Its [unified shell web application](https://experience.adobe.com) provides a unified user experience for customers to manage these solutions from a single place.

AEC solution experiences run within an iframe in the unified shell, and can interact with its including the topbar, menus, nps, and alerts. These interactions are made possible through:

1. A module-runtime script injected into the product iframe

2. The [@adobe/exc-app](https://www.npmjs.com/package/@adobe/exc-app) package that provides an API to interact with the injected module-runtime script

## Getting started

To get started with this integration, first:

1. Include the runtime loader script on the home page
2. Include the [@adobe/exc-app](https://www.npmjs.com/package/@adobe/exc-app) package in your NPM `package.json` and invoke the `init` API:

```typescript
import React from 'react';
import ReactDOM from 'react-dom';
import {init} from '@exc/runtime';

init(runtime => {
  // Example initialization for a solution that uses React
  ReactDOM.render(<MyProductPage runtime={runtime} />, document.querySelector('#main'));
});
```

## Features

### APIs

This is a glossary of Experience Cloud integration APIs, with links to details:

**[Index](modules/index.md)** lets solutions initialize the application, provide access to the runtime object from anywhere in the app, and listen to events.

**[Page](modules/page.md)** lets solutions interact with and personalize the main page by setting the title or favicon, refreshing the solution iframe, and so on.

**[TopBar](modules/topbar.md)** lets solutions interact with and personalize the top bar by configuring the left-side solution area, setting up workspaces, arranging custom search, and so on.

**[User](./modules/user.md)** requests user-specific information including IMS organization, IMS profile, access token, tenant, and so on. It also gives solutions capabilities such as notifying the shell that a session has expired and configuring logout URLs to expire custom sessions.

### Events

Events are emitted by the module-runtime when it receives certain messages from the unified shell.

#### Ready

The Ready event fires when the initial configuration is received from the shell. It makes sense to wait for this event before rendering the application or setting any workspaces: the locale is required for globalization, and workspaces must be translated before setting them on the shell, since we don’t have translations for every solution’s workspaces.

***Example:***

```typescript
import excApp from '@exc/runtime';

function setup() {
  const runtime = excApp();
  runtime.on('ready', ({imsOrg, imsToken, locale}) => {
  this.setState({
      imsOrg,
      imsToken,
      loading: false,
      locale
  });
  });
}
```

***Payload:***

- `baseUrl`: Base url for the solution
- `environment`: Environment being used
- `historyType`: Type of history
- `imsEnvironment`: IMS environment, which follows general rules unless the  `shell_ims query` parameter is in use
- `imsOrg`: IMS organization ID
- `imsOrgName`: Name of the IMS organization
- `imsProfile`: Object containing information about the authenticated user
- `imsToken`: IMS token
- `locale`: Locale string for globalization
- `preferredLanguages`: List of preferred languages from the user's IMS profile
- `shellInfo`: Shell-related information needed to populate the shell UI
- `tenant`: tenant name for current ims organization

#### Configuration

The Configuration event fires when any configuration change arrives from the shell. It has the same payload as the Ready event.

***Example:***

```typescript
import runtime from '@exc/runtime';

function setup() {
  const runtime = excApp();
  runtime.on('configuration', ({imsOrg, imsToken, locale}) => {
    if (imsOrg !== this.state.imsOrg) {
      // Change org
    }
    this.setState({imsOrg, imsToken, locale});
  });
}
```

#### History

The History event fires when the browser history changes and the frame needs to know about it.

***Example:***

```typescript
import runtime from '@exc/runtime';

function setup() {
  const runtime = excApp();
  runtime.on('history', ({type, path}) => {
    const cleanedPath = path[0] === '/' ? path : '/' + path;
    if (type === 'external' && this.history.location.pathname !== cleanedPath) {
      this.history.replace(cleanedPath);
    }
  });
}
```

***Payload:***

- `type`: Internal or external, depending on the event's origin
- `path`: The new path, for updating the browser history

## Licensing

This project is licensed under the Creative Commons Attribution-NoDerivatives 4.0 International Public License. See [Licenses](https://creativecommons.org/licenses/by-nd/4.0/) for more information.

## Next steps

Explore details of the [Index](modules/index.md), [Page](modules/page.md), [TopBar](modules/topbar.md), and [User](modules/user.md) external modules.

Explore the attributes and behavior of Adobe Experience Cloud Interfaces:

[Modules](interfaces/modules.md)

[Runtime](interfaces/runtime.md)

| Page                                                        | Topbar                                                                | User                                     |
|:----------------------------------------------------------- | --------------------------------------------------------------------- | ---------------------------------------- |
| [Object with Href](interfaces/page.objectwithhref.md)       | [Callback](interfaces/topbar.callback.md)                             | [User API](interfaces/user.userapi.md)   |
| [Object with Path](interfaces/page.objectwithpath.md)       | [Custom Feedback](interfaces/topbar.customfeedbackconfig.md)          | [User Info](interfaces/user.userinfo.md) |
| [Page API](interfaces/page.pageapi.md)                      | [Custom Search](interfaces/topbar.customsearchconfig.md)              |                                          |
| [Page API Properties](interfaces/page.pageapiproperties.md) | [Help Center Feedback](interfaces/topbar.helpcenterfeedbackconfig.md) |                                          |
|                                                             | [External Feedback](interfaces/topbar.externalfeedbackconfig.md)      |                                          |
|                                                             | [Solution](interfaces/topbar.solution.md)                             |                                          |
|                                                             | [Topbar API](interfaces/topbar.topbarapi.md)                          |                                          |
|                                                             | [Topbar API Properties](interfaces/topbar.topbarapiproperties.md)     | `                                        |

Return to the [Guides Index](../../guides_index.md).
