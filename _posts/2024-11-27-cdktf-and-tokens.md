CDKTF and Tokens

I ran into a problem using cdktf and DataDog where I was trying to create a downtime
for a specific monitor using it's id.  The problem was that the downtime monitor_id
attribute requires a number and the monitor.id attribute is a string.  This lead
to the error that the downtime schedule monitor identifier expected a number but
got a string:
`TypeError: type of argument monitor_id must be one of (int, float, NoneType); got str instead`
I first tried to use `int()` and `float()` but that got an error that the value wasn't a
number:
`ValueError: invalid literal for int() with base 10: '${TfToken[TOKEN.17]}'`

After much googling and trying to figure it out, I discovered that cdk and thus cdktf
has a token system for values not known till apply and so when I was trying to use
`int()` and `float()` it was before the token was replaced with the value so failing.
That was when I discovered there are functions you can use on the token to convert it to
a number or a list: `Token.as_number(request_count_monitor.id)`.  So my final code looked
like this:
```
DowntimeSchedule(
  self, f'downtime', scope='env:prod', display_timezone=timezone,
  recurring_schedule=DowntimeScheduleRecurringSchedule(
    timezone=timezone, recurrence=recurrence), monitor_identifier=DowntimeScheduleMonitorIdentifier(
      monitor_id=Token.as_number(request_count_monitor.id)))
```

Sources:
* https://developer.hashicorp.com/terraform/cdktf/concepts/tokens
* https://docs.aws.amazon.com/cdk/v2/guide/tokens.html
