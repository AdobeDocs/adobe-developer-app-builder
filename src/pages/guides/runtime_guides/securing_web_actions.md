# Securing Web Actions

By default, a web action can be invoked by anyone knowing the action's URL. If you want to restrict 'access, either use Basic Authentication or you build your own authentication layer.

## Basic authentication

Enable Basic Authentication for a web action:

```
// create a web action with Basic Authentication on
aio rt:action:create my-secure-web-action main.js --web true --web-secure this-is-the-secret-hash
```

or

```
// update an existing web action to enable Basic Authenticationn or change the secret
aio rt:action:update my-secure-web-action main.js --web true --web-secure this-is-the-secret-hash
```

Once  Basic Authentication is enabled, pass *X-Require-Wisk-Auth* header and the secret you set when invoking the web action. Assuming the web action is created in the default package, invoke it like this:

```
curl -X GET -H "X-Require-Whisk-Auth: <this-is-the-secret-hash>" https://[your-namespaces].adobeioruntime.net/api/v1/web/default/my-secure-web-action
```

If the authentication header is missing or the secret is wrong, you will get this error:

```
{
  "error": "Authentication is possible but has failed or not yet been provided.",
  "code": "OWGYkWwCUT7Ta6hWpfZWTQqRsfvcFTku"
}
```

## Oauth (using Adobe Identity Management System)

To enable IMS Authentication for a web action:

```
// create a web action with Require Validation on
aio rt:action:create my-requir-validation-web-action main.js --web true -a require-gw-validation true
```

or

```
// update an existing web action to enable Require Validation
aio rt:action:update my-require-validation-web-action main.js --web true -a require-gw-validation true
```

To interact with the action, set up a security configuration in your Swagger API route for that action. Detailed instructions on how to do this can be found in [Securing API Endpoints](creating_rest_apis.md#securing-api-endpoints).  

# Non-web actions

If your action is not a web action, you can still use your namespace credentials, base64 encoded, to call any of the actions in your namespace, like this:

```
curl -X POST -H "Authorization: Basic <base64-namepsace-auth>" https://[your-namespaces].adobeioruntime.net/api/v1/default/my-secure-action
```

## Cookies

We discourage using cookies directly from web actions on Runtime: since the infrastructure is shared, cookies can't be completely segregated between namespaces. The Runtime domain may be used for testing, but any cookie set directly by the domain should be considered compromised.

To alleviate this, set up a forwarding proxy with a correct and valid second-level domain, and send those requests to the actual Runtime domain. Inside your function, implement an approve list or shared secret to make sure people who attempt to access the functions directly are denied. Adobe also provides Container Native Applications, which can do much of this work for you.

For a fuller discussion of this topic you can read [Heroku's discussion of their cookie policy](https://devcenter.heroku.com/articles/cookies-and-herokuapp-com).

## Next steps

Review the [Security Guide](security_general.md).

Return to [Guides Index](../guides_index.md).
