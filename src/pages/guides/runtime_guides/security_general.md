# Security Guide

This guide reviews security issues to consider when working with Runtime functions. Only a subset of these may apply to your use case: for example, the section about cookies isn't relevant if you aren't using web actions. This guide will help you keep your functions secure and steer you away from practices that are risky in a serverless environment.

## Sandboxing

Every action is run in its own container. Containers may be reused for the same action and namespace, but never for different ones. Functions' memory and CPU usage are limited by the settings requested when the action is created. Actions have full access to the internet but no direct access to Runtime with the exception that chained functions are supported by sequences, which do not fully leave the Runtime cluster.

## Cross-Site scripting (XSS)

Anything that runs on the internet and accepts user input is potentially vulnerable to cross-site scripting (XSS) attacks. These take a variety of forms and can be easily introduced if you are not careful.

Parameters sent to actions are not sanitized by the Runtime system. These inputs should therefore be treated as unsafe and sanitized before they are used. For example, do not pass parameters directly to a SQL queries or evaluate them in JavaScript. A good resource on how to avoid XSS attacks is the [Open Web Application Security Project (OWASP) XSS documentation](https://www.owasp.org/index.php/Cross-site_Scripting_(XSS)).

## Cookies

You can set cookies in two ways in Runtime: from JavaScript on the page using [document.cookie](https://developer.mozilla.org/en-US/docs/Web/API/Document/cookie) calls, or by passing a [Header object from a web action](https://github.com/apache/incubator-openwhisk/blob/master/docs/webactions.md#web-actions) with a Set-Cookie header directive. The way Runtime hosts functions raises some particular concerns for Developers.

We discourage the use of cookies directly from web actions on Runtime: please see [Securing Web Actions](securing_web_actions.md) for details.

## Secrets

Secrets fall into two categories that must be handled differently.

### Runtime namespace details

Namespace credentials should be treated with the utmost care, and should never be shared with customers using the action. If a system's design requires namespace credentials to be stored inside an action or passed in as parameters to an action, please consider some other design. Leaking of namespace credentials gives an attacker full access to the namespace, and the namespace owner is responsible for any costs accrued because of such access.

To pass namespace credentials, use the `-a provide-api-key true` annotation on the function. This will place the namespace and key in the `__OW_NAMESPACE` and `__OW_API_KEY` execution context.

### Secrets of functions

If an action must communicate with an external service in an authenticated way, consider making use of a [hash-based message authentication code](https://en.wikipedia.org/wiki/HMAC) (HMAC) passed into the service to authorize it to retrieve those credentials temporarily. If passing a short-lived token to the action to retrieve the credentials is not possible, any long-lived credentials should be passed as part of a header or document POST-ed to the action. This will ensure that the credentials are passed over a secure channel and not leaked to any internal or external routing mechanism.

If you need to store secrets your functions use, place them in some other trusted storage system, or use the default params mechanism that is supported by packages and actions. To support this use case, all default parameters are automatically encrypted, and decrypted just before the action code is executed. Thus, the only time you have access to the decrypted value is while executing the action code.

If you run the `CLI` command for getting an action or package, you will get a listing of the names of the default parameters; the values will be listed as hashes.

## Authentication and authorization

When invoking functions on the command line, your namespace and key are authenticated with Runtime. We determine whether or not you are authorized to perform the requested action; if you are, it proceeds. 

### Adobe IMS authentication

Actions should include the [aio-lib-ims](https://github.com/adobe/aio-lib-ims) library to authenticate with Adobe IMS. Alternatively, you could use [App Builder](../app_builder_guides/security/index.md#authentication-and-authorization-handling) to authenticate your actions against Adobe IMS. 

### Securing web actions

For web actions, see [Securing Web Actions](securing_web_actions.md).

## Transport security

Due to the shared nature of any serverless system, it is necessary to review the security of transport to your function and how it communicates with the world.

All communications to the Runtime cluster are secured by HTTPS. Communications from your functions to any other service should also use HTTPS or some other secure channel if possible. There is a network partition, so no function should be able to see or route traffic directly to another, but it is always best to secure communication channels as well.

## Allowed egress ports

Actions can call any external IP, however only the following ports are allowed:

`21, 22, 25, 53, 80, 123, 143, [200-299], 389, 443, 445, 465, 587, 636, 1433, [2000-2999], 3000, 3306, 4242, 4317, 4343, 5400, 5432, 5671, 5672, 6061, 6062, 6379, 6380, 6651, 8000, 8020, 8080, 8085, 8088, 8089, 8300, 8500, 8600, 9090, 9092, 9093, 9094, [9096-9352], 9354, [10000-20000], 27016, 27017, 27018, 27019, 30303, 50010, 60020`

## Using a Content Delivery Network (CDN)

Using a CDN in front of web actions improves security. Many CDNs offer built-in security features such as DDoS protection and web application firewall (WAF) that can protect web actions against DDoS attacks and other threats.

You can secure web actions using any CDN by following these steps: 

1. Choose a provider that meets your needs and sign up for their service. [Wikipedia](https://en.wikipedia.org/wiki/Content_delivery_network#notable_content_delivery_service_providers) provides a list of alternatives.
2. Configure the CDN and point it to Runtime’s domain name, `<your-namespace>.adobeioruntime.net`. This can usually be done through the CDN provider's web interface or API.
3. Configure your action to use a security header as described in [Securing Web Actions](securing_web_actions.md). 
4. Configure the CDN to add the `X-Require-Whisk-Auth` security header, with the secret hash value, for all requests made to the secured web actions.

## Secure communication with back-end services

For security reasons, Runtime does not expose egress IPs . Customers who need a way to  communicate securely with their back-end services can use a proxy between their system and Runtime, as described in [Configuring a Secure Proxy](reference_docs/configuringproxy.md).

Runtime ingress IPs are not static. To facilitate operational changes,  IPs returned when looking up I/O Runtime endpoints may change. To ensure uninterrupted service availability, it is critical that clients honor the Time to Live (TTL) returned by I/O Runtime DNS records. When the IPs associated with these endpoints change due to operational adjustments, clients who rely on the outdated addresses may experience service disruptions, increased latency, or network connectivity issues. How clients honoring TTL is implementation-specific, but in general, clients should not cache DNS records for longer than the specified TTL.

## Next steps

Proceed to [Securing Web Actions](securing_web_actions.md).

Return to [Guides Index](../guides_index.md).
