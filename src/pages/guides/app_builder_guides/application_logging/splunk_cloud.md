---
keywords:
  - Adobe I/O
  - Extensibility
  - API Documentation
  - Developer Tooling
  - Logging
  - Log Forwarding
  - Monitoring
  - Splunk
  - Splunk Cloud
title: Forwarding Logs to Splunk Cloud
---

# Forwarding Logs to Splunk Cloud

This guide covers configuring App Builder to forward application logs to a Splunk Cloud account. 

## Prerequisites

- An index on your Splunk Cloud account. Create a new index following [Splunk's guide](https://docs.splunk.com/Documentation/Splunk/8.2.4/Indexer/Setupmultipleindexes)
- Local development setup for your App Builder application
- The latest version of AIO CLI. Check by running `aio --version`; update by running `npm install -g @adobe/aio-cli`

## Steps to configure log forwarding

### 1. Set up Splunk HTTP event collector

1. On your Splunk Cloud account homepage, select `Settings` from the ribbon and `Data Inputs` on the *Settings* pane

2. Click the `+ Add New` button corresponding to the HTTP Event Collector input type

3. On the *Select Source* screen:
   
   - Type in an input name, for example: `My App Builder Application` 
   - Make sure the `Enable Indexer Acknowledgment` checkbox is **not** ticked
   - Click the `Next` button on top

4. On the *Input Settings* screen:
   
   - Set the source type to `automatic`. Forwarded logs will be sent with the `sourcetype` field set to `_json`
   - From the list of indexes, select only the index on which you wish to receive logs from your App Builder application
   - Click the `Review` button on top.

5. On the _Done_ screen:
   
   - Copy the token value for later use.

### 2. Get your Splunk hostname and port

1. To find your `hostname` and `port` number, follow the procedures in [Send Data to HTTP Event Collector on Splunk Cloud Platform](https://docs.splunk.com/Documentation/Splunk/8.2.4/Data/UsetheHTTPEventCollector#Send_data_to_HTTP_Event_Collector_on_Splunk_Cloud_Platform) from Splunk documentation. 

2. Confirm your  `hostname` and `port` number by executing this cURL request, using the token saved when you set up the Splunk HTTP event collector:
   
   ```
   curl -X POST 'https://<hostname>:<port>/services/collector' \     
   -H "Authorization: Splunk <token>" \
   -d '{"event": "hello world"}'
   ```
   
   A `200 OK` response confirms your `hostname` and `port`. Note that I/O Runtime supports only ports `443` and `8088` for port forwarding.

### 3. Set up log forwarding in App Builder

1. Open Terminal and navigate to the App Builder project directory on your local machine.

2. Run this command, supplying the values from previous steps:
   
   ```
   aio app config set log-forwarding
   ? select log forwarding destination: Splunk HEC
   ? host: <hostname>
   ? port: <port_number>
   ? index: <index>
   ? hec_token: <token>
   ```
   
   Note:
   
   + Be sure not to prefix the protocol (`http://` or `https://`) before the hostname
   + Use the `hostname` and `port` as determined in "Getting Splunk host and port" above 
   + Use the `token` value token saved when you set up the Splunk HTTP event collector

3. Verify that the configuration change has taken place: 

```
aio app config get log-forwarding
```

4. Execute an action in your App Builder application workspace to generate logs.

5. Go to Splunk Home > Search and run the query:

```
index=<index>
```

6. If you don't see any logs in Splunk, check for log forwarding errors:

```
aio app config get log-forwarding errors
```

## Next steps

If you are unable to set up log forwarding using these procedures, please visit [Adobe Experience Leage App Builder Community](https://experienceleaguecommunities.adobe.com/t5/app-builder/ct-p/adobe-app-builder) for support.

Proceed to [Forwarding logs to Splunk Enterprise](splunk_enterprise.md).

Return to [Managing Application Logs](logging.md).

Return to [Guides Index](../../guides_index.md).
