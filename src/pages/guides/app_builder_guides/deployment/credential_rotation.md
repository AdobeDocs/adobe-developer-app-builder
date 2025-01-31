---
keywords:
  - cli
  - credentials
  - Developer Tooling
  - secrets
  - rotation
title: Credential Rotation
description: How to rotate your secrets and keep your actions secured.
---

# Credential Rotation

Eventually you will need to rotate your credentials to keep everything secure.  This page outlines best practices for updating parameters (inputs) to your actions without disruption.

## Why rotate credentials?

If your credentials may have been leaked, for example by appearing in a errant log or because a Developer left the team, it is good practice to rotate them.

## How to rotate credentials

Use the `aio runtime action update` command to update inputs for an action:

```bash
➜ aio runtime action update <action-name> --param <param-name> <param-value>
# here's a concrete version
➜ aio rt action update dx-excshell-1/generic -p=DEBUG silly

# you can specify multiple values in several ways

# firstly, just a list of key value pairs
➜ aio runtime action update <action-name> --param <param-name> <param-value> <param-name> <param-value> <param-name> <param-value>

# secondly, you can use the --param flag more than once .. here we use the shortened version -p
➜ aio rt action update <action-name> -p <param-name> <param-value> -p <param-name> <param-value> -p <param-name> <param-value>
```

<InlineAlert slots="text" />

It is critically important to pass all parameters in a single `update` call.  Any parameters that are not specified will disappear.

## Using a JSON parameters file

You can also put your input values in a json file instead of typing them all into the command line.  This applies to both the `aio runtime action update` and the `aio runtime package update`:

```bash
# actionInputs.json
{
  "actionCredential": "value from actionInputs.json"
}

➜ aio runtime action update <action-name> --param-file=<value>
# concrete example
➜ aio runtime action update dx-excshell-1/generic --param-file=actionInputs.json
```

<InlineAlert slots="text" />

Again, it is critically important to pass all parameters in a single `update` call. Any parameters that are not specified will disappear.

## Next steps

Continue to [Setting Response Headers](setting_response_headers.md).

Return to [Deployment Overview](deployment.md).

Return to the [Guides Index](../../guides_index.md).
