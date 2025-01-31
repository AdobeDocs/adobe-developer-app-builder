---
keywords:
  - Adobe I/O
  - Extensibility
  - API Documentation
  - Developer Tooling
  - Logging
  - Log Forwarding
  - Monitoring
  - Azure
  - Azure Log Analytics
title: Forwarding Logs to Azure Log Analytics
---

# Forwarding Logs to Azure Log Analytics

This guide covers configuration of your App Builder application to forward logs to your Azure Log Analytics workspace.

## Prerequisites

1. Access to an Azure Log Analytics workspace. If you need to create one, follow [Azure's guide](https://docs.microsoft.com/en-us/azure/azure-monitor/logs/quick-create-workspace)
2. Local development setup for your App Builder application
3. The latest version of AIO CLI. Check by running `aio --version`; update by running `npm install -g @adobe/aio-cli`

## Steps to configure log forwarding

1. Open Azure Portal and navigate to the Azure Log Analytics workspace you want to use. On the workspace overview screen, pick Agent Management from the menu on the left. Copy the **`Workspace ID`** and the **`Primary Key`** for later use.

2. Open Terminal and navigate to the App Builder project directory on your local machine.

3. Run this command, supplying the values copied in step 1 when prompted:
   
   ```
   aio app config set log-forwarding
   ? select log forwarding destination: Azure Log Analytics
   ? customer ID: <Workspace ID>
   ? shared key: <Primary Key>
   ? log type: <table_name_alpha_chars_only>
   ```
   
    Note:
   
   + Replace the value of `Workspace ID` and `Primary Key` as copied in step 1.
   + Replace the value of `table_name_alpha_chars_only` with the custom table name that you wish to provide. This field accepts only alphabetic characters.

4. Verify that the configuration change has taken place:
   
   ```
   aio app config get log-forwarding
   ```

5. Execute an action in your App Builder application workspace to generate logs.

6. On Azure Portal and the overview screen of your workspace, select Logs on the left menu. Run a query using the table name you provided in step 3. Note that the table name that shows up on the Azure portal will have the suffix **`_CL`** appended to the table name you provided.

7. If you don't see any logs in Azure, please check for log forwarding errors:
   
   ```
   aio app config get log-forwarding errors
   ```
   
   ## Next steps
   
   If you are unable to set up log forwarding using these procedures, please visit Adobe [App Builder forums](https://experienceleaguecommunities.adobe.com/t5/app-builder/ct-p/app-builder) for support.
   
   Return to [Managing Application Logs](logging.md).
   
   Return to [Guides Index](../../guides_index.md).
