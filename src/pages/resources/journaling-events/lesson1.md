---
keywords:
  - Adobe I/O
  - Extensibility
  - API Documentation
  - Developer Tooling
contributors:
  - 'https://github.com/Yu1986'
title: 'Lesson 1: Create an Event Provider using App Builder'
---

# Lesson 1: Create an Event Provider using App Builder

In this lesson, we will :

- Create an event provider using an App Builder template
- Register the App as event provider 
- Fire Events
- Schedule cron jobs with alarms

## Create an event provider using App Builder template

To provide an end-to-end solution, for this Code Lab, we need an event provider to send events to the Journaling API, at a rate that is configurable to help with testing. We will use the OpenWhisk Alarms Package in an App Builder application to create cron jobs. 

First, we will create a new App Builder App from a template, following [this Code Lab](../event-driven/lesson1.md). Be sure to add `I/O management API` in the console and choose `publish-event` in the CLI template. 

## Register the app as event provider

To use the CLI to register the app as an event provider, we need to install the Adobe I/O Event CLI plugin, like this:

```bash
npm install -g @adobe/aio-cli-plugin-events
```

Then follow the steps in [this Code Lab](../event-driven/lesson2.md).

## Fire events and set up to consume them

Now we can arrange to fire events by following [the procedure from this Code Lab](../event-driven/index.md), making sure to choose Journaling API as the way to consume them.

## Scheduling cron jobs with alarms

Follow [this CodeLab](../cron-jobs/index.md) to fire events automatically by using the runtime alarms package.

Your `app.config.yaml` should now look like this:

```yaml
application:
  actions: actions
  web: web-src
  runtimeManifest:
    packages:
      my-app:
        license: Apache-2.0
        actions:
          generic:
            function: actions/generic/index.js
            web: 'yes'
            runtime: 'nodejs:14'
            inputs:
              LOG_LEVEL: debug
            annotations:
              require-adobe-auth: true
              final: true
          publish-events:
            function: actions/publish-events/index.js
            web: 'yes'
            runtime: 'nodejs:14'
            inputs:
              LOG_LEVEL: debug
              apiKey: $SERVICE_API_KEY
              providerId: $PROVIDER_ID
              eventCode: $EVENT_CODE
              client_id: $CLIENT_ID
              client_secret: $CLIENT_SECRET
              technical_account_email: $TECH_ACCOUNT_EMAIL
              technical_account_id: $TECH_ACCOUNT_ID
              ims_org_id: $IMS_ORG_ID
              private_key: $PRIVATE_KEY
            annotations:
              final: true
        Triggers:
          everyMin:
            feed: /whisk.system/alarms/interval
            inputs:
              minutes: 1
          rules:
            everyMinRule:
              trigger: everyMin
              action: publish-events
```

To test the action, execute `aio app deploy` in the VSCode terminal. Once the deployment is finished, run `aio rt action invoke your-app-name/generic`, and then verify its result and logs using `aio rt activation get ID` and `aio rt activation logs ID`.

If successful, the event provider should automatically send the events, you should be able to use Postman or cURL to verify that the Journaling API is receiving them. 
