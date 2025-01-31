---
keywords:
  - CLI
  - Extensibility
  - Developer Tooling
  - telemetry
title: Telemetry
description: The latest release of the Adobe Developer Platform CLI (v9.0.0) collects anonymous telemetry data about general usage.  This is an optional feature, and you may opt-out if you would not like to share your anonymous usage information.
---

# Telemetry

The latest release of the Adobe Developer Platform CLI (v9.0.0) collects anonymized telemetry data about usage. This is an optional feature; you may opt out if you do not care to share anonymous usage information.

## Why collect telemetry?

The Command Line Interface (CLI) has grown from a small project developed by a small team to a tool that is used and supported by many teams inside and outside of Adobe.  As it has grown, we have tried to maintain consistency across operating systems, node versions, terminal choices, and so on.  Telemetry allows us to observe feature usage so we can focus on removing pain points as we try to maintain a first-class Developer experience.

## What data is collected?

Our primary goal is performance, so in addition to tracking which commands are run and whether they are successful, we keep track of how long each command runs.

The data collected includes:

- The command being run, with any aliases used
- Any flags used, but not the values of the flags 
- How long each command ran, and whether it was successful
- Information about the environment, including operating system, node version and CLI version

## What about secrets and sensitive data?

We __never__ collect any that might be sensitive, including but not limited to command arguments and flag values, file paths, environment variables, logs, stack traces, or error output.

<InlineAlert slots="text" />

For more information please read our [privacy policy](https://www.adobe.com/privacy.html).

## Will this data be shared? With whom?

The data we collect is anonymized, and meaningful only in aggregate. We may share non-personally identifiable aggregate data with other teams at Adobe to help them develop or improve their CLI plugins.  Some information may be also shared with the Developer community, this will usually be high-level information such as the popularity of an operating system or node version.

## How do I opt out?

You may turn telemetry off or on at any time; this setting will persist until you change it.

```bash
➜ aio telemetry off
Telemetry is off.
If you would like to turn telemetry on, simply run `aio telemetry on`

➜ aio telemetry on
Telemetry is on! Nice, you are helping us improve Adobe Developer CLI
If you would like to turn telemetry off, simply run `aio telemetry off`
```

You can also check telemetry status:

```bash
➜ aio telemetry
Telemetry is on! Nice, you are helping us improve Adobe Developer CLI
If you would like to turn telemetry off, simply run `aio telemetry off`
```

Individual commands may be run with telemetry off by using the `--no-telemetry` flag, for example:

`aio app info --no-telemetry`

## Show me the code

Telemetry itself is implemented as a CLI plugin.  It is developed in the open and released under the Apache 2.0 license.  If you would like to watch, review, or contribute to the CLI project please visit [aio-CLI-plugin-telemetry](https://github.com/adobe/aio-CLI-plugin-telemetry): pull requests are welcome.

## Next steps

Review Adobe policies and guidelines for [contributions](../contribution_guide.md).

Return to the [Guides Index](../guides_index.md).
