---
keywords:
  - Adobe I/O
  - Extensibility
  - API Documentation
  - Developer Tooling
contributors:
  - 'https://github.com/duynguyen'
title: 'Lesson 1: Create a New App Builder App from Campaign Standard Template'
---

# Lesson 1: Create a New App Builder App from Campaign Standard Template

To initialize an App Builder app, use the `init` command from the CLI:

```bash
aio app init customers-dashboard
```

Select the org, project, and workspace for your app, and then choose what you want it to include - serverless actions, web UI assets, CI/CD pipeline, and events. In this Code Lab, we select all except events.

Next, select the sample actions to be created as part of the initial app. Since customer profiles are pulled from Campaign Standard, we are going to select `Adobe Campaign Standard` by pressing `<Space>` to select or de-select, and `<Enter>` to confirm the choice.

You will be asked to specify the name of the sample action. Name it `get-profiles`:

![app-init](assets/app-init-1.png)

Next, create a NodeJS project from the command line, including its Adobe I/O Runtime actions, configuration files, tests, and so on. Explore your project in VS Code, either by opening VSCode -> Open... -> select app folder, or entering:

```bash
code customers-dashboard
```

![app-explore](assets/app-explore.png)

Upon app initialization, some of the mandatory environment variables are automatically defined in the `.env` file, namely `AIO_runtime_namespace`, `AIO_runtime_auth`, and `SERVICE_API_KEY`. If they are not set, you can download the Runtime credentials and obtain the API key from I/O Console:  

![acs-api-key](assets/acs-api-key.png)

For Campaign Standard integration, you must also set the required variable for `CAMPAIGN_STANDARD_TENANT`. This is usually the subdomain of your Campaign Standard instance. If it's not, ask your Campaign TechOps team the correct value.

![acs-tenant](assets/acs-tenant.png)

All set environment variables should then be uncommented:

![dot-env](assets/dot-env.png)
