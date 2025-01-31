# Pre-Installed Packages

These packages are pre-installed and available to any I/O Runtime user:

```
/whisk.system/alarms                                                  
```

You can get details about the content of a package by running this command:

```
aio rt:package:get /whisk.system/alarms
```

You can execute any of the actions defined in a shared package by using the fully qualified name. For example:

```
aio rt:action:invoke /whisk.system/alarms/alarm
```

Alternatively, you can bind any of these packages to your namespace; see the [Packages](packages.md) page. When you bind a package, you can set your own default parameters (if any) and run the actions as local to your namespace.

## Using the alarms package

The alarms package can be used to trigger a actions for a single or recurring interval. This is a three-step process:

1. Create one or more actions you want to be executed. There is nothing special about these actions; create them as you would any other.
2. Create a trigger and specify the frequency. This is what the Alarms package provides.
3. Create a rule that links every action to a trigger Every time the trigger is fired, its linked action is executed.

The alarms package contains:

| Entity                          | Type    | Parameters                                            | Description                                            |
| ------------------------------- | ------- | ----------------------------------------------------- | ------------------------------------------------------ |
| `/whisk.system/alarms`          | package | -                                                     | Alarms utility                                         |
| `/whisk.system/alarms/interval` | feed    | `minutes`, `trigger_payload`, `startDate`, `stopDate` | Fire Trigger event on an interval-based schedule       |
| `/whisk.system/alarms/once`     | feed    | `date`, `trigger_payload`, `deleteAfterFire`          | Fire Trigger event once on a specific date             |
| `/whisk.system/alarms/alarm`    | feed    | `cron`, `trigger_payload`, `startDate`, `stopDate`    | Fire Trigger event on a time-based schedule using cron |

To create a trigger named `my-interval` on an interval-based schedule that will fire every 10 minutes until January 31, 2028:

```
aio rt:trigger:create my-interval \
  --feed /whisk.system/alarms/interval \
  --param minutes 10 \
  --param trigger_payload "{\"name\":\"Vlad\",\"place\":\"Transylvania\"}" \
  --param stopDate "2028-01-31T23:59:00.000Z"
```

To create a trigger that fires once:

```
aio rt:trigger:create my-interval \
  --feed /whisk.system/alarms/once \
  --param trigger_payload "{\"name\":\"Vlad\",\"place\":\"Transylvania\"}" \
  --param stopDate "2028-01-31T23:59:00.000Z"
  --param deleteAfterFire "rules"
```

Finally, to create a trigger that fires every hour on a time-based schedule using cron:

```
aio rt:trigger:create my-interval \
  --feed /whisk.system/alarms/alarm \
  --param cron "0 * * * *" \
  --param trigger_payload "{\"name\":\"Vlad\",\"place\":\"Transylvania\"}" \
  --param startDate "2025-10-17" \
  --param stopDate "2026-10-31T23:59:00.000Z"
```

Here are some examples of how to set various `cron` values - for more information, check this [page](http://crontab.org):

- `* * * * *`: The Trigger fires at the top of every minute
- `0 * * * *`: The Trigger fires at the top of every hour
- `0 */2 * * *`: The Trigger fires every 2 hours (that is, 02:00:00, 04:00:00, ...)
- `0 9 8 * *`: The Trigger fires at 9:00:00AM (UTC) on the eighth day of every month

## Next steps

Return to the [Runtime Reference Documentation Index](index.md).

Return to the [Guides Index](../../guides_index.md).
