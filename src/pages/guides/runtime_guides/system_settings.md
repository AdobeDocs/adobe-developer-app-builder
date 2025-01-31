# System Settings

These are the system settings and limitations to consider when designing and debugging your actions.

| Limit                                       | Description                                                                                                                                      | Configurable                                      | Default             | Range               |
| ------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------- | ------------------- | ------------------- |
| timeout                                     | Container run time limit, in msec. For blocking calls like web actions, 60,000 msec (1 minute); for non-blocking calls, 3,600,000 msec (1 hour). | per action                                        | 60,000 milliseconds | 100ms - 3,600,000ms |
| memory                                      | Container memory allocation limit, in MB.                                                                                                        | per action                                        | 256MB               | 128MB - 4096MB      |
| minuteRate (actions)                        | Action rate limit per namespace per minute. Returns `429: TOO MANY REQUESTS` if exceeded.                                                        | per namespace (not configurable)                  | 600/minute          | 600/minute          |
| minuteRate (web actions with extra logging) | Rate limit per namespace per minute for actions invoked with the header `X-OW_EXTRA-LOGGING: on` Returns `429: TOO MANY REQUESTS` if exceeded.   | per namespace (not configurable)                  | 30/minute           | 30/minute           |
| logs                                        | Container limit on write volume to stdout, in MB.                                                                                                | per action                                        | 10MB                | 0MB - 10MB          |
| concurrent                                  | Limit on  activations  submitted or queued for execution per namespace. Returns `429: TOO MANY REQUESTS` if exceeded.                            | per namespace (not configurable)                  | 100                 | 100                 |
| action / container concurrency              | Limit on action invocations sent in parallel to the same container.                                                                              | per action                                        | 200                 | 1 - 500             |
| codeSize                                    | Limit on archived action size, including dependencies,  in MB.                                                                                   | per action (not configurable)                     | 22MB                | 0MB - 22MB          |
| parameters                                  | Size limit of attached parameters, in MB.                                                                                                        | per action / package / trigger (not configurable) | 1MB                 | 0 - 1MB             |
| payload                                     | Size limit on POST content, including  carried parameters for an action invocation or trigger firing, in MB.                                     | per action / trigger (not configurable)           | 1MB                 | 0 - 1MB             |
| result                                      | Size limit on action result, in MB.                                                                                                              | per action (not configurable)                     | 1MB                 |                     |
| minuteRate (triggers)                       | Limit on triggers fired per namespace per minute. Returns `429: TOO MANY REQUESTS` if exceeded.                                                  | per namespace (not configurable)                  | 600/minute          | 600/minute          |
| actions Sequence Maxlength                  | Limit on number of actions  chained in a sequence.                                                                                               | per namespace (not configurable)                  | 50                  | 50                  |
| list                                        | Limit on number of entities that can be listed.                                                                                                  | per list request                                  | 30                  | 1 - 50              |
| local storage                               | Limit on local storage available for action, in MB.                                                                                              | not configurable                                  | 600MB               |                     |

## Sequences and timeout

When invoking a sequence in a blocking manner, there is a 60-second limit for timeout that can't be changed. This means the sum of the execution times of all actions in the sequence must be 60 seconds or less. You can specify a timeout value greater than 60 seconds, but the system will ignore it and impose the 60-second limit.

If one of your actions needs more than 60 seconds (therefore putting the sequence over the limit), the only solution is to invoke it as a non-blocking action using the OpenWhisk npm module. So, using the example above, you could have `actionA` calling another action in a non-blocking manner. You can see an example of how to do this [here](asynchronous_calls.md).

## Using pre-warmed containers

You can avoid most container cold starts and improve chances of low-latency execution by creating actions that use the default Node version and a memory setting of `256MB`, `512MB`, or `1024MB`. 

The system maintains a pool of "pre-warmed" containers with these settings waiting to be used. Incoming calls that can't be sent to an existing running container will be sent to one of the pre-warmed containers, but only if the action's Node version and memory setting match the container's.  Use of pre-warmed containers cuts latency because time is spent only to inject action code, not to wait for creation of a container.

## Timeout

Changes to the default timeout setting must be done explicitly. For example, this command increases the timeout to 300,000 msec (5 minutes):
`aio rt:action:create action-name source.js -t 300000`

If you plan to increase the timeout to more than one minute, be aware that:

1. Blocking calls such as web actions time out in one minute regardless of the timeout setting, and return an error to the caller. But action execution will continue until either it completes or the timeout value is exceeded. Actions that time out produce a Developer error as their result; retrieve it by polling for the activationId, then use it to get the result.
2. Asynchronous calls respond immediately with an activationId. Execution continues until the work is done or the timeout value is reached.

## Activation TTL

Activation Time To Live (TTL) is seven days. This is a system setting that can't be changed by Developers. If you don't see activations that you know took place, consider whether they exceeded their TTL .

## Next step

Return to [Guides Index](../guides_index.md).
