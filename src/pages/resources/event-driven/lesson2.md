---
keywords:
  - Adobe I/O
  - Extensibility
  - API Documentation
  - Developer Tooling
contributors:
  - 'https://github.com/Yu1986'
title: 'Lesson 2: Register the App as Event Provider'
---

# Lesson 2: Register the App as an Event Provider

After you have set up your integration and collected the information, you can start to set up your event provider.

### Use CLI to create provider, eventmetadata and webhook registration

Be sure you the Adobe I/O Events CLI Plugin is installed, and then run these commands:

```bash
aio console org list
aio console org select <orgId>
aio console project list
aio console project select <projectid>
aio console workspace list
aio console workspace select <wkspId>
aio app use
aio event => this will list all the commands for events.
```

## Register the event provider

After selecting the org, project and workspace, start to create the registration. 

```bash
aio event provider create
```

Fill in the requested information, and the terminal will show you the provider ID. Once you have successfully created the provider, copy the `provider id` for use in the next steps. Now we can create eventmetadata:

```bash
aio event eventmetadata create <provider id>
```

CLI will prompt you to input the `label` and `event code`for the eventmetadata. 

Now create the registration:

```bash
aio event registration create 
```

The terminal will display a sample of JSON format with the information needed to create a new event registration. Copy it, create your own `.json` file, fill in the details and use it. 

The event provider is now registered.

Another option for registering an event provider is to run the javascript code available [here](https://github.com/AdobeDocs/adobeio-samples-custom-events-registration) - just fill in the `.env` and run `npm start` .

## Check your result on Console

After using the events CLI plugin to create an event provider, you will see your event provider registrated in console and your journaling endpoint.

 ![event-provider](assets/event-provider.png)

Note: You will need your `event provider id` and `event code` from this lesson to fire an event in the next lesson:
