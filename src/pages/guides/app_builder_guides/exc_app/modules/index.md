---
keywords:
  - External Module
title: 'External module: "index"'
description: External modules
---

# External Module: Index

This API is used to integrate solution web applications with the unified shell of Adobe Experience Cloud.

### Interfaces

* [Modules](../interfaces/modules.md)
* [Runtime](../interfaces/runtime.md)

## Functions

### Init

▸ **init**(`bootstrap`: function): *void*

Initializes a solution web application by invoking the bootstrap callback once the runtime is ready.

1. If the module is already defined, begin bootstrap
2. Otherwise, define the global callback that will be called when runtime is ready

***Example:***

```typescript
import React from 'react';
import ReactDOM from 'react-dom';
import runtime, {init} from '@exc/runtime';

init(() => {
  ReactDOM.render(<MainComponent runtime={runtime()} />, document.querySelector('#main'));
});
```

**Parameters:**

▪ **bootstrap**: *function*

The callback used to bootstrap a solution. The runtime object is passed in as a parameter to this callback.

▸ (`runtime`: [Runtime](../interfaces/runtime.md)): *void*

**Parameters:**

| Name      | Type                                |
| --------- | ----------------------------------- |
| `runtime` | [Runtime](../interfaces/runtime.md) |

**Returns:** *void*

___

### runtime

▸ **runtime**(): *[Runtime](../interfaces/runtime.md)*

Get the runtime object, which contains all unified-shell APIs.

***Example:***

```typescript
import React from 'react';
import ReactDOM from 'react-dom';
import excApp from '@exc/runtime';

export class MyComponent extends React.Component {
  constructor(props) {
    this.runtime = excApp();
  }
}
```

**Returns:** *[Runtime](../interfaces/runtime.md)*

The runtime object.

## Next steps

Explore details of the [Page](page.md), [TopBar](topbar.md), and [User](user.md) external modules.

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
