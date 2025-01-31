---
keywords:
  - Acquire Access
  - Credentials
  - Local Environment
  - Set up
title: Set Up Access, Environment, and Tools
description: App Builder is a complete framework that enables enterprise developers to build and deploy custom web applications that extend Adobe Experience Cloud solutions and run on Adobe infrastructure.
---

import NPMBadge from '../../../components/NPMBadge.js'

# Set Up Access, Environment, and Tools

Here you'll learn what systems you need to access, how to access them, and how to configure your local environment.

## Access and credentials

**Adobe Experience Cloud Identity Management Service (IMS) organization** membership, with a Developer or System Admin role, is needed to access App Builder.

* If your organization has a license for App Builder, use the IMS organization selected by the person who purchased it

* If you are using App Builder as a trial, use the IMS organization that you requested or was assigned to you when the trial was initiated
- If you do not have access to an organization:
  
  - Customers should request access from their account manager
  
  - Partners should request App Builder access from their partner manager, or Sandbox access though the [Adobe Solution Partner Portal](https://solutionpartners.adobe.com/home.html)

**App Builder access** is available under a purchased license or as a trial. For example, the AEM Headless trial includes App Builder.

**[Adobe Developer Console](https://developer.adobe.com/developer-console/)** gives you access to APIs, SDKs, and developer tools. It also provides a way to set up your credentials.

**[A GitHub account](https://github.com/)** is optional but highly recommended for setting up your CI/CD workflow.

## Local environment setup

### Required tools

[**NodeJS**](https://nodejs.org/en/download/), version 18 or 20 (odd versions are not recommended), which will also install the npm package manager. We recommend [nvm](https://github.com/nvm-sh/nvm/blob/master/README.md) for NodeJS installation and version management.

[**Adobe I/O Command Line Interface (CLI)**](https://github.com/adobe/aio-cli)

- `npm install -g @adobe/aio-cli`
- If Adobe I/O CLI is already installed, please make sure you have the latest version: <NPMBadge/>
  - Check your CLI version using `aio -v` and compare it to`npm show @adobe/aio-cli version`. If your CLI is outdated, update it by running `npm install -g @adobe/aio-cli`.
  - Even if your Adobe I/O CLI is up to date, run `aio update` to make sure all core plugins are updated.
  - For seven days after release of a new version of Adobe I/O CLI, you'll see a message like this before the command output:

```bash
›   Warning: @adobe/aio-cli update available from 3.3.0 to 3.4.1.
›   Run npm install -g @adobe/aio-cli to update.
```

#### Supported local environments

We aim to provide similar quality of local development experience on Windows 10 and 11, and MacOS 10.14 and higher.
[Adobe I/O CLI](https://github.com/adobe/aio-cli) and its plugins are automatically tested against NodeJS versions 14 and 16 on both Windows and [Linux Xenial](http://releases.ubuntu.com/16.04/).

#### Supported terminals for the CLI

The [CLI](https://github.com/adobe/aio-cli) uses the popular [inquirer](https://www.npmjs.com/package/inquirer) package for all its interactive functions such as application generators.

See [inquirer's Support section](https://www.npmjs.com/package/inquirer#support-os-terminals) and its [known issues](https://www.npmjs.com/package/inquirer#known-issues) for up-to-date details.

### Optional tool

If you intend to use local development (`aio app dev`) features provided by the [CLI](https://github.com/adobe/aio-cli), you will also need:

- [Visual Studio Code](https://code.visualstudio.com/download), the supported integrated development environment (IDE) for editing, debugging, etc. You may use any other IDE as a code editor, but advanced usage like debugging is not yet supported.

## Next step

Now that you have your environment set up, you can start to [Create your First App Builder Application](first_app.md).
