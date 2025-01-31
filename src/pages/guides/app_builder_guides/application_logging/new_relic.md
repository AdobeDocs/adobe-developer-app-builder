---
keywords:
  - Adobe I/O
  - Extensibility
  - API Documentation
  - Developer Tooling
  - Logging
  - Log Forwarding
  - Monitoring
  - New Relic
title: Forwarding Logs to New Relic
---

# Forwarding Logs to New Relic

This guide covers configuration of your App Builder application to forward logs to your New Relic deployment.

## Prerequisites

* A New Relic account and an [Ingest license API key](https://docs.newrelic.com/docs/apis/intro-apis/new-relic-api-keys/).

* A local development setup for your App Builder application.

* The latest version of AIO CLI. Check by running `aio --version`; update by running `npm install -g @adobe/aio-cli`

## Identify the API key and base URI

1. Log in to New Relic and navigate to the API keys screen. Pick the `INGEST - LICENSE` API key type you want to use and copy the `key` value for later use.

2. Depending on the region hosting your New Relic account, you must use the United States or Europe endpoint as a `base uri`. 
   
   If you don't know the region of your New Relic instance, check the browser URL of your New Relic home. 
   
   * If the URL begins with `https://one.newrelic.com/`, specify 
     `https://log-api.newrelic.com/log/v1` as the URI
   
   * If it begins with `https://one.eu.newrelic.com/`, specify 
     `https://log-api.eu.newrelic.com/log/v1`

## Set up log forwarding in App Builder

1. From the command line, navigate to the App Builder project directory on your local machine.

2. Run this command, and supplying values from previous steps:
   
   ```terminal
   aio app config set log-forwarding
   ? select log forwarding destination: New Relic
   ? base URI: <base_uri>
   ? license key: <license_key>
   ```
   
   The URI value must include the protocol (`https://`).

3. Verify that the configuration change has taken effect:
   
   ```terminal
   aio app config get log-forwarding
   ```

4. Execute an action in your App Builder application workspace to generate logs.

5. Go to New Relic Home > Logs and run your query.

6. If you don't see any logs in New Relic, check for log forwarding errors:
   
   ```terminal
   aio app config get log-forwarding errors
   ```

## Next steps

If you are unable to set up log forwarding using these procedures, please visit [Adobe Experience Leage App Builder Community](https://experienceleaguecommunities.adobe.com/t5/app-builder/ct-p/adobe-app-builder) for support.

Return to [Managing Application Logs](logging.md).

Return to [Guides Index](../../guides_index.md).
