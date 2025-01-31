# Retrieve Action Invocation Results

The activation record of an action invocation contains information to help you understand what happened. It contains the invocation's: 

* **Activation ID**, a unique identifier

* **Namespace** and **action name** of the invoked action

* **Logs**, if your action generated any

* **Response**, a dictionary that contains the status, success, and result of the invoked action

## Retrieve activations

If you have invoked an action named `hello` and enter the command: 

`aio rt:activation:list`

You will see a list of activation IDs from actions invoked during the preceding seven days, along with the name of each invoked action:

```
e9932762894d4ccf932762894d6ccff4 hello            
c76dbe66e9b04ad5adbe66e9b06ad541 hello            
[]...]
```

You can retrieve the entire activation record by entering `aio rt:activation:get <activation id>`:

```
aio rt:activation:get e9932762894d4ccf932762894d6ccff4
ok: got activation e9932762894d4ccf932762894d6ccff4
{
    "namespace": "your-namespace",
    "name": "hello",
    "version": "0.0.20",
    "subject": "your-namespace",
    "activationId": "e9932762894d4ccf932762894d6ccff4",
    "start": 1542232412321,
    "end": 1542232412366,
    "duration": 45,
    "response": {
        "status": "success",
        "statusCode": 0,
        "success": true,
        "result": {
            "body": {
                "payload": {
                    "message": "hello world!"
                }
            },
            "statusCode": 200
        }
    },
    "logs": [],
    "annotations": [
        {
            "key": "path",
            "value": "your-namespace/hello"
        },
        {
            "key": "waitTime",
            "value": 23
        },
        {
            "key": "kind",
            "value": "nodejs:10-fat"
        },
        {
            "key": "limits",
            "value": {
                "logs": 10,
                "memory": 256,
                "timeout": 60000
            }
        },
        {
            "key": "initTime",
            "value": 40
        }
    ],
    "publish": false
}
```

You can also retrieve part of the activation record:

```
// just the result
aio rt:activation:result <activation ID>

// just the logs
aio rt:activation:logs <activation ID>
```

## Block or unblock successful activations

At a scale of millions of activations per day, it may be difficult to screen out successful activations to debug those that failed. To simplify this task, I/O Runtime doesn't persist successful activations. 

The exceptions are asynchronous actions invoked in a non-blocking fashion. Their results are persisted regardless of success or failure, so you can extract their responses later. 

You can still view the total number of executions, as described in the [Logging and Monitoring](../../guides/runtime_guides/logging_monitoring.md) Guide. 

To review all activation results during development, set the extra logging header to `on` in the request: 

```
X-OW-EXTRA-LOGGING: on
```

We do not recommend use of the extra logging headers in production environments, since it may degrade invocation performance. 

## Retrieve activations for non-blocking calls

Execution of a non-blocking asynchronous action immediately returns its activation ID. Queries for an action's result or logs before execution is compete will generate an error:

```
aio rt:activation:get 1d24121f91384740a4121f91389740f0
error: Unable to get activation '1d24121f91384740a4121f91389740f0': The requested resource does not exist. (code myM2aaCufgIcnjnrbNIHztNmhL2HvFia)


aio rt:activation:logs c8c4f354c1824f2c84f354c182ef2cdb
error: Unable to get logs for activation 'c8c4f354c1824f2c84f354c182ef2cdb': The requested resource does not exist. (code nCPo4KRbYmvOTcwPQVfDJdrwtJpv1c3d)
```

## Activation time to live

Activation time to live is seven days, a system setting, and can't be changed by Developers. If you can't see activations that you know took place, they may have timed out.

## Next steps

This completes the "Get Started with Adobe I/O Runtime" tutorial. 

For a detailed review of Runtime components, operations, settings, and tuning, see [Understanding I/O Runtime](understanding_runtime.md).

The procedures described above will help you debug your first actions. For additional details about debugging tools and processes, review the [Logging and Monitoring](../../guides/runtime_guides/logging_monitoring.md) Guide.
