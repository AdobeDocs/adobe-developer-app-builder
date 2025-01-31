# Troubleshooting

If you're having trouble with your actions or activations, here is a common issue and its solution.

## The pod exceeded local storage and was evicted with X in-flight activations

The most common reasons for exceeding local storage for an action are:

- Writing too many verbose logs
- Storing large files on local storage without cleaning them up

Please refer to the local storage limit configured for an action, found in [System Settings](./system_settings.md) section.
Exceeding this limit will result in the action being terminated or not completing successfully.

### Mitigation Strategies

- Logging: Use the [@adobe/aio-sdk](https://github.com/adobe/aio-lib-core-logging) library for logging. Make sure to use the `DEBUG` logging level for development, and set your production `AIO_LOG_LEVEL` env variable to `INFO` or `WARN`. This approach minimizes storage use while retaining critical log information necessary for troubleshooting.
- Logging: Avoid verbose logging and log only the necessary information.
- File Management: Always clean up files written to disk after function execution. Neglecting this can lead to storage bloat, preventing future actions from running as expected.

## Next step

Review [System Settings](system_settings.md) and defaults.

Return to [Guides Index](../guides_index.md).
