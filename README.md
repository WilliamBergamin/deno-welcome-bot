# Welcome Bot - Next Generation Sample App

This sample app is a Welcome Bot that helps create, store and send friendly
welcome messages when a user joins a channel. If you are new to next generation
apps then you've come to the right place!

**Guide Outline**:

- [Supported Workflows](#supported-workflows)
- [Setup](#setup)
  - [Install the Slack CLI](#install-the-slack-cli)
  - [Clone the Sample App](#clone-the-sample-app)
- [Understanding Welcome Bot](#understanding-welcome-bot)
- [Create a Link Trigger](#create-a-link-trigger)
- [Running Your Project Locally](#running-your-project-locally)
- [Testing](#testing)
- [Deploying Your App](#deploying-your-app)
  - [Viewing Activity Logs](#viewing-activity-logs)
- [Project Structure](#project-structure)
- [Resources](#resources)

---

## Supported Workflows

- **Welcome Message Setup**: Create and store a welcome message for a specified
  channel.
- **Send Welcome Message**: Retrieve stored message and send it when a new user
  joins the channel.

## Understanding Welcome Bot

When working with this app it helps to think about it as two separate series of
steps.

<img src="assets/deno-welcome-bot-flow.png" alt="Welcome bot flow diagram" width="500">

### Creating and storing messages

- A link trigger starts the `MessageSetupWorkflow` workflow.
- The `MessageSetupWorkflow` workflow has three steps, steps are the action
  components of a workflow.
  1. The `OpenForm` built-in function that opens a form.
  2. The `SendEphemeralMessage` built-in function that sends a confirmation
     message.
  3. Passes data to the `WelcomeMessageSetupFunction` custom function.
- When the form is submitted, the `WelcomeMessageSetupFunction` function saves
  the message to the datastore and creates an event trigger to listen in on
  `user_joined_channel` events in the specified channel.

### Sending messages

- The `user_joined_channel` event trigger starts the
  `SendWelcomeMessageWorkflow` workflow.
- The `SendWelcomeMessageWorkflow` workflow has one additional step:
  1. Pass data to the `SendWelcomeMessageFunction` custom function.
- The `SendWelcomeMessageFunction` function retrieves the saved message and
  sends it to the selected channel.

## Setup

Before getting started, make sure you have a development workspace where you
have permissions to install apps. If you don’t have one set up, go ahead and
[create one](https://slack.com/create).

### Install the Slack CLI

To use this sample, you first need to install and configure the Slack CLI.
Step-by-step instructions can be found in our
[Quickstart Guide](https://api.slack.com/future/quickstart).

### Clone the Sample App

Start by cloning this repository:

```zsh
# Clone this project onto your machine
$ slack create my-welcome-bot -t slack-samples/deno-welcome-bot

# Change into this project directory
$ cd my-welcome-bot
```

## Create a Link Trigger

[Triggers](https://api.slack.com/future/triggers) are what cause Workflows to
run. These Triggers can be invoked by a user, or automatically as a response to
an event within Slack.

A [Link Trigger](https://api.slack.com/future/triggers/link) is a type of
Trigger that generates a **Shortcut URL** which, when posted in a channel or
added as a bookmark, becomes a link. When clicked, the Link Trigger will run the
associated Workflow.

Link Triggers are _unique to each installed version of your app_. This means
that Shortcut URLs will be different across each workspace, as well as between
[locally run](#running-your-project-locally) and
[deployed apps](#deploying-your-app). When creating a Trigger, you must select
the Workspace that you'd like to create the Trigger in. Each Workspace has a
development version (denoted by `(dev)`), as well as a deployed version.

To create a Link Trigger for the "Message Setup" Workflow, run the following
command:

```zsh
$ slack trigger create --trigger-def triggers/create_welcome_message_shortcut.ts
```

After selecting a Workspace, the output provided will include the Link Trigger
Shortcut URL. Copy and paste this URL into a channel as a message, or add it as
a bookmark in a channel of the Workspace you selected.

**Note: this link won't run the Workflow until the app is either running locally
or deployed!** Read on to learn how to run your app locally and eventually
deploy it to Slack hosting.

## Running Your Project Locally

While building your app, you can see your changes propagated to your workspace
in real-time with `slack run`. In both the CLI and in Slack, you'll know an app
is the development version if the name has the string `(dev)` appended.

```zsh
# Run app locally
$ slack run

Connected, awaiting events
```

Once running, click the
[previously created Shortcut URL](#create-a-link-trigger) associated with the
`(dev)` version of your app. This should start the Message Setup Workflow.

To stop running locally, press `<CTRL> + C` to end the process.

## Testing

Test filenames should be suffixed with `_test`. Run all tests with `deno test`:

```zsh
$ deno test
```

## Deploying Your App

Once you're done with development, you can deploy the production version of your
app to Slack hosting using `slack deploy`:

```zsh
$ slack deploy
```

After deploying, [create a new Link Trigger](#create-a-link-trigger) for the
production version of your app (not appended with `(dev)`). Once the Trigger is
invoked, the Workflow should run just as it did in when developing locally.

### Viewing Activity Logs

Activity logs for the production instance of your application can be viewed with
the `slack activity` command:

```zsh
$ slack activity
```

## Project Structure

### `manifest.ts`

The [app manifest](https://api.slack.com/future/manifest) contains the app's
configuration. This file defines attributes like app name and description.

### `slack.json`

Used by the CLI to interact with the project's SDK dependencies. It contains
script hooks that are executed by the CLI and implemented by the SDK.

### `/functions`

[Functions](https://api.slack.com/future/functions) are reusable building blocks
of automation that accept inputs, perform calculations, and provide outputs.
Functions can be used independently or as steps in Workflows.

### `/workflows`

A [Workflow](https://api.slack.com/future/workflows) is a set of steps that are
executed in order. Each step in a Workflow is a function.

Workflows can be configured to run without user input or they can collect input
by beginning with a [form](https://api.slack.com/future/forms) before continuing
to the next step.

### `/triggers`

[Triggers](https://api.slack.com/future/triggers) determine when Workflows are
executed. A trigger file describes a scenario in which a workflow should be run,
such as a user pressing a button or when a specific event occurs.

### `/datastores`

[Datastores](https://api.slack.com/future/datastores) can securely store and
retrieve data for your application. Required scopes to use datastores include
`datastore:write` and `datastore:read`.

## Resources

To learn more about developing with the CLI, you can visit the following guides:

- [Creating a new app with the CLI](https://api.slack.com/future/create)
- [Configuring your app](https://api.slack.com/future/manifest)
- [Developing locally](https://api.slack.com/future/run)

To view all documentation and guides available, visit the
[Overview page](https://api.slack.com/future/overview).
