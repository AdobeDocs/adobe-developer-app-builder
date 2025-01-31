---
keywords:
  - Adobe I/O
  - Extensibility
  - API Documentation
  - Developer Tooling
title: 'Lesson 3: Custom CI/CD workflow'
---

# Lesson 3: Custom CI/CD workflow

## Setting environment variables

You can add additional environment variables to the test and deploy GitHub workflows see the [GitHub documentation](https://docs.github.com/en/free-pro-team@latest/actions/reference/environment-variables) for more information.

In our case, if you want to add for example environment values named `MY_ENV_VALUE_1` and `MY_ENV_VALUE_2` to the stage and production deploy workflow, follow these steps:

1. First define the environment variables as input to your Runtime action in the `app.config.yaml` e.g. :

```yml
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
              my_value_1: $MY_ENV_VALUE_1
              my_value_2: $MY_ENV_VALUE_2
            annotations:
              require-adobe-auth: true
              final: true
```

2. Add the corresponding GitHub secrets for example `MY_ENV_VALUE_1_SECRET` and `MY_ENV_VALUE_2_SECRET`.
3. Edit the stage and prod deploy workflows in your project under `.github/workflow/deploy_stage.yml` and `.github/workflow/deploy_prod.yml` to map the GitHub secrets to the environment values: 

```
...
- name: Deploy
  uses: adobe/aio-apps-action@1.0.0
  with:
    os: ${{ matrix.os }}
    command: deploy
    AIO_RUNTIME_AUTH: ${{ secrets.AIO_RUNTIME_AUTH }}
    AIO_RUNTIME_NAMESPACE: ${{ secrets.AIO_RUNTIME_NAMESPACE }}
  env:
    MY_ENV_VALUE_1: ${{ secrets.MY_ENV_VALUE_1_SECRET }}
    MY_ENV_VALUE_2: ${{ secrets.MY_ENV_VALUE_2_SECRET }}
```

So with that, you are setting environment variables in the GitHub CI/CD workflow that can be accessed inside your Adobe I/O Runtime function: 

```javascript
async function main (params) {
  // ...
  console.log(params.my_value_1);
  console.log(params.my_value_2);
  // ...
}
```

## Custom use cases

The default implementation of the CI/CD workflow for App Builder Applications relies on GitHub capabilities. 
Unfortunately, GitHub actions are not supported on GitHub Enterprise yet.

In the case you can't use the GitHub Actions, we recommend implementing a custom CI/CD workflow with focus on two main aspects:

* The [CLI](https://github.com/adobe/aio-cli) is the official tool to manage the App Builder Application development lifecycle from bootstrapping to deployment, and can be used within a CI/CD workflow for automation purpose.
* Security is a key requirement, and any alternative CI/CD workflow should propose a solid secret management solution to store the credentials required to deploy an App Builder Application against a specific **Workspace**.
