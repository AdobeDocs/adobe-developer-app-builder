---
keywords:
  - Adobe I/O
  - Extensibility
  - API Documentation
  - Developer Tooling
contributors:
  - 'https://github.com/Yu1986'
title: 'Lesson 2: Create the Event Consumer using Journaling API'
---

# Lesson 2: Create an Event Consumer using the Journaling API

In this lesson, we will:

- Create an event consumer using an App Builder template
- Use [aio-lib-state](https://github.com/adobe/aio-lib-state) as the storage library for events from the Journaling api
- Schedule cron jobs with alarms to trigger the event consumer to pull events from the Journaling API every x minutes

## Create an event consumer using an App Builder template

We will use an App Builder template to create the event consumer, this time using the `generic` template. In this Code Lab we will create a headless app following [this procedure](../cron-jobs/index.md).

The Adobe I/O Events Journaling API supports enterprise integrations that consume events at their own cadence and process them in bulk. Unlike webhooks, no additional registration or other configuration is required; every enterprise integration that is registered for events is automatically enabled for journaling. Journaling data is retained for 7 days.

After you fire an event, you should be able to verify your event through journaling the unique API endpoint you get from the console following the instructions below. You could use the curl command or Postman to call this journaling unique API endpoint to see your fired event. Or you could use [Custom Event SDK](https://github.com/adobe/aio-lib-events) to call the Journaling API to retrieve your event.

## Write the data into App Builder storage

We will use [aio-lib-state](https://github.com/adobe/aio-lib-state) to store the event from the Journaling API. First, we install the dependency:

```bash
npm i --save @adobe/aio-lib-state
```

Then we import it:

```javascript
const stateLib = require('@adobe/aio-lib-state');
```

Set up write to storage inside the main function: 

```javascript
async function saveToDb(params, new_events) {
  const stateCLient = await State.init()


  var events = await stateCLient.get(params.db_event_key) 
  if (events === undefined) {
    events = {latest: new_events[new_events.length - 1], events: new_events}
  } else {
    events = events.value
    events.latest = new_events[new_events.length - 1]
    events.events.push(new_events)
  }
  await stateCLient.put(params.db_event_key, events, { ttl: -1 })
}
```

Write down the event postion to make sure that if the action fails the next invocation will retrieve from the same index instead of the new one. This way, no events are lost.

```javascript
async function getLatestEventPosition(params) {
  const stateCLient = await State.init()
  const events = await stateCLient.get(params.db_event_key)
  if (events === undefined) {
    return undefined
  } else {
    return events.value.latest.position
  }
}
```

You can see the source code [here](https://github.com/AdobeDocs/adobeio-samples-journaling-events/blob/main/event-consumer/actions/event_consumer/index.js).

## Scheduling cron jobs to automate consuming events

Following the same steps as in [lesson 2 of Scheduling Cron Jobs with Alarms](../cron-jobs/lesson2.md), schedule cron jobs to make sure the consumer is pulling events from journaling API every x minutes. Now we can deploy this event consumer app in another runtime namespace. 
