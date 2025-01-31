# Multiple Regions

Actions are executed in one of three regions on AWS:

* `US East` (us-east-1) | Northern Virginia
* `EU` (eu-west-1) | Ireland
* `Asia Pacific` (ap-northeast-1) | Tokyo

## How do we execute your action?

When an action is deployed, action code is made available in all regions. At execution time, a latency-based routing policy runs the action in the fastest available region.

When a region is unavailable, action invocations are routed to an available region based on latency .

No action is required by the client to execute an action on the fastest available region, and clients cannot restrict the region in which an action is invoked. 

## How do I know in what region my actions are executed?

The cloud and region in which an action is invoked is available at execution time via environment variables; `__OW_CLOUD` for the cloud, and `__OW_REGION` for the region as seen here:

```javascript
process.env['__OW_CLOUD'] // example output: aws
process.env['__OW_REGION'] // example output: us-east-1
```

Please note that regions can change due to a failover or permanent platform change. Your application should not work against a hard-coded set of regions, and should handle cases in which the region in which it is executed changes.

## Next steps

Return to the [Runtime Reference Documentation Index](index.md).

Return to the [Guides Index](../../guides_index.md).
