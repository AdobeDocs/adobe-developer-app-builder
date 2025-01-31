---
keywords:
  - Adobe I/O
  - Extensibility
  - API Documentation
  - Developer Tooling
contributors:
  - 'https://github.com/duynguyen'
title: 'Lesson 2: Explore the App Builder App'
---

# Lesson 2: Explore the App Builder App

Within the newly created app, you have seen the `.env` file with your credentials for running the app. 

`package.json` is the [crucial part](https://docs.npmjs.com/creating-a-package-json-file) of almost every NodeJS project. It contains the list of dependencies, version, reproducible builds, and so on.

`ext.config.yaml` in the `src/dx-excshell-1/` folder is the cockpit of your App Builder app back end. It lists the declaration of serverless actions including name, source files, runtime kind, default parameters, annotations, and so on. You can find the grammar of writing manifest [here](https://github.com/apache/openwhisk-wskdeploy/blob/master/docs/programming_guide.md#wskdeploy-utility-by-example):

```yaml
operations:
  workerProcess:
    - type: action
      impl: dx-asset-compute-worker-1/worker
hooks:
  post-app-run: adobe-asset-compute devtool
  test: adobe-asset-compute test-worker
actions: actions
runtimeManifest:
  packages:
    dx-asset-compute-worker-1:
      license: Apache-2.0
      actions:
        get-profiles:
          function: actions/get-profiles/index.js
          web: 'yes'
          runtime: 'nodejs:18'
          inputs:
              LOG_LEVEL: debug
              tenant: $CAMPAIGN_STANDARD_TENANT
              apiKey: $SERVICE_API_KEY
          annotations:
            require-adobe-auth: true
            final: true
```

Your app currently has only one action, `get-profiles`:

* Source code is at `src/dx-excshell-1/actions/get-profiles/index.js`
* It is a [web action](../../guides/runtime_guides/creating_actions.md)
* The action will be run in the `nodejs:18` [runtime container on I/O Runtime](../../guides/runtime_guides/reference_docs/runtimes.md#node-js-v18-14-2)
* It has some [default parameters](../../guides/runtime_guides/creating_actions.md#working-with-parameters) such as `LOG_LEVEL`, `tenant`, `apiKey`, which are automatically available in the `params` object of the action without passing it to the action for every invocation. The `final` annotation set as `true` reveals that those parameters are immutable.
* Setting the `require-adobe-auth` annotation as `true` enables this action to be protected by the Adobe IMS user token in the request header. Without it, the action will return a `401 Unauthorized` error.

Now let's take a deeper look at the action's source code:

```javascript
/**
 * This action gets a list of customer profiles the Adobe Campaign Standard API
 */

const { Core } = require('@adobe/aio-sdk')
const { CampaignStandard } = require('@adobe/aio-sdk')
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
    const requiredParams = ['apiKey', 'tenant']
    const errorMessage = checkMissingRequestInputs(params, requiredParams, ['Authorization'])
    if (errorMessage) {
      // return and log client errors
      return errorResponse(400, errorMessage, logger)
    }

    // extract the user Bearer token from the input request parameters
    const token = getBearerToken(params)

    // initialize the sdk
    const campaignClient = await CampaignStandard.init(params.tenant, params.apiKey, token)

    // get profiles from Campaign Standard
    const profiles = await campaignClient.getAllProfiles()
    logger.debug('profiles = ' + JSON.stringify(profiles, null, 2))
    const response = {
      statusCode: 200,
      body: profiles
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

Here, the [action](https://github.com/apache/openwhisk/blob/master/docs/actions-nodejs.md) exposes a `main` function, which accepts a list of parameters from the client. It checks that required parameters for using the Campaign Standard SDK are present in the list, including the `Authorization` header for authentication against Adobe IMS.

An access token is retrieved to initiate the SDK client instance, which is then used to retrieve the list of customer profiles using the [getAllProfiles()](https://docs.adobe.com/content/help/en/campaign-standard/using/working-with-apis/managing-profiles/retrieving-profiles.html) function. Finally, the profiles are returned to the client. The entire execution is wrapped within a try-catch block, so errors are handled appropriately.

Next, let's see how the web UI communicates with the back end. All web assets are placed in the `src/dx-excshell-1/web-src` folder.

Beside a few auto-generated files  useful for running your app on Adobe Experience Cloud (ExC) Shell, `App.js` is the extension point of your UI.  

By default, it contains three pages: Home and About are just static pages listing reference docs; ActionsForm lists all available back-end actions, allows you to select which action to be invoke, and, when you click the "invoke" button, shows the invocation results in the browser console:

```javascript
<View gridArea='content' padding='size-200'>
  <Switch>
    <Route exact path='/'>
      <Home></Home>
    </Route>
    <Route path='/actions'>
      <ActionsForm runtime={props.runtime} ims={props.ims} />
    </Route>
    <Route path='/about'>
      <About></About>
    </Route>
  </Switch>
</View>
```
