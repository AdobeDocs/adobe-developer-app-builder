---
keywords:
  - cli
  - response headers
title: Setting Response Headers
description: How to set response headers for your static assets
---

# Setting Response Headers

For applications with UI, static assets such as HTML, Javascript, css, and images are served by the Content Content Delivery Network through the `adobeio-static.net` domain. App Builder has supported setting response headers for this static content since aio-cli version 9.3.0.

This feature applies to any App Builder applications with UI, and is helpful for use cases such as:

- Setting `CORS` Headers to enforce access policies
- Enabling `Content Security Policy` for assets
- Adding any `custom headers` required by the application UI

## Rules

App Builder Developers can now set response headers in the application manifest file - either `ext.config.yaml` or `app.config.yaml` - during application development.

The manifest lets headers be set as rules that can select or static assets or paths, or all static assets within the application.

The rules are:

| Rule                               | Description                                 |
| ---------------------------------- | ------------------------------------------- |
| /*                                 | Include all static content                  |
| /&lt;folder name&gt;/*             | Include all files within the named folder   |
| \*.html or \*.js etc.              | Include files based on their file extension |
| /file or /&lt;folder name&gt;/file | Include the named file                      |

Rules are applied in the order specified in the manifest. For example, rules placed after a given rule can override the previous rule if both apply to the same file.

## Example usage

```yaml
application:
  actions: actions
  web:
    src: web-src
    response-headers:
      /*: # add headers to all content
        X-custom-header: generic header
      /secure-dir/*: # specific folder
        Content-security-policy: default-src 'self'
      /widgets/*.html: # add headers to all html content
        X-custom-header: widget specific header
        Content-security-policy: default-src 'self'
      /lib/sample.js: # add headers to specific ile
        Content-security-policy: default-src 'self' example.com *.example.com
```

Once headers are added, they can be deployed with the app using the `aio app:deploy` command. Note that the paths specified in rules are relative to the distributable folder created after the application build, and not to the application root.

## Disallowed headers

Developers may set any HTTP or custom response headers except those in the list below. If the listed headers are specified in the manifest, they will be ignored and not included in the response.

Listed headers are ignored because they are either meant for internal use or are CDN-specific headers that should not be overridden.

1. accept-ranges
2. age
3. allow
4. alt-svc
5. cache-control
6. connection
7. content-length
8. content-type
9. content-disposition
10. content-encoding
11. content-language
12. content-length
13. date
14. etag
15. expires
16. last-modified
17. location
18. server
19. trailer
20. transfer-encoding
21. upgrade
22. x-cache

## Next steps

This concludes the Deployment section.

Return to [Deployment Overview](deployment.md).

Return to the [Guides Index](../../guides_index.md).
