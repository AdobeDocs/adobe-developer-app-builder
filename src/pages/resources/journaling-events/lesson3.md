---
keywords:
  - Adobe I/O
  - Extensibility
  - API Documentation
  - Developer Tooling
contributors:
  - 'https://github.com/Yu1986'
title: 'Lesson 3: End to end test'
---

# Lesson 3: End-to-End Test

In the previous lessons, we have set up two App Builder apps:

- An event provider that automatically generates events 
- An event consumer that automatically pulls from the Journaling API and writes to App Builder storage

If you would like to configure the alarm package to automatically trigger events or pull events from the Journaling API, set up `app.config.yaml` and try the `/whisk.system/alarms/interval` feed of the OpenWhisk Alarm Package to fire trigger events on an interval schedule. To see the effect quickly, make it run every minute. You will need a trigger set up with the `/whisk.system/alarms/interval` feed, and a rule to wire this trigger to the `publish-event` or `consume-event` action created earlier.

The only required parameter for the `interval` feed is `minutes`, an integer representing the length of the interval (in minutes) between trigger fires. Optional parameters are `trigger_payload`, `startDate` and `stopDate`.

Now that we have deployed these apps in two different namespaces, we can configure them to trigger at different cadences. if successful, the events will be stored in the App Builder database. 
