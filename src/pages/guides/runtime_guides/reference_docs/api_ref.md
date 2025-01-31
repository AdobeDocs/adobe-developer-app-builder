# Adobe I/O Runtime API Reference

## API endpoints

Adobe I/O Runtime supports the following API endpoints for interacting programmatically with the service. 

**Notes:**

1. Unless otherwise noted, all parameters are required. 
2. For all the API calls on this page, the base URL is:  
   `https://api.adobe.io/`

### GET /runtime/admin/namespaces/{orgId}/{intId}

Returns the details of the namespace associated with the specified organization and integration.

#### _Parameters:_

| Name                                 | Description                                     |
| ------------------------------------ | ----------------------------------------------- |
| `orgId` (`string`: _path_)           | Organization ID                                 |
| `intId` (`string`: _path_)           | Integration ID                                  |
| `Authorization` (`string`: _header_) | Authorization token in format: `Bearer {token}` |
| `X-Api-Key` (`string`: _header_)     | Api key                                         |

#### _Responses:_

Response content type: `application/json`

| Code | Description                        |
| ---- | ---------------------------------- |
| 200  | Successful operationExample value: |
| {    |                                    |

    "name": "string",
    "auth": "string"

}
Model:
NamespaceDTO {
description: Namespace Details
    name    string
            Namespace name
    auth    string
            Auth associated with Namespace
}|

### POST /runtime/admin/namespaces/{orgId}/{intId}

Creates a new namespace and returns the details of the newly created namespace. If namespace already exists it returns the details of the namespace.

#### _Parameters:_

| Name                                 | Description                                     |
| ------------------------------------ | ----------------------------------------------- |
| `orgId` (`string`: _path_)           | Organization ID                                 |
| `intId` (`string`: _path_)           | Integration ID                                  |
| `Authorization` (`string`: _header_) | Authorization token in format: `Bearer {token}` |
| `X-Api-Key` (`string`: _header_)     | Api key                                         |

#### _Responses:_

Response content type: `application/json`
|Code|Description|
|--- |--- |
|200|Successful operation Example value:
{
"name": "string",
"auth": "string"
}
Model:
NamespaceDTO {
description: Namespace Details
    name    string
            Namespace name
    auth    string
            Auth associated with Namespace
}|

### DELETE /runtime/admin/namespaces/{orgId}/{intId}

Deletes the namespace associated with the specified organization and integration.

#### _Parameters:_

| Name                                 | Description                                     |
| ------------------------------------ | ----------------------------------------------- |
| `orgId` (`string`: _path_)           | Organization ID                                 |
| `intId` (`string`: _path_)           | Integration ID                                  |
| `Authorization` (`string`: _header_) | Authorization token in format: `Bearer {token}` |
| `X-Api-Key` (`string`: _header_)     | Api key                                         |

#### _Responses:_

Response content type: `application/json`
| Code | Description |
|---|---|
| _default_ | Successful operation |

### GET /runtime/namespaces/{orgId}/{intId}/actions

Returns the list of actions associated with the specified organization and integration.

#### _Parameters:_

| Name                                 | Description                                     |
| ------------------------------------ | ----------------------------------------------- |
| `orgId` (`string`: _path_)           | Organization ID                                 |
| `intId` (`string`: _path_)           | Integration ID                                  |
| `Authorization` (`string`: _header_) | Authorization token in format: `Bearer {token}` |
| `X-Api-Key` (`string`: _header_)     | Api key                                         |

#### _Responses:_

Response content type: `application/json`
|Code|Description|
|--- |--- |
|200|Successful operation Example value:
[
  {
    "name": "string",
    "code": "string",
    "namespace": "string",
    "version": "string",
    "params": [
      {
        "key": "string",
        "value": {}
      }
    ],
    "annotations": [
      {
        "key": "string",
        "value": {}
      }
    ],
    "limits": {
      "timeout": "string",
      "memory": "string",
      "logs": "string"
    },
    "exec": {
      "kind": "string",
      "binary": false,
      "components": [
        "string"
      ]
    },
    "url": "string"
  }
]
Model: 
[ActionDTO {
description: OpenWhisk Action
name        string
            Action name
code        string
            Action code
namespace   string
            Action namespace
version     string
            Action version
params      [Action params
            KeyValuePairDTO {
            description: OpenWhisk Action param
            key     string
                    Param Name
            value   {
                        description: Param value
                    }
            }]
annotations [Action annotations
            KeyValuePairDTO {
            description: OpenWhisk Action param
            key     string
                    Param Name
            value   {
                        description: Param value
                    }
            }]
limits      LimitsDTO {
            description: OpenWhisk Action Limits
            timeout     string
                        Action timeout
            memory      string
                        Action memory limit
            logs        string
                        Action logs
            }
exec        ExecDTO {
            description: OpenWhisk Action exec details
            kind    string
            Action kind
            binary  boolean
                    default: false
                    Is action binary
            components  [
                        Action components in case of sequence
                        string]
            }
url         string
            Action url
}]|

### POST /runtime/namespaces/{orgId}/{intId}/actions

Creates a new action.

#### _Parameters:_

| Name                   | Description                  |
| ---------------------- | ---------------------------- |
| orgId (string: path)   | Organization ID              |
| intId (string: path)   | Integration ID               |
| body (body)            | Action form.  Example value: |
| {                      |                              |
| "name": "string",      |                              |
| "code": "string",      |                              |
| "namespace": "string", |                              |
| "version": "string",   |                              |
| "params": [            |                              |

    {
      "key": "string",
      "value": {}
    }

  ],
  "annotations": [
    {
      "key": "string",
      "value": {}
    }
  ],
  "limits": {
    "timeout": "string",
    "memory": "string",
    "logs": "string"
  },
  "exec": {
    "kind": "string",
    "binary": false,
    "components": [
      "string"
    ]
  },
  "url": "string"
}Parameter content type: application/json
Model: 
[ActionDTO {
description: OpenWhisk Action

name        string
            Action name
code        string
            Action code
namespace   string
            Action namespace
version     string
            Action version
params      [Action params
            KeyValuePairDTO {
            description: OpenWhisk Action param
            key     string
                    Param Name
            value   {
                        description: Param value
                    }
            }]
annotations [Action annotations
            KeyValuePairDTO {
            description: OpenWhisk Action param
            key     string
                    Param Name
            value   {
                        description: Param value
                    }
            }]
limits      LimitsDTO {
            description: OpenWhisk Action Limits
            timeout     string
                        Action timeout
            memory      string
                        Action memory limit
            logs        string
                        Action logs
            }
exec        ExecDTO {
            description: OpenWhisk Action exec details
            kind    string
            Action kind
            binary  boolean
                    default: false
                    Is action binary
            components  [
                        Action components in case of sequence
                        string]
            }
url         string
            Action url
}]|
|Authorization (string: header)|Authorization token in format: Bearer {token}|
|X-Api-Key (string: header)|Api key|

#### _Responses:_

Response content type: `application/json`
| Code | Description |
|---|---|
| _default_ | Successful operation |

### GET /runtime/namespaces/{orgId}/{intId}/actions/{name}

Returns the details of an action.

#### _Parameters:_

| Name                                 | Description                                     |
| ------------------------------------ | ----------------------------------------------- |
| `orgId` (`string`: _path_)           | Organization ID                                 |
| `intId` (`string`: _path_)           | Integration ID                                  |
| `name` (`string`: _path_)            | Action name                                     |
| `Authorization` (`string`: _header_) | Authorization token in format: `Bearer {token}` |
| `X-Api-Key` (`string`: _header_)     | Api key                                         |

#### _Responses:_

Response content type: `application/json`
|Code|Description|
|--- |--- |
|200|Successful operation Example value:
{
  "name": "string",
  "namespace": "string",
  "activationId": "string",
  "annotations": [
    {
      "key": "string",
      "value": {}
    }
  ],
  "duration": 0,
  "version": "string",
  "response": {}
}
Model: 
[ActionDTO {
description: OpenWhisk Action
name        string
            Action name
code        string
            Action code
namespace   string
            Action namespace
version     string
            Action version
params      [Action params
            KeyValuePairDTO {
            description: OpenWhisk Action param
            key     string
                    Param Name
            value   {
                        description: Param value
                    }
            }]
annotations [Action annotations
            KeyValuePairDTO {
            description: OpenWhisk Action param
            key     string
                    Param Name
            value   {
                        description: Param value
                    }
            }]
limits      LimitsDTO {
            description: OpenWhisk Action Limits
            timeout     string
                        Action timeout
            memory      string
                        Action memory limit
            logs        string
                        Action logs
            }
exec        ExecDTO {
            description: OpenWhisk Action exec details
            kind    string
            Action kind
            binary  boolean
                    default: false
                    Is action binary
            components  [
                        Action components in case of sequence
                        string]
            }
url         string
            Action url
}]|

### POST /runtime/namespaces/{orgId}/{intId}/actions/{name}

Executes an action.

#### _Parameters:_

| Name                                 | Description                                     |
| ------------------------------------ | ----------------------------------------------- |
| `orgId` (`string`: _path_)           | Organization ID                                 |
| `intId` (`string`: _path_)           | Integration ID                                  |
| `name` (`string`: _path_)            | Action name                                     |
| `Authorization` (`string`: _header_) | Authorization token in format: `Bearer {token}` |
| `X-Api-Key` (`string`: _header_)     | Api key                                         |

#### _Responses:_

Response content type: `application/json`
|Code|Description|
|--- |--- |
|200|Successful operation Example value:
{
  "name": "string",
  "code": "string",
  "namespace": "string",
  "version": "string",
  "params": [
    {
      "key": "string",
      "value": {}
    }
  ],
  "annotations": [
    {
      "key": "string",
      "value": {}
    }
  ],
  "limits": {
    "timeout": "string",
    "memory": "string",
    "logs": "string"
  },
  "exec": {
    "kind": "string",
    "binary": false,
    "components": [
      "string"
    ]
  },
  "url": "string"
}
Model: 
[ActionResultDTO {
description: OpenWhisk Action invocation result
name        string
            Action name
code        string
            Action code
namespace   string
            Action namespace
version     string
            Action version
annotations [Action annotations
            KeyValuePairDTO {
            description: OpenWhisk Action param
            key     string
                    Param Name
            value   {
                        description: Param value
                    }
            }]
duration    integer($int32)
            Duration
version     string
            Action Version
response    {
            description:    
            Action invocation response
}|

### PUT /runtime/namespaces/{orgId}/{intId}/actions/{name}

Updates an action.

#### _Parameters:_

| Name                   | Description                  |
| ---------------------- | ---------------------------- |
| orgId (string: path)   | Organization ID              |
| intId (string: path)   | Integration ID               |
| name (string: path)    | Action name                  |
| body (body)            | Action form.  Example value: |
| {                      |                              |
| "name": "string",      |                              |
| "code": "string",      |                              |
| "namespace": "string", |                              |
| "version": "string",   |                              |
| "params": [            |                              |

    {
      "key": "string",
      "value": {}
    }

  ],
  "annotations": [
    {
      "key": "string",
      "value": {}
    }
  ],
  "limits": {
    "timeout": "string",
    "memory": "string",
    "logs": "string"
  },
  "exec": {
    "kind": "string",
    "binary": false,
    "components": [
      "string"
    ]
  },
  "url": "string"
}Parameter content type: application/json
Model: 
[ActionDTO {
description: OpenWhisk Action
name        string
            Action name
code        string
            Action code
namespace   string
            Action namespace
version     string
            Action version
params      [Action params
            KeyValuePairDTO {
            description: OpenWhisk Action param
            key     string
                    Param Name
            value   {
                        description: Param value
                    }
            }]
annotations [Action annotations
            KeyValuePairDTO {
            description: OpenWhisk Action param
            key     string
                    Param Name
            value   {
                        description: Param value
                    }
            }]
limits      LimitsDTO {
            description: OpenWhisk Action Limits
            timeout     string
                        Action timeout
            memory      string
                        Action memory limit
            logs        string
                        Action logs
            }
exec        ExecDTO {
            description: OpenWhisk Action exec details
            kind    string
            Action kind
            binary  boolean
                    default: false
                    Is action binary
            components  [
                        Action components in case of sequence
                        string]
            }
url         string
            Action url
}]|
|Authorization (string: header)|Authorization token in format: Bearer {token}|
|X-Api-Key (string: header)|Api key|

#### _Responses:_

Response content type: `application/json`
| Code | Description |
|---|---|
| _default_ | Successful operation |

### DELETE /runtime/namespaces/{orgId}/{intId}/actions/{name}

Deletes an action.

#### _Parameters:_

| Name                                 | Description                                     |
| ------------------------------------ | ----------------------------------------------- |
| `orgId` (`string`: _path_)           | Organization ID                                 |
| `intId` (`string`: _path_)           | Integration ID                                  |
| `name` (`string`: _path_)            | Action name                                     |
| `Authorization` (`string`: _header_) | Authorization token in format: `Bearer {token}` |
| `X-Api-Key` (`string`: _header_)     | Api key                                         |

#### _Responses:_

Response content type: `application/json`
| Code | Description |
|---|---|
| _default_ | Successful operation |

### GET /runtime/system/actions

Returns a list of built-in actions.

#### _Parameters:_

| Name                                 | Description                                     |
| ------------------------------------ | ----------------------------------------------- |
| `Authorization` (`string`: _header_) | Authorization token in format: `Bearer {token}` |
| `X-Api-Key` (`string`: _header_)     | Api key                                         |

#### _Responses:_

Response content type: `application/json`
|Code|Description|
|--- |--- |
|200|Successful operationExample value:
[
  {
    "name": "string",
    "code": "string",
    "namespace": "string",
    "version": "string",
    "params": [
      {
        "key": "string",
        "value": {}
      }
    ],
    "annotations": [
      {
        "key": "string",
        "value": {}
      }
    ],
    "limits": {
      "timeout": "string",
      "memory": "string",
      "logs": "string"
    },
    "exec": {
      "kind": "string",
      "binary": false,
      "components": [
        "string"
      ]
    },
    "url": "string"
  }
]
Model: 
[ActionDTO {
description: OpenWhisk Action
name        string
            Action name
code        string
            Action code
namespace   string
            Action namespace
version     string
            Action version
params      [Action params
            KeyValuePairDTO {
            description: OpenWhisk Action param
            key     string
                    Param Name
            value   {
                        description: Param value
                    }
            }]
annotations [Action annotations
            KeyValuePairDTO {
            description: OpenWhisk Action param
            key     string
                    Param Name
            value   {
                        description: Param value
                    }
            }]
limits      LimitsDTO {
            description: OpenWhisk Action Limits
            timeout     string
                        Action timeout
            memory      string
                        Action memory limit
            logs        string
                        Action logs
            }
exec        ExecDTO {
            description: OpenWhisk Action exec details
            kind    string
            Action kind
            binary  boolean
                    default: false
                    Is action binary
            components  [
                        Action components in case of sequence
                        string]
            }
url         string
            Action url
}]|

### POST /runtime/namespaces/{orgId}/{intId}/handleEventRegistration

Registers an event registration and assigns a given action to the event.

#### _Parameters:_

| Name                            | Description     |
| ------------------------------- | --------------- |
| orgId (string: path)            | Organization ID |
| intId (string: path)            | Integration ID  |
| body (body)                     | Example value:  |
| {                               |                 |
| "id": "string",                 |                 |
| "name": "string",               |                 |
| "description": "string",        |                 |
| "client_id": "string",          |                 |
| "type": "string",               |                 |
| "integration_status": "string", |                 |
| "delivery_type": "string",      |                 |
| "webhook_url": "string",        |                 |
| "events_of_interest": [         |                 |

    {
      "event_code": "string",
      "provider": "string"
    }

  ],
  "runtime_action": "string",
  "registration_id": "string"
}Parameter content type: application/json
Model: 
EventDTO {
description: Adobe I/O Event Details
id                  string
                    Event id
name                string
                    Event name
description         string
                    Event code
Client id           string
                    Event namespace
type                string
                    Event type
integration_status  string
                    Event integration status
delivery_type       string
                    Event delivery type
webhook_url         string
                    Webhook url
events_of_interest  [
                    Events of interest to listen to
                    EventsOfInterestDTO{
                    description: Events of interest
                        event_code  string
                                    Event code
                        provider    string
                                    Event provider
                    }]
runtime_action      string
                    Action to handle event
registration_id     string
                    Event registration id
}|
|Authorization (string: header)|Authorization token in format: Bearer {token}|
|X-Ams-Consumer-Id (string: header)|AMS consumer ID|
|X-Ams-Application-Id (string: header)|AMS application ID|
|X-Api-Key (string: header)|Api key|

#### _Responses:_

Response content type: `application/json`
|Code|Description|
|--- |--- |
|200|Successful operation Example value:
{
  "id": "string",
  "name": "string",
  "description": "string",
  "client_id": "string",
  "type": "string",
  "integration_status": "string",
  "delivery_type": "string",
  "webhook_url": "string",
  "events_of_interest": [
    {
      "event_code": "string",
      "provider": "string"
    }
  ],
  "runtime_action": "string",
  "registration_id": "string"
}
Model: 
EventDTO {
description: Adobe I/O Event Details
id                  string
                    Event id
name                string
                    Event name
description         string
                    Event code
Client id           string
                    Event namespace
type                string
                    Event type
integration_status  string
                    Event integration status
delivery_type       string
                    Event delivery type
webhook_url         string
                    Webhook url
events_of_interest  [
                    Events of interest to listen to
                    EventsOfInterestDTO{
                    description: Events of interest
                        event_code  string
                                    Event code
                        provider    string
                                    Event provider
                    }]
runtime_action      string
                    Action to handle event
registration_id     string
                    Event registration id
}|

### DELETE /runtime/namespaces/{orgId}/{intId}/handleEventDeletion/{clientId}/{registrationId}

Deletes an event registration.

#### _Parameters:_

| Name                                      | Description                                     |
| ----------------------------------------- | ----------------------------------------------- |
| `orgId` (`string`: _path_)                | Organization ID                                 |
| `intId` (`string`: _path_)                | Integration ID                                  |
| `clientId ` (`string`: _path_)            | IMS client ID                                   |
| `registrationId` (`string`: _path_)       | ID of registration                              |
| `X-Ams-Consumer-Id` (`string`: _path_)    | AMS consumer ID                                 |
| `X-Ams-Application-Id` (`string`: _path_) | AMS application ID                              |
| `Authorization` (`string`: _header_)      | Authorization token in format: `Bearer {token}` |
| `X-Api-Key` (`string`: _header_)          | Api key                                         |

#### _Responses:_

Response content type: `application/json`
| Code | Description |
|---|---|
| _default_ | Successful operation |

### PUT /runtime/namespaces/{orgId}/{intId}/handleEventUpdate/{clientId}/{registrationId}

Updates an event registration.

#### _Parameters:_

| Name                                  | Description        |
| ------------------------------------- | ------------------ |
| orgId (string: path)                  | Organization ID    |
| intId (string: path)                  | Integration ID     |
| clientId (string: path)               | IMS client ID      |
| registrationId (string: path)         | Registration ID    |
| X-Ams-Consumer-Id (string: header)    | AMS consumer ID    |
| X-Ams-Application-Id (string: header) | AMS application ID |
| body (body)                           | Example value:     |
| {                                     |                    |
| "id": "string",                       |                    |
| "name": "string",                     |                    |
| "description": "string",              |                    |
| "client_id": "string",                |                    |
| "type": "string",                     |                    |
| "integration_status": "string",       |                    |
| "delivery_type": "string",            |                    |
| "webhook_url": "string",              |                    |
| "events_of_interest": [               |                    |

    {
      "event_code": "string",
      "provider": "string"
    }

  ],
  "runtime_action": "string",
  "registration_id": "string"
}Parameter content type: application/json
Model: 
EventDTO {
description: Adobe I/O Event Details
id                  string
                    Event id
name                string
                    Event name
description         string
                    Event code
Client id           string
                    Event namespace
type                string
                    Event type
integration_status  string
                    Event integration status
delivery_type       string
                    Event delivery type
webhook_url         string
                    Webhook url
events_of_interest  [
                    Events of interest to listen to
                    EventsOfInterestDTO{
                    description: Events of interest
                        event_code  string
                                    Event code
                        provider    string
                                    Event provider
                    }]
runtime_action      string
                    Action to handle event
registration_id     string
                    Event registration id
}|
|Authorization (string: header)|Authorization token in format: Bearer {token}|
|X-Api-Key (string: header)|Api key|

#### _Responses:_

Response content type: `application/json`
|Code|Description|
|--- |--- |
|200|Successful operation Example value:
{
  "id": "string",
  "name": "string",
  "description": "string",
  "client_id": "string",
  "type": "string",
  "integration_status": "string",
  "delivery_type": "string",
  "webhook_url": "string",
  "events_of_interest": [
    {
      "event_code": "string",
      "provider": "string"
    }
  ],
  "runtime_action": "string",
  "registration_id": "string"
}
Model: 
EventDTO {
description: Adobe I/O Event Details
id                  string
                    Event id
name                string
                    Event name
description         string
                    Event code
Client id           string
                    Event namespace
type                string
                    Event type
integration_status  string
                    Event integration status
delivery_type       string
                    Event delivery type
webhook_url         string
                    Webhook url
events_of_interest  [
                    Events of interest to listen to
                    EventsOfInterestDTO{
                    description: Events of interest
                        event_code  string
                                    Event code
                        provider    string
                                    Event provider
                    }]
runtime_action      string
                    Action to handle event
registration_id     string
                    Event registration id
}|

### POST /runtime/namespaces/{orgId}/{intId}/handleEventStatus/{clientId}/{registrationId}/{status}

Updates the status of an event registration.

#### _Parameters:_

| Name                                      | Description                                     |
| ----------------------------------------- | ----------------------------------------------- |
| `orgId` (`string`: _path_)                | Organization ID                                 |
| `intId` (`string`: _path_)                | Integration ID                                  |
| `clientId ` (`string`: _path_)            | IMS client ID                                   |
| `registrationId` (`string`: _path_)       | ID of registration                              |
| `status` (`string`: _path_)               | Status of the registration                      |
| `X-Ams-Consumer-Id` (`string`: _path_)    | AMS consumer ID                                 |
| `X-Ams-Application-Id` (`string`: _path_) | AMS application ID                              |
| `Authorization` (`string`: _header_)      | Authorization token in format: `Bearer {token}` |
| `X-Api-Key` (`string`: _header_)          | Api key                                         |

#### _Responses:_

Response content type: `application/json`
|Code|Description|
|--- |--- |
|200|Successful operation Example value:
{
  "id": "string",
  "name": "string",
  "description": "string",
  "client_id": "string",
  "type": "string",
  "integration_status": "string",
  "delivery_type": "string",
  "webhook_url": "string",
  "events_of_interest": [
    {
      "event_code": "string",
      "provider": "string"
    }
  ],
  "runtime_action": "string",
  "registration_id": "string"
}
Model: 
EventDTO {
description: Adobe I/O Event Details
id                  string
                    Event id
name                string
                    Event name
description         string
                    Event code
Client id           string
                    Event namespace
type                string
                    Event type
integration_status  string
                    Event integration status
delivery_type       string
                    Event delivery type
webhook_url         string
                    Webhook url
events_of_interest  [
                    Events of interest to listen to
                    EventsOfInterestDTO{
                    description: Events of interest
                        event_code  string
                                    Event code
                        provider    string
                                    Event provider
                    }]
runtime_action      string
                    Action to handle event
registration_id     string
                    Event registration id
}|

## Next steps

Return to the [Runtime Reference Documentation Index](index.md).

Return to the [Guides Index](../../guides_index.md).
