---
keywords:
  - Adobe I/O
  - Extensibility
  - API Documentation
  - Developer Tooling
title: App Builder Code Snippets - Real-time data from Adobe Analytics API 1.4  
---

# Real-Time Data from Adobe Analytics API 1.4

Demonstrates how to write an action accessing Adobe Analytics Real-time API 1.4

```javascript
/**
 * Demonstrating how to write an action accessing Adobe Analytics Real-time API 1.4
 *
 * Sample curl request:
 * curl --location --request GET 'https://my-namespace.adobeioruntime.net/api/v1/web/my-app-0.0.1/analytics14' \
 * --header 'Authorization: Bearer ey123...' \
 * --header 'x-gw-ims-org-id: some-org-id' \
 * --header 'x-api-key: some-key'
 */

const fetch = require('node-fetch')
const { Core } = require('@adobe/aio-sdk')
const { errorResponse, getBearerToken, stringParameters, checkMissingRequestInputs } = require('../utils')

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
    const requiredParams = [/* add required params */]
    const requiredHeaders = ['Authorization', 'x-gw-ims-org-id', 'x-api-key']
    const errorMessage = checkMissingRequestInputs(params, requiredParams, requiredHeaders)
    if (errorMessage) {
      // return and log client errors
      return errorResponse(400, errorMessage, logger)
    }

    // extract the user Bearer token from the Authorization header
    const token = getBearerToken(params)

    // sample payload, please customize according to your report set-up
    const data = {
      "reportDescription": {
        "source": "realtime",
        "reportSuiteID": "change-me",
        "metrics": [
          { "id": "pageviews" }
        ],
        "elements": [
          {
            "id": "page",
            "search": { "keywords": [ "product" ] },
            "top": 200
          }
        ],
        "dateFrom": "-5 minutes"
      }
    }
    const apiEndpoint = 'https://api.omniture.com/admin/1.4/rest/?method=Report.Run'
    const headers = {
      'x-api-key': params.headers['x-api-key'],
      'Authorization': `Bearer ${token}`,
      'x-ims-org-id': params.headers['x-gw-ims-org-id'],
      'x-proxy-global-company-id': 'change-me',
      'Content-Type': 'application/json'
    }

    const res = await fetch(apiEndpoint, { method: 'POST', headers: headers, body: JSON.stringify(data) })
    const response = {
      statusCode: 200,
      body: {
        data: await res.json()
      }
    }
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
