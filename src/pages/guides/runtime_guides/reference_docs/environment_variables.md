# Environment Variables

When an action is being executed, your code can use the following environment variables:

* `__OW_ACTION_NAME` = the name of the action
* `__OW_ACTION_VERSION` = the internal revision of the action (developers cann't affect this)
* `__OW_ACTIVATION_ID` = the activation ID
* `__OW_ALLOW_CONCURRENT` = indicates the runtime should support concurrency (should always be true for nodejs actions)
* `__OW_API_HOST` = the host used for making additional OpenWhisk API requests within the action (e.g. using OpenWhisk JavaScript SDK)
* `__OW_CLOUD` = AWS vs Azure
* `__OW_NAMESPACE` = the namespace of the invocation client
* `__OW_REGION` = AWS/Azure region where the action was executed
* `__OW_TRANSACTION_ID` = request ID sent with OW API request, or generated; see response header `X-Request-Id`

## Next steps

Return to the [Runtime Reference Documentation Index](index.md).

Return to the [Guides Index](../../guides_index.md).
