# Creating REST APIs

This tutorial shows how to create REST APIs from web actions deployed to Adobe I/O Runtime. It starts with the assumption that you've created and deployed four web actions to manage CRUD operations for the `pet` entity:

| CRUD Operation | Action Name |
| -------------- | ----------- |
| Create         | `addPet`    |
| Read           | `getPet`    |
| Update         | `updatePet` |
| Delete         | `deletePet` |

You can map these actions to a REST API for managing `pet` resources:

| Endpoint  | HTTP Method | Action Nam  |
| --------- | ----------- | ----------- |
| /pet      | POST        | `addPet`    |
| /pet      | GET         | `getPet`    |
| /pet/{id} | GET         | `getPet`    |
| /pet/{id} | PUT         | `updatePet` |
| /pet/{id} | DELETE      | `deletePet` |

 This is how to do it:

## Using aio CLI

Using the `aio rt:api:create` command, you create API endpoints one by one. The command allows you to set a base path, path, method, and response type, like this:

```
aio rt:api:create /pet-store /pet post createPet --response-type http
aio rt:api:create /pet-store /pet get getPet --response-type http
aio rt:api:create /pet-store /pet/{id} get getPet --response-type http
aio rt:api:create /pet-store /pet/{id} put updatePet --response-type http
aio rt:api:create /pet-store /pet/{id} delete deletePet --response-type http
```

See the API you defined with its fully qualified path by running this command:

```
aio rt:api:list /pet-store
```

> Note:  `aio rt:api:list` is not implemented; instead, please use `aio rt:api:list/enter-base-path`.

Here is an example of a call to one of the endpoints:

```
curl https://adobeioruntime.net:443/apis/<YOUR-NAMESPACE>/pet-store/pet/2345 -X get
```

or

```
curl https://<YOUR-NAMESPACE>.adobeioruntime.net:443/apis/pet-store/pet/2345 -X GET
```

In this example, the `{di}` value, 2335, will be mapped to a {payload.id}.

> **Note** that this URL differs from what `aio` returns. This is due to protections in Runtime that segregate namespaces from one another when invoking web actions. The `aio`-generated link will still work, but it will return a `308` redirect to your namespace's subdomain on Runtime. For a further discussion of this issue, please see [Securing Web Actions](securing_web_actions.md).

## Using Swagger files

REST APIs support  Swagger files, both for creating REST APIs from scratch or saving APIs created using the `aio` CLI as Swagger definition files you can use later to restore the API.

Continuing the example above, this command creates a Swagger definition file on your machine from the pet-store API:

```
aio rt:api:get /pet-store > pet-store-swagger.json
```

To restore or create the same API, for example in some other namespace,  run:

```
aio rt:api:create --config-file pet-store-swagger.json
```

This will work provided that the actions are already created in that namespace.

## Enable CORS on an HTTP resource

[CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS) headers can be controlled either statically or dynamically.

### Static CORS response with OpenAPI

If the returned CORS headers can be static, no code is necessary. The REST APIs can be configured in OpenAPI 2.0 format, by defining the `options` method. This code illustrates how to configure CORS headers:

```json
"paths": {
    "/test": {
      "options": {
        "responses": {
          "204": {
            "description": "Default CORS response",
            "headers": {
              "Access-Control-Allow-Origin": {
                "type": "string",
                "description": "Which origin can invoke the /test API\n",
                "default": "https://xyz.example.com"
              },
              "Access-Control-Allow-Methods": {
                "type": "string",
                "description": "Which methods are allowed\n",
                "default": "GET, POST, PUT"
              }
            }
          }
        }
      }
    }
}
```

Once the `options` block is added to any HTTP resource, the system will respond with the configured headers and their corresponding `default` values. In this example the response will be:

```
 HTTP/1.1 204 No Content
 Access-Control-Allow-Methods: GET, POST, PUT
 Access-Control-Allow-Origin: https://xyz.example.com
```

#### Dynamic CORS response with custom actions

CORS headers can also be returned by a dedicated function, which must be configured to handle the `options` HTTP Method. It works in the same way as other HTTP Methods like `GET` or `POST`.

This code snippet demonstrates an action that returns a CORS response:

```javascript
// save it as cors-action.js
function main(params) {
  return {
    statusCode: 204,
    headers: {
      "Access-Control-Allow-Origin": "https://xyz.example.com",
      "Access-Control-Allow-Methods": "GET, POST, PUT"
    }
  }
}
```

The web action must be created and configured for the CORS request:

```bash
aio rt:action:create handleCorsRequest ./cors-action.js --web true -a web-custom-options true

aio rt:api:create /pet-store /pet options handleCorsRequest --response-type http
```

To test the CORS request, get the URL of the action and invoke it:

```bash
aio rt:api:list /pet-store
# get the URL for the options action and invoke it
curl -i -X OPTIONS https://adobeioruntime.net/...
# it should return
 HTTP/1.1 204 No Content
 Access-Control-Allow-Methods: GET, POST, PUT
 Access-Control-Allow-Origin: https://xyz.example.com
```

## Securing API endpoints

### Oauth (using Adobe Identity Management System)

An action can be configured to require IMS validation for incoming requests using the following command: 

```bash
aio rt:action:create <action_name> --web true -a require-gw-validation true
```

#### Scopes validation

Once IMS authentication has been enabled for an action, the only way to allow access to it is by specifying a list of IMS scopes or client IDs permitted to invoke the action. 

The following code snippet demonstrates how to configure access using a standard Swagger file and the `security` object:

```json
{
    "basePath": "/v2",
    "paths": {
      "/ims-secure-endpoint": {
        "get": {
          "operationId": "your-namespaces/default/my-ims-secure-web-action.json",        
          "security": [
            {
              "scopes_auth": [
                  "write:pets",
                  "read:pets"
                ]
            }
          ]
        }
      }
    },
    "securityDefinitions": {
        "scopes_auth": {
          "type": "oauth2",
          "authorizationUrl": "",
          "flow": "implicit",
          "scopes": {
            "write:pets": "modify pets in your account",
            "read:pets": "read your pets"
          }
        }
    }
}
```

This establishes scope validation for the API endpoint, allowing requests with access tokens that have the scopes `write:pets` or `read:pets`. Requests without the required scopes in the access token will be rejected with this error message: 

```json
{
  "error_code":"401015",
  "message":"Scope mismatch"
}
```

After publishing the Swagger file, this endpoint `your-namespaces/default/my-require-gw-validation-web-action` can be used to call the action: 

```bash
curl -i -H "Authorization: Bearer <ims_access_token>" https://guest.adobeioruntime.net/api/v2/ims-validation-endpoint
```

#### Client ID validation

`client_id` validation can be established for an action by adding `x-client-ids` with a list of clients allowed to invoke it. The clientID list must be added to the `security definition` object in the Swagger. Be sure to add the security definition key to the method `security` object; otherwise the validation won't be enabled for that method: 

```json
{
    "basePath": "/v2",
    "paths": {
      "/ims-validation-endpoint": {
        "get": {
          "operationId": "your-namespaces/default/my-require-gw-validation-web-action.json",
          "security": [
            {
              "clientids_auth": []
            }
          ]
        }
      }
    },
    "securityDefinitions": {
        "clientids_auth": {
          "type": "oauth2",
          "authorizationUrl": "",
          "flow": "implicit",
          "scopes": {
            "write:pets": "modify pets in your account",
            "read:pets": "read your pets"
          },
          "x-client-ids": ["zookeeper", "dogwalker"]
        }
    }
}
```

This configuration allows the action to accept requests with access tokens that have the client IDs `zookeeper` OR `dogwalker`. Requests without the client ID in the access token will be rejected with this error message: 

```json
{
  "error_code":"403201",
  "message":"Client ID not allowed to call this service"
}
```

> Note that both `scope` and `client_id` validation can be enabled at the same time. If they are, the request will be rejected if the access token does not have both the required scope and client ID.
> 
> If no validation is enabled for the endpoint's verb, by removing the `security` object from the method definition, the action can be invoked publicly without any restrictions on the API URL.
> 
> By default, the IMS token validation URL will be used for token validation so "authorizationUrl" can be left empty. But if you want to use a different Oauth provider, you can specify the `authorizationUrl` in the `securityDefinition` object. Only one external security provider can be configured, and it must be defined in the first `securityDefinition` object in the Swagger file.
> 
> Please allow five minutes for these changes to take effect.

### Basic authentication

APIs are secured in the same way as web actions. You can read more about this in [Securing Web Actions](securing_web_actions.md).

Once basic authentication is enabled for an action, it's necessary to pass the `X-Require-Whisk-Auth` header and the secret you chose when making an API call.

### IP Allow-list / Disallow-list

Endpoints may also be configured to allow or block requests only from specific IP addresses. This can be done by adding the `x-ip-allowlist` or `x-ip-disallowlist` to the method definition as follows:

```json
{
    "basePath": "/v2",
    "paths": {
      "/ip-validation-endpoint": {
        "get": {
          "operationId": "your-namespaces/default/my-require-gw-validation-web-action.json",
          "x-ip-allowlist": ["192.150.10.210", "192.168.0.1"],
          "x-ip-disallowlist": ["192.150.10.10"]
        }
      }
    }
}
```

This configuration allows the action to accept requests from clients with the IP addresses "192.150.10.210" or "192.168.0.1", and block requests from "192.150.10.10". 

Requests that do not originate from the IP addresses on the whitelist will be rejected with this error message:

```json
{
  "error_code":"403013",
  "message":"Access from your IP address is not authorized"
}
```

Requests that originate from the IP addresses on the disallow list will be rejected with this error message:

```json
{
  "error_code":"403012",
  "message":"Access from your IP address is not authorized"
}
```

> Note: be sure the `my-require-gw-validation-web-action` is configured as a web action with `-a require-gw-validation true`, or the action can be accessed publicly with no restrictions on the non-API URL. 

# How long does it take to create or update an API?

It can take up to five minutes to see the changes from creation or update of an API.

## Next step

Return to [Guides Index](../guides_index.md).