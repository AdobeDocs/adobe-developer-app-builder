---
keywords:
  - Adobe I/O
  - Extensibility
  - API Documentation
  - Developer Tooling
title: Events Using Runtime Actions as Webhook
description: >-
  Creating cron jobs in an App Builder application to consume events using
  Journaling API.
contributors:
  - 'https://github.com/Yu1986'
---

# Events Using Runtime Actions as Webhook

The Code Lab will show how to consume events using runtime actions as webhook. 

## Background

In the Code Lab [Consume Events using Journaling API](../journaling-events/index.md) we showed how to consume events using Journaling API. This one will introduce another way to consume events - runtime action as webhook. The integration between Adobe I/O Runtime and I/O Events allows you create runtime actions set up as webhook endpoints on the Adobe Developer console for receiving events. Every time an event fires, your runtime action is executed and the debug tracing feature allows you to debug easily.  

## Benefits of using Runtime Actions as webhook

Two main benefits to choose Runtime actions as webhook are: 

- [Built in Signature Verification](https://developer.adobe.com/events/docs/guides/sdk/sdk_signature_verification/)
- Tracing actions with Activation IDs 

## How to choose between Journaling API and Runtime Action webhook

- The [journaling approach](../journaling-events/index.md) is recommended when you have long-running (asynchronous) actions that require guaranteed event handling, especially when there is a surge of events 
- Runtime action webhook is recommended for short-running (synchronous) actions, for example those that respond within 10 sec

## Overview

This Code Lab will take you through the I/O events SYNC webhook registration using your own runtime actions through the console.
As part of this Code Lab, we will cover all the behind-the-scenes actions that occur while: 

- Using the App Builder template `publish-event`to set up a custom event as event provider
- Setting up event registration 
- Ingesting subscribed events for that webhook in the I/O Events pipeline
- Debug tracing on the console for the Success and Failure scenarios
