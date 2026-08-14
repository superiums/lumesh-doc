---
title: 内置库 time
date: 2026-08-14 11:29:46
---

## Builtin Functions for Lib: time

- `add [datetime] <duration>`
	向日期时间添加有符号持续时间字符串（例如 '1d2h30m', '-1h'）或整数秒（默认为现在）

- `day [datetime]`
	获取月份中的第几天（1-31）

- `diff <datetime1> [datetime2] <unit>`
	计算两个日期时间在指定单位下的差异

- `display [datetime]`
	获取预格式化的日期时间，返回带有时间/日期/日期时间等字段的映射

- `fmt [datetime] <format_string>`
	使用 chrono 格式字符串格式化日期时间（当前或指定）

- `hour [datetime]`
	获取小时（0-23）

- `is_leap [year]`
	检查某年是否为闰年

- `minute [datetime]`
	获取分钟（0-59）

- `month [datetime]`
	获取月份（1-12）

- `now [format_string]`
	获取当前日期时间作为 DateTime 对象或格式化字符串

- `parse <datetime_string> [format_string]`
	解析日期时间字符串，可选择使用 chrono 格式字符串

- `second [datetime]`
	获取秒（0-59）

- `seconds [datetime]`
	获取自午夜以来的秒数

- `sleep <duration>`
	休眠指定的毫秒数 [ms] 或持续时间字符串（例如 '1s', '2m'）

- `stamp [datetime]`
	获取 Unix 时间戳（秒）

- `stamp_ms [datetime]`
	获取 Unix 时间戳（毫秒）

- `timezone [datetime] <offset_hours> [format_string]`
	将日期时间转换为不同时区偏移量（小时）

- `to_string <datetime> [format_string]`
	将 DateTime 转换为字符串

- `weekday [datetime]`
	获取星期几（1-7，周一=1）

- `year [datetime]`
	获取年份（当前或从指定日期时间获取）
