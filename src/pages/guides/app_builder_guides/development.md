---
keywords:
  - Adobe I/O
  - Extensibility
  - API Documentation
  - Developer Tooling
---

# Development

## Localhost dev server

The `aio-cli` includes a development server for seamless local development, so you can run, develop, and test your applications on localhost. Traffic is securely served over HTTPS using a self-signed certificate. The server supports hot reloading, so you can instantly see the effects of any changes made to your application.

> Note: as is common with self-signed certificates, you may need to accept 'unsafe site' warnings explicitly, and manually trust the certificate in your browser settings.

## `aio app dev` vs. `aio app run`

These commands are very similar, but their differences give you different ways of working with your application. Both commands:

- Allow  browser debugging and run a local webserver to serve your front end on localhost

- Support browser hot reload on front-end code changes

When you use the command `aio app run`, the following events occur before the localhost webserver is started:

- Action code is transpiled, zipped, and deployed to Runtime
- Web action urls are written to `/src/dx-excshell-1/web-src/src/config.json`
- Front-end code is built and served on localhost
- Web action calls from your front-end are directed to Runtime
- Polling: action logs are output in the terminal
- Hot reload: changes to action code are re-built and re-deployed
- Activation records are kept, and may be queried for outputs, timing, logs, etc.
- Logs may be forwarded

When you use the command `aio app dev`:

- Web action urls are written to `/src/dx-excshell-1/web-src/src/config.json` in the form `https://localhost:PORT/api/v1/web/<package-name>/<action-name>`
- Front-end code is built and served on localhost
- Action calls from your front end are run in a debuggable local node process
- Action logs are output immediately to the terminal
- Changes to action code are loaded on the next call to the action; modules are not cached
- There are no activation records
- Logs cannot be forwarded

## State-lib and Files-lib usage

There are also differences in what your actions can do in these cases. The storage libraries have security restrictions that limit calls from outside Runtime actions, so if your action needs either state or files libs, it will fail with `aio app dev`.  Third-party APIs may have similar restrictions.

## Debugging

This section covers how to debug your App Builder application front-end and back-end code with full source maps support using VS Code Debugging or Chrome DevTools.

> Note: although any debugger that can attach to Node.js can technically be used to debug App Builder applications, some of the details will differ from the configurations covered here. For details on node debugging see the Node.js [Debugging Guide](https://nodejs.org/en/learn/getting-started/debugging)

### Debugging with VS code

Create a file named .vscode/launch.json at the root of your project with this content:

```json
{
  "version": "0.3.0",
  "configurations": [
    {
      "name": "App Builder: debug actions",
      "type": "node-terminal",
      "request": "launch",
      "command": "aio app dev",
      "skipFiles": [
        "<node_internals>/**/*.js",
        "${workspaceFolder}/node_modules/**/*.js"
      ]
    }, {
      "name": "App Builder: debug full stack",
      "type": "node-terminal",
      "request": "launch",
      "command": "aio app dev",
      "sourceMapPathOverrides": {
        "/__parcel_source_root/*": "${webRoot}/*"
      },
      "skipFiles": [
        "<node_internals>/**/*.js",
        "${workspaceFolder}/node_modules/**/*.js"
      ],
      "serverReadyAction": {
        "pattern": "server running on port : ([0-9]+)",
        "uriFormat": "https://localhost:%s",
        "action": "debugWithChrome",
        "webRoot": "${workspaceFolder}"
      }
    }
  ]
}
```

- Open the Debug panel (Ctrl+Shift+D on Windows/Linux; ⇧+⌘+D on MacOS)

> Note: The debug configuration is dynamic and allows you to set a port in the .env file, for example `PORT=3000`

Actions are loaded dynamically, so the code is not evaluated until an http request is made.  This means breakpoints in your action code will be grayed out until you trigger a call to the action. Web action urls are mapped to `https://localhost:PORT/api/v1/web/<package-name>/<action-name>` and can be called from your front end, opening the url in your browser, with cURL, etc.

You can use the step-through debugging features of VS Code, inspect variables, add watches, etc.

## More information

Learn more about using a JavaScript debugger

- [Chrome devtools](https://developer.chrome.com/docs/devtools/)
- [Node.js debugging in VS Code](https://code.visualstudio.com/docs/nodejs/nodejs-debugging)

## Next step

Return to [Guides Index](../guides_index.md).
