# Set up Your Environment

## Get access

Adobe I/O Runtime is part of Adobe App Builder. Customers and partners who want to try Adobe I/O Runtime along with the storage, custom events, and other features of App Builder may sign up for an [App Builder trial](https://commerce.adobe.com/business-trial/sign-up?items%5B0%5D%5Bid%5D=649A1AF5CBC5467A25E84F2561274821&cli=headless_exl_banner_campaign&co=US&lang=en).

## Install the Adobe I/O (aio) command-line interface (CLI)

To install `aio`, you'll need the `npm` package manager. Make sure that the latest versions of `npm` and `Node.js` are both installed, and then install `aio`:

`npm install -g @adobe/aio-cli`

Then confirm that `aio` installed properly:

`aio help`

### Create a namespace and retrieve credentials

Organizations with access to I/O Runtime manage namespaces from the [Developer Console](https://developer.adobe.com/developer-console/). Namespace management requires the Developer role or System Administrator permissions.

In the Developer Console:

* Create a new `Project`.
* Select a workspace option, for example `Production`.
* Click `Add service` and choose `Runtime`.
* Navigate back to `Workspace overview` page and click on the `Download all` button at the top of the page. This will download the configuration file for this project and workspace.
* Open the configuration file in a text editor and search for the `runtime` > `namespaces` entry. Here you will find the namespace `name` and `auth` values you will use to configure the `aio` CLI. 

### Sign in from the CLI

There are two ways to configure the `aio` CLI:

* **With Developer Role or System Administrator permissions**, sign in to the `aio` CLI as shown [here](../app_builder_get_started/first_app.md#3-sign-in-from-the-CLI), then use it to retrieve the projects you created in the Developer Console and select the workspace you want to work in. 

* **If you lack the right permissions** but have a namespace and authorization for it, manually configure the `aio` CLI as shown in the next section.

### Configure aio CLI

The `aio` CLI will pick up credentials from an `.env` file in the current working directory.

## Test that the CLI is set up correctly

Once the CLI is configured, test it:

`aio runtime list`

A successful test will display a list of the entities defined in your namespace.

## Next step

With the CLI installed and tested, you're ready to [deploy your first function](deploy.md).
