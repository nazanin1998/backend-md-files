# 01 How to Convert Local Date Time To Another Zone

In some cases it is needed to convert local-date-time value to another time zone.
The following code is how this is done.

```
// source code:
// https://stackoverflow.com/a/38376576

LocalDateTime now = LocalDateTime.now();
ZonedDateTime nowZoned = now.atZone(ZoneId.systemDefault());
ZonedDateTime utcZoned = nowZoned.withZoneSameInstant(ZoneId.of("UTC"));
System.out.println("time in utc: " + utcZoned);
```