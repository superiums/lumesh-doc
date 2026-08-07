---
title: Builtin Lib TIME
date: 2026-08-07 16:40:11
---
	
## Builtin Functions for Lib: time

- `add [datetime] <duration>`
	add a signed duration string (e.g. '1d2h30m', '-1h') or integer seconds to a datetime (defaults to now)

- `day [datetime]`
	get day of month (1-31)

- `diff <datetime1> [datetime2] <unit>`
	calculate difference between two datetimes in given unit

- `display [datetime]`
	get preformatted datetime as map with time/date/datetime/etc.

- `fmt [datetime] <format_string>`
	format datetime (current or specified) using chrono format string

- `hour [datetime]`
	get hour (0-23)

- `is_leap [year]`
	check if a year is a leap year

- `minute [datetime]`
	get minute (0-59)

- `month [datetime]`
	get month (1-12)

- `now [format_string]`
	get current datetime as DateTime object or formatted string

- `parse <datetime_string> [format_string]`
	parse datetime string, optionally with a chrono format string

- `second [datetime]`
	get second (0-59)

- `seconds [datetime]`
	get seconds since midnight

- `sleep <duration>`
	sleep for a given number of milliseconds [ms] or duration string (e.g. '1s', '2m')

- `stamp [datetime]`
	get Unix timestamp in seconds

- `stamp_ms [datetime]`
	get Unix timestamp in milliseconds

- `timezone [datetime] <offset_hours> [format_string]`
	convert datetime to a different timezone offset (in hours)

- `to_string <datetime> [format_string]`
	convert DateTime to string

- `weekday [datetime]`
	get weekday (1-7, Monday=1)

- `year [datetime]`
	get year (current or from specified datetime)

