---
keywords:
  - Adobe I/O
  - Extensibility
  - API Documentation
  - Developer Tooling
title: 'Interface: UserAPI'
---

# Interface: UserAPI

## Hierarchy

* EventEmitter‹UserInfoEvent›
  
  ↳ **UserAPI**

## Properties

### logoutUrl

• **logoutUrl**: *string | undefined*

Optional. When specified, this URL will be invoked when the user logs out. This is useful to reduce the number of lingering sessions for solutions that have their own sessions server in addition to IMS.

## EventEmitter Methods

### emit

▸ **emit**<**K**>(`type`: K, `event?`: UserInfoEvent[K]): *void*

*Inherited from void*

Invoke all handlers for the given type.

**Type parameters:**

▪ **K**: *keyof UserInfoEvent*

**Parameters:**

| Name     | Type             | Description                                                              |
| -------- | ---------------- | ------------------------------------------------------------------------ |
| `type`   | K                | The event type to invoke.                                                |
| `event?` | UserInfoEvent[K] | Any value  passed to each handler - object is recommended, and powerful. |

**Returns:** *void*

___

### off

▸ **off**<**K**>(`type`: K, `handler`: function): *void*

*Inherited from void*

Remove an event handler for the given type.

**Type parameters:**

▪ **K**: *keyof UserInfoEvent*

**Parameters:**

▪ **type**: *K*

Type of event to unregister `handler` from.

▪ **handler**: *function*

Handler function to remove.

▸ (`event?`: UserInfoEvent[K]): *void*

**Parameters:**

| Name     | Type             |
| -------- | ---------------- |
| `event?` | UserInfoEvent[K] |

**Returns:** *void*

___

### on

▸ **on**<**K**>(`type`: K, `handler`: function): *void*

*Inherited from void*

Register an event handler for the given type.

**Type parameters:**

▪ **K**: *keyof UserInfoEvent*

**Parameters:**

▪ **type**: *K*

Type of event to listen for.

▪ **handler**: *function*

Function to call in response to given event.

▸ (`event?`: UserInfoEvent[K]): *void*

**Parameters:**

| Name     | Type             |
| -------- | ---------------- |
| `event?` | UserInfoEvent[K] |

**Returns:** *void*

___

## Other Methods

### authExpired

▸ **authExpired**(): *void*

API to notify the unified shell that APIs are returning 401 and the user needs to be re-authenticated.

**Returns:** *void*

___

### get

▸ **get**<**T**>(`type`: T): *Promise‹UserInfo[T]›*

Gets the specified type of information about an user.

**Type parameters:**

▪ **T**: *keyof UserInfo*

**Parameters:**

| Name   | Type | Description                     |
| ------ | ---- | ------------------------------- |
| `type` | T    | The type of information to get. |

**Returns:** *Promise‹UserInfo[T]›*

## Next steps

Explore details of the [Index](../modules/index.md), [Page](../modules/page.md), [TopBar](../modules/topbar.md), and [User](../modules/user.md) external modules.

Explore the attributes and behavior of Adobe Experience Cloud Interfaces:

[Modules](modules.md)

[Runtime](runtime.md)

| Page                                        | Topbar                                                     | User                     |
| ------------------------------------------- | ---------------------------------------------------------- | ------------------------ |
| [Object with Href](page.objectwithhref.md)  | [Callback](topbar.callback.md)                             | [Info](user.userinfo.md) |
| [Object with Path](page.objectwithpath.md)  | [Custom Feedback](topbar.customfeedbackconfig.md)          |                          |
| [API](page.pageapi.md)                      | [Custom Search](topbar.customsearchconfig.md)              |                          |
| [API Properties](page.pageapiproperties.md) | [Help Center Feedback](topbar.helpcenterfeedbackconfig.md) |                          |
|                                             | [External Feedback](topbar.externalfeedbackconfig.md)      |                          |
|                                             | [Solution](topbar.solution.md)                             |                          |
|                                             | [API](topbar.topbarapi.md)                                 |                          |
|                                             | [API Properties](topbar.topbarapiproperties.md)            | `                        |

Return to the [Guides Index](../../../guides_index.md).
