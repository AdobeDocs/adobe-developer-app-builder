---
keywords:
  - Adobe I/O
  - Extensibility
  - API Documentation
  - Developer Tooling
title: CI/CD for App Builder Applications
---

# CI/CD for App Builder Applications

Continuous integration and continuous delivery (CI/CD) is a crucial component for the success of any development team, including one on App Builder applications. By automating deployment, CI/CD allows the development team to focus on requirements, code quality, and security; it is one of the best practices for teams to implement, and for us to support. 

App Builder allows you to manage multiple environments through Workspace in Adobe Developer Console. Every new application project created by an entitled organization Administrator or Developer in the Console will be set up with two named workspaces, Stage and Production, and each workspace will have its own setup with:

- Adobe services to integrate through user or technical user accounts
- Runtime namespace
- Cloud storage for the Single-Page Application static files 
- CDN delegation and sub-domain provisioned on `adobeio-static.net`
- Related credentials and secrets, including API Key, access token, and Runtime namespace credentials

Users at entitled organizations will also be allowed to create as many additional workspaces as their project requires, whether to add extra stages (QA, Preproduction, etc.), to match infrastructure requirements, or to define Developer-specific workspaces for each team member to work against locally.

![High-Level CI/CD architecture](../../../images/high-level-ci-cd-architecture.png)

## Local application development

A project might have several Developers working on the same application code. They may clone the code from the same branch, or create different branches for new features, but deployments should differ so they don't interfere with one another. Each Developer can then use individual workspaces as described above to create their own deployments. 

Prerequisites are application code cloned to the local machine and the workspace configuration JSON file downloaded from Adobe Developer Console. Once those conditions are met, navigate in the terminal to the home directory of the application code and run:

```bash
aio app use path/to/workspace.json
```

Check to see that the workspace is set up for the app by verifying the `.aio` and `.env` files, which contain the target workspace details.

## GitHub actions support

A sample CI/CD workflow is included With App Builder on top of [GitHub Actions](https://github.com/features/actions).
When a new App Builder application is bootstrapped from the [CLI](https://github.com/adobe/aio-cli) using the `aio app init` command, the application generator asks the Developer whether to `include GitHub Actions based workflows for Build, Test and Deploy`.

If the Developer selects this option, the application code will be initialized with an additional `.github` folder at its root. This folder contains default [GitHub Workflows](https://github.com/adobe/generator-aio-app/tree/master/generators/add-ci/.github/workflows) that can be extended at the application level depending on the Developer's needs.

### Github workflows

The default GitHub workflows support the following actions, based on the Github events triggered on the application repository:

- On `Pull Request`, the application unit tests are executed by calling `aio app test` against the requested changes. See the [PR workflow](https://github.com/adobe/generator-aio-app/blob/master/generators/add-ci/.github/workflows/pr_test.yml).

- On `Branch Merge`, the application is deployed to the `Stage` workspace by calling `aio app deploy`. Back-end serverless actions are deployed to Runtime, and the SPA gets to the CDN if the application has a web UI. See the [Deploy Stage workflow](https://github.com/adobe/generator-aio-app/blob/master/generators/add-ci/.github/workflows/deploy_stage.yml).

- On `Repository Release`, the application is deployed to the `Production` workspace by calling `aio app deploy`. The back-end serverless actions are deployed to Runtime, and the SPA to the CDN if the application has a web UI. See the [Deploy Prod workflow](https://github.com/adobe/generator-aio-app/blob/master/generators/add-ci/.github/workflows/deploy_prod.yml).

Each of the default Github Workflows uses two core features provided by Github: [Github Actions](https://github.com/features/actions) and [Github Secrets](https://help.github.com/en/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets).

![CI/CD with Github Actions](../../../images/ci-cd-github-actions-architecture.png)

### GitHub actions for AIO CLI

These GitHub Actions have been built to support the use of the CLI in a CI/CD workflow running within GitHub infrastructure. They are used in the default [App Builder Apps workflows](https://github.com/adobe/generator-aio-app/tree/master/generators/add-ci/.github/workflows), and can also be used in custom GitHub workflows built by Developers to fulfil project needs:

- [CLI Setup Action](https://github.com/adobe/aio-cli-setup-action) can be used to install and configure the CLI on the GitHub infrastructure running the workflow that invoked the action

- [Apps Action](https://github.com/adobe/aio-apps-action) centralizes support for a GitHub workflow to use application-specific commands such as testing using `aio app test` and deployment using `aio app deploy`.

Both actions have been published and can be found on GitHub Marketplace. See [CLI Setup](https://github.com/marketplace/actions/aio-cli-setup) and [Apps](https://github.com/marketplace/actions/aio-apps).

### GitHub secrets

The following GitHub Actions use GitHub Secrets to store environment-specific secrets. Currently, an Administrator must add these secrets to the application repository manually:

- **AIO_RUNTIME_NAMESPACE_STAGE**, the name of the Runtime namespace associated with the `Stage` App Builder workspace
- **AIO_RUNTIME_AUTH_STAGE**, credentials for the Runtime namespace associated with the `Stage` App Builder workspace
- **AIO_RUNTIME_NAMESPACE_PROD**, the name of the Runtime namespace associated with the `Prod` App Builder workspace
- **AIO_RUNTIME_AUTH_PROD**, the credentials for the Runtime namespace associated with the `Prod` App Builder workspace

We plan to simplify this configuration process in the future. 

## Bring your own CI/CD pipeline

The default implementation of the CI/CD workflow for App Builder Applications relies on GitHub capabilities. However, Developers might need an alternative solution due to project requirements or team preferences. If so, we recommend that the custom solution focus on:

- The CLI, the official tool to manage the development lifecycle of App Builder applications from bootstrapping to deployment. It can be used within CI/CD workflows for automation purposes.
- Security, a key requirement: any alternative CI/CD workflow should include a solid secret-management solution to store credentials needed to deploy an App Builder Application against a specific workspace.

## Next steps

Continue to [Credential Rotation](credential_rotation.md).

Return to [Deployment Overview](deployment.md).

Return to the [Guides Index](../../guides_index.md).
