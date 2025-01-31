# Asynchronous Calls

The system executes web actions or REST APIs as synchronous (blocking) calls and gives them 60 seconds to complete their work. Actions that need more time can use asynchronous (async) calls, and use up to 180 minutes. 

Since web actions and APIs are always executed synchronously, you need to separate work that needs more time (the async call) from work that can be done in under a minute using synchronous calls. There are two ways to do this:

## Option 1: web action that calls the actual action async

First, we create an action. This one, `worker`, uses a setTimeout function to simulate long-running code. 

```
// worker.js
function main(args) {
    return new Promise(function(resolve, reject) {
        setTimeout(function() {
            var result = {
                statusCode: 200,
                body: { 
                    payload: 'Hello from the long running job!'
                }
            };
            resolve(result);
        }, 100000);
    });   
}

exports.main = main;
```

Next we set the `-t` flag to change the default timeout. If we know that an action needs about 100,000 milliseconds to complete, we can set it to 120,000 milliseconds (2 minutes) to be sure it completes its work before timeout.

```
aio rt:action:create my-worker worker.js -t 120000
```

Then we build a web action that calls the `worker` action we created above. We can simplify this step by using the OpenWhisk Node module. Since it's part of I/O Runtime Node.js environment, we don't need to pack the module with our code. 

```
//web-action.js
let openwhisk = require("openwhisk");

 // This returns the activation ID of the action that it called
function main(args) {
    let ow = openwhisk();
    return ow.actions.invoke({
                    name: 'worker', // the name of the action to invoke
                    blocking: false, // this is the flag that instructs to execute the worker asynchronous
                    result: false,
                    params: args
                    }); 

}

exports.main = main;
```

Now we can create the web action:

```
aio rt:action:create test web-action.js --web true
```

Invoking this web action will deliver the ActivationId for the `worker` action, which can be used to retrieve the result.

In this sample invocation, the `.json` extension appended to the URI instructs the system to return the result as JSON:

```
curl https://adobeioruntime.net/api/v1/web/YOUR-NAMESPACE-HERE/default/test.json
{
  "activationId": "0123456789"
}
```

## Option 2: using triggers and rules

Another way to set up an asynchronous call is to create a trigger and a rule to call the action. This will work only for calls using a POST method; if you need a GET, use Option 1.

Here is the code, using the `my-worker` action from above:

```
// First, you create a trigger
aio rt:trigger:create my-worker-trigger

//Then, you create the rule
aio rt:rule:create async-rule my-worker-trigger my-worker
```

Now when the trigger `my-worker-trigger` is executed, it will in turn execute the `my-worker` action. Note the authentication header: it must match the authentication as used by the namespace where the actions, triggers, and rules were created.

```
curl https://adobeioruntime.net/api/v1/namespaces/_/triggers/my-worker-trigger -X POST -H "Authorization: Basic NAMESPACE AUTHORIZATION"
```

To find the URI and the authorization for a trigger or secured web action, add `-v` (verbose) to the command to get it:

```
aio rt:trigger:get my-worker-trigger -v
```

Retrieval of the action activationId (and not the trigger activationId) is trickier than in Option 1. Use the trigger activationId to retrieve the logs, which will contain the action activationId you can use to read the result.

## Next steps

Learn about [Triggers and Rules](reference_docs/triggersrules.md).

Return to [Guides Index](../guides_index.md).
