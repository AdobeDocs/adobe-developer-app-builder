# Creating Actions

To execute as an action on Adobe I/O Runtime, the function you code must both :

- Either be named *main* or have an entry point exported as *main* - the function that will be executed when invoked
- Accept valid JSON objects as input, and output valid JSON objects

To create and invoke actions, you must also configure the `aio` CLI on your machine: refer to the [aio CLI](tools/cli_install.md) page for installation and configuration instructions. 

Let's assume you have this function available on your machine:

```javascript
// this is saved in a file named first-function.js
function main(params) {
    var nm = params.name || 'stranger';
    return {payload: 'Hello ' + nm};
}

exports.main = main;
```

You can now create an action called `test` using this command:

```
aio rt:action:create test first-function.js
```

You may update it at any time using this command:

```
aio rt:action:update test first-function.js
```

When you no longer need an action, you can delete it:

```
aio rt:action:delete test
```

To save an action deployed to your machine, use this command:

```
aio rt:action:get test --save 
```

To list all the actions available in your current namespace, run this command:

```
aio rt:action:list
```

## Invoking actions

Once you have an action, for example `test`, call it using this command:

```
aio rt:action:invoke test --result
```

Note the `--result` flag; it outputs the result of the invocation. Without it, the invocation would return the activation ID instead of a result. In that case, you could use the activation ID to retrieve the result:

```
aio rt:activation:get <activation ID>
```

When you invoke an action this way, the invocation is not blocking - in other words, it is asynchronous. To execute it in a blocking style and therefore get an activation record instead of just an ID,  add the `--blocking` flag to the command:

```
aio rt:action:invoke test --blocking
```

## Working with parameters

Actions can receive parameters while they are being executed. Parameters sent to an action are available using the `params` variable. Parameters called *first-name* and *last-name*  will be available as *params.first-name* and *params.last-name*.

To invoke an action with parameters, set a parameter like `name` in our function sample above, in this way:

```
aio rt:action:invoke test --param name "John Doe" --result
```

## Setting default parameters

To bind the same parameter values for all invocations or set default values, you have two options: set them at the package level so all actions in the package inherit them, or set them at the action level.

### Default params and encryption

Developers often use the default parameters as a way to provision actions with the secrets and passwords they need to authenticate against databases, services, APIs, or other systems.

To support this use case, default parameters are automatically encrypted, and decrypted just before the action code is executed. The decrypted value is therefore accessible only while the action code executes.

The CLI command to get an action or package will return a listing of the default parameter names; the values will be listed as a hashes of the actual values.

### Set default parameters on action

Assume you want the default value of your parameter to be "Runtime". You can set it when creating the action or updating an existing action. In either case, add the `--param` flag:

```
// creation time
aio rt:action:create test first-function.js --param name "Runtime"

// update
aio rt:action:update test first-function.js --param name "Runtime"
```

When you run this action without any parameters,  it will use the default values. Parameter set during invocation will overwrite the defaults.

> Note: When updating an action's params using `--param <key> <value>` as above, you must specify all of the parameters, because all previous values will be overwritten.

### Set default parameters on package

Actions are always created in a package. If you don't specify a package, the default package is used. In the same way you set default parameters at the action level, you can specify them at the package level. Parameters set at the package level will be used for all  actions created in that package.

```
// creation time
aio rt:package:create my-package --param name "Runtime"

// update
aio rt:package:update my-package --param name "Runtime"
```

In case of conflict, the precedence rules are: parameters set at invocation time override those set at the action level, which in turn override those set at the package level.

### Use parameter files to set default parameters

You can use a dedicated file to store default parameter values, and then use the file to set them. This is useful when you need to set multiple parameter values while configuring API access keys, endpoints, and so on.

Returning to the sample function that expects the single parameter, `name`, you would create a JSON file to store its value:

```json
// filename is my-params.json
{
    "name": "Runtime"
}
```

Then use the `--param-file` flag when creating actions, creating packages, or invoking actions:

```
// update action
aio rt:action:update test first-function.js --param-file my-params.json

// invoke action
aio rt:action:invoke test --param-file my-params.json
```

### Final parameters

Sometimes, applications need to make sure their default parameters are final, or immutable, so calling clients can't override them. Achieve this by adding the `final` annotation - `-a final true`:

```
aio rt:action:update test first-function.js --web true --param name "Runtime" -a final true
```

This mechanism works for all actions, including web actions.

## Invoking web actions

The actions shown above were invoked from the CLI. This is fine for development and test, but production systems might need to invoke actions from HTTP REST calls, for example from a web application. 

Create web actions by adding the *--web* flag to the `aio` action command:

```
// creation time
aio rt:action:create test first-function.js --web true

// update
aio rt:action:update test first-function.js --web true
```

Note the `true` value used in the command: setting it to `false`  would disable the web action.

To call a web action, specify the full path to the action, which you can find by adding the `--url` flag to the action command:

```
aio rt:action:get test --url
```

This will return something like this:

```
ok: got action test
https://adobeioruntime.net/api/v1/web/[your namespace]/default/test
```

In the URL above, `default` stands for the `default` package that contains actions when you don't create them explicitly in a named package.

You can invoke the web action like this:

```
curl -L https://adobeioruntime.net/api/v1/web/[your namespace]/default/test -X GET
```

or like this:

```
curl https://[your namespace].adobeioruntime.net/api/v1/web/default/test -X GET
```

> Note the difference between this URL and the one `aio` returns. This is because when invoking web actions, Runtime adds protections that segregate namespaces from each other . The `aio`-generated link will still work, but it will return a 308 redirect to your namespace's subdomain on Runtime. For a further discussion of this please see [Securing Web Actions](securing_web_actions.md).

### Successful response

To send a response that follows the status code-headers-body HTTP response structure from an action to be used as a web action, you could rewrite our sample function:

```javascript
function main(params) {
    var nm = params.name || 'stranger';
    return {
        statusCode: 200,
        body: {
            payload: 'Hello ' + nm
        }
    }
}

exports.main = main;
```

This approach also allows you to set cookies or cache control headers, perform HTTP redirects, and so on.

### Unsuccessful response

On failed action invocations, the error code and message should be wrapped in an `error` object: this allows the system to interpret the response as an `applicationError`:

```javascript
async function main(params) {
    try {
        throw new Error("Boom!")
    } catch (err) {
        return {
            error: {
                statusCode: 500,
                body: {
                    payload: `Something went wrong: ${err}`
                }
            }
        }
    }
}

exports.main = main;
```

### General error handling

Handling errors in action code is extremely important: it is literally impossible to recover from a unhandled asynchronous errors in Node.js. Please refer to the [uncaughtExceptions](https://nodejs.org/api/process.html#event-uncaughtexception) discussion in Node.js documentation.

An unhandled asynchronous errors will cause termination of the action and destruction of the container running it. As a result, all in-flight activations will be failed, and the next action invocation will incur the overhead of creating a new container.

Here are incorrect and correct methods to handle asynchronous errors:

#### Incorrect handling of async errors

```javascript
function doSomethingAsync() {
    return new Promise((resolve, reject) => {
        try {
            setTimeout(() => {
                new Error("Something went wrong");
            }, 1000);
        } catch (err) {
            reject(err);
        }
    });
}
```

Here, the error is generated not while the executor is running, but afterwards. As a result, the try catch block will not catch the error, and the action will be terminated.

#### Correct handling of async errors

```javascript
function doSomethingAsync() {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            reject(new Error("Something went wrong"));
        }, 1000);
    });
}
```

This code will execute correctly and the error will be handled.     

When asynchronous operations are performed, there is almost always a chance for something to go wrong: network errors, a database connection issues, unexpected inputs, and more. 
To ensure the reliability and stability of the program, errors should always be handled inside the callback function passed to setTimeout or any other asynchronous function.

### HTTP context

When executing actions as web actions, the `params` object is decorated with additional information:

| Method         | Description                                                                                                                         |
| -------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| `__ow_method`  | the HTTP method of the request                                                                                                      |
| `__ow_headers` | the request headers                                                                                                                 |
| `__ow_path`    | the unmatched path of the request (matching stops after consuming the action extension)                                             |
| `__ow_body`    | the request body entity, as a base64-encoded string when its content is binary or JSON object/array, or as a plain string otherwise |
| `__ow_query`   | the query parameters from the request as an unparsed string                                                                         |

By default, web actions are accessible to anyone who knows their URLs. To secure access, review [Securing Web Actions](securing_web_actions.md).

## Deploying ZIP actions

The actions above were created from single source files. To create actions from multiple files or add npm modules, deploy ZIP actions. 

Requirements to deploy a ZIP action are:

- Configure `wskdeploy` instead of `aio` 

- Create the package.json file

- Create a manifest file

### wskdeploy

If you need to install `wskdeploy`, check [Setting up the wskdeploy CLI](tools/wskdeploy_install.md).

### Package.json file

You use the package.json file to specify the dependencies. If you don't have one already, in the same folder where your function files are, run `npm init -y`.

Make sure to declare any npm dependencies required for your functions in the package file.

### wskdeploy manifest file

The final step in deploying a ZIP action is to create the manifest file wskdeploy will use to create the action. In the parent folder of the folder that contains your function files and npm modules, create a `manifest.yaml`file. See the [YAML reference card](http://yaml.org/refcard.html) for more information on YAML format.

This is the folder structure:

```
--/actions
--/actions/node_modules/
--/actions/index.js
--/actions/package.json
--manifest.yaml
```

Here is a sample `manifest.yaml`:

```
packages:
    # this is the package name
    test:
        actions:
            # name of the action
            test-zip:
                # source for the action; in this case it is a folder
                function: actions
                runtime: nodejs:10
                # publish the action as a web action
                web:  yes
```

> Note: If your action requires dependencies such as other node modules or JavaScript files, place the action code in an `index.js` file and in the `manifest.yaml` file. The `function` value should point to the folder where the action is stored instead of the action file. These conditions are met in the example above.

To deploy, run the `wskdeploy` command from the folder that contains `manifest.yaml`:

```
wskdeploy
```

This will deploy an action called `test-zip` under a package called `test`; it can be invoked like this:

```
aio rt:action:invoke test/test-zip --result
```

To emove the action, run `wskdeploy` with the *undeploy* flag:

```
wskdeploy undeploy
```

`wskdeploy` supports other useful flows; please check the [official documentation](https://github.com/apache/openwhisk-wskdeploy) to learn about them.

> Note: You can also deploy ZIP actions using the aio command. This approach lacks the flexibility of the manifest.yaml file: it won't allow definition of multiple actions or packages at the same time. The ZIP file has to have in the root the package.json file.  `aio rt:action:create my-action --kind nodejs:20 zip-file.zip`

## Next step

Return to [Guides Index](../../guides/guides_index.md).
