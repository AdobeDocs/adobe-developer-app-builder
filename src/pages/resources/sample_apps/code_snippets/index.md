---
keywords:
  - Adobe I/O
  - Extensibility
  - API Documentation
  - Developer Tooling
title: App Builder Code Snippets - Caching HTTP responses  
---

# Caching HTTP responses

Demonstrates how to cache the response of an action at Gateway level. To test this functionality, make sure that the Cache-Control header in your HTTP request is not set to `no-cache`, the default if you use Postman or your browser's developer tools. More information is available [here](../../../guides/runtime_guides/throughput_tuning.md#caching-responses). 

```javascript
const { Core } = require('@adobe/aio-sdk')
const { errorResponse, stringParameters, checkMissingRequestInputs } = require('../utils')

// main function that will be executed by Adobe I/O Runtime
async function main (params) {
  // create a Logger
  const logger = Core.Logger('main', { level: params.LOG_LEVEL || 'info' })

  try {
    // 'info' is the default level if not set
    logger.info('Calling the main action')

    // log parameters, only if params.LOG_LEVEL === 'debug'
    logger.debug(stringParameters(params))

    // check for missing request input parameters and headers
    const requiredParams = ['name']
    const requiredHeaders = []
    const errorMessage = checkMissingRequestInputs(params, requiredParams, requiredHeaders)
    if (errorMessage) {
      // return and log client errors
      return errorResponse(400, errorMessage, logger)
    }

    // sleeping 2 secs to simulate an outgoing server call
    await new Promise(r => setTimeout(r, 2000))

    const response = {
      headers: {
        'Cache-Control': 'max-age=300' // cached 5 min
      },
      statusCode: 200,
      body: { message: `Hi ${params.name}, I am ready!` }
    }

    // log the response status code
    logger.info(`${response.statusCode}: successful request`)
    return response
  } catch (error) {
    // log any server errors
    logger.error(error)
    // return with 500
    return errorResponse(500, 'server error', logger)
  }
}

exports.main = main
```
