# Throughput Tuning

## Containers

### Setting concurrency

The main instrument for tuning how rapidly actions can be executed is the value you set for `action/container concurrency`. This is not related to the concurrent value per namespace or minuteRate value, although these two enforce an upper limit on actions executed per minute in your namespace.

The default `action/container concurrency` value is `200` invocations of an action in the same container. If, for example, you plan to execute a `HelloWorld` action 100 times, either simultaneously or within minutes, the default value (`200`) means that the system will use one container rather than as many as 100.

This helps avoid cold-start issues. When the system has no containers left, it creates new ones - "cold starts" that add a lot of latency to your application.

You can set `action/container concurrency` to any value between `1` and `500`. Here, the limit is set to `100`:

```
aio rt:action:create actionName fileName.js -c 100
```

### Concurrency considerations

1. Containers are kept warm after for 10 minutes after an invocation finishes. For those 10 minutes, it is highly unlikely (p<.01) that invoking the same action will require a cold start.
2. Experiment with the value you set for concurrency limits. The default, `200`, is a good place to start, but a smaller or a larger value may be better depending on how much memory and other resources your action consumes.  
3. Be sure your code is designed to work when executed in parallel. For example, avoid using global variables to store values that may differ between invocations.
4. If an action works on a large data set that is not different from one invocation to another, a global variable can maximize the chances that the next execution can use it. But your code should handle the condition in which the variable is not initialized.
5. It is not guaranteed that all invocations will use the same container. In case of errors, for example, the existing container is destroyed and a new one created.
6. If your action code consumes a large amount of memory, you may need to set concurrency to a lower value to avoid exceeding the container's memory limit.

### Using pre-warmed containers

A second way avoid cold-starts and mimimize latency is to create actions that use the default Node version, and a memory settings of `256MB`, `512MB`, or `1024MB`. 

The system has a pool of "pre-warmed" containers it will use for any incoming call that can't be sent to a running container - so long as the action matches the pre-warmed container's Node version and memory setting. If it does, time will still be spent for injecting your action code, but not for creating a container first. If it doesn't, your action will wait while the system creates a new container.

## Caching responses

In addition to optimizing the use of containers, caching action responses helps improve throughput. For the time a cache is valid, invocations of the action will not increase the `minuteRate` counter or concurrent action invocations per namespace. This is because the action is not actually executed: the system serves the result from the cache.

Use the Cache-Control directive to configure the cache. Here is an example of an action that sets the cache with a Time to Live of `30 minutes`. In the response object you'll find an entry with `X-Cache: HIT` or `X-Cache: MISS`, depending on whether the answer was returned from the cache or not. 

```
function main(args) {
   return {
       body: "OK",
       statusCode: 200,
       headers: {
           "Cache-Control": "max-age=1800"
       }
   }
}
```

One way to verify whether a response is returned from the cache or not is to check for this header:

```
X-GW-Cache: HIT
```

<InlineAlert slots="text"/>

Encoded responses can't be cached, so the `Content-Encoding` response header must be empty to cache responses. 

### Vary header

The caching layer supports the use of [Vary header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Vary) to cache based on header fields as well as on URL and query parameters.

This action responds to certain header fields while doing a complex calculation:

```
curl -H "storeId: 1234" https://runtime-namespace-1.
adobeioruntime.net/api/v1/web/store?query={products
(pageSize: 10,filter:{ id:{ eq:"abcedefg"}}){items{name}}}`
```

It could produce this response:

```
HTTP/1.1 200 OK
Content-Type: application/json
Vary: storeId
Cache-Control: max-age=120
X-GW-Cache: MISS

{"someBigData" : ["array"]}
```

This response would add the `storeId` to the cache key, so that subsequent requests with the same `storeId` in their headers would create a `HIT`, up to the limits imposed by the cache control header settings. Any time the value varied, it would be a `MISS`, and would be stored under a new key with new cache control directives.

## Next step

Return to [Guides Index](../guides_index.md).
