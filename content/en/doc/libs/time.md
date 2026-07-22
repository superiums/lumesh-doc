---
title: Lumesh Time Module  
date: 2025-07-13 19:16:45  
highlight: true  
tags:  
 - libs  
 - datetime  
categories:  
 - wiki  
 - libs  
---

**Module Path**: `time`  
Use `help time` to view help.

**Function Description**: Provides functionalities for obtaining, parsing, calculating, and formatting time.

---

### **Function Directory**

| Function Category     | Function Name     | Calling Format                          | Core Functionality                     |
|-----------------------|-------------------|-----------------------------------------|----------------------------------------|
| Time Retrieval        | `now`             | `time.now [format]`                    | Get the current time (as an object or formatted string) |
| Time Retrieval        | `display`         | `time.display`                          | Get pre-formatted time information (mapping of various formats) |
| Time Retrieval        | `stamp`           | `time.stamp [datetime]`                | Get Unix timestamp (in seconds)       |
| Time Retrieval        | `stamp_ms`        | `time.stamp_ms [datetime]`             | Get Unix timestamp (in milliseconds)   |
| Time Parsing and Formatting | `parse`    | `time.parse [format] datetime_str`     | Parse a time string into a time object |
| Time Parsing and Formatting | `fmt`      | `time.fmt format_str [datetime]`       | Format a time object as a string       |
| Time Parsing and Formatting | `to_string` | `time.to_string datetime [format]`      | Convert a time object to a string (default RFC3339) |
| Time Arithmetic       | `add`             | `time.add duration [base_time]`        | Add a duration to a time               |
| Time Arithmetic       | `diff`            | `time.diff time1 time2 unit`           | Calculate the difference between two times (specifying unit) |
| Time Arithmetic       | `timezone`        | `time.timezone datetime offset_hours`   | Convert timezone (by hour offset)      |
| Time Component Retrieval | `year`         | `time.year [datetime]`                 | Get the year                           |
| Time Component Retrieval | `month`        | `time.month [datetime]`                | Get the month (1-12)                   |
| Time Component Retrieval | `weekday`      | `time.weekday [datetime]`              | Get the day of the week (1-7, Monday is 1) |
| Time Component Retrieval | `day`          | `time.day [datetime]`                  | Get the date (1-31)                   |
| Time Component Retrieval | `hour`         | `time.hour [datetime]`                 | Get the hour (0-23)                   |
| Time Component Retrieval | `minute`       | `time.minute [datetime]`               | Get the minute (0-59)                 |
| Time Component Retrieval | `second`       | `time.second [datetime]`               | Get the second (0-59)                 |
| Time Component Retrieval | `seconds`      | `time.seconds [datetime]`              | Get the number of seconds since midnight |
| Other Functions        | `sleep`          | `time.sleep duration`                   | Pause execution for a specified duration |
| Other Functions        | `is_leap_year`   | `time.is_leap_year [year]`             | Check if a year is a leap year        |
| Other Functions        | `from_map`      | `time.from_map [map]`                  | Create a time object from components   |

---

### **Function Details**

#### **1. Time Retrieval Functions**

##### **`time.now` - Get Current Time**

**Parameter Description**:

| Parameter Mode | Parameter Type | Description                          |
|----------------|----------------|-------------------------------------|
| No Parameters   | -              | Returns the current time as a `DateTime` object |
| Single Parameter | `String`      | Returns a formatted string using the specified format |

**Return Value**: `DateTime` object or `String`  
**Example**:

```bash
time.now()           # => DateTime(2024-06-18T15:30:45)
time.now "%Y年%m月%d日"  # => "2024年06月18日"
```

---

##### **`time.display` - Get Pre-Formatted Time Information**

**Parameter Description**: No parameters  
**Return Value**: A mapping containing various time formats (`Map` type), keys include:

- `time`: 24-hour time (HH:MM:SS)
- `timepm`: 12-hour time (h:mm AM/PM)
- `date`: Date (YYYY-MM-DD)
- `datetime`: Date and time (YYYY-MM-DD HH:MM:SS)
- `rfc3339`: RFC3339 format
- `rfc2822`: RFC2822 format
- `week`: Week number of the year
- `ordinal`: Day of the year
- `datetime_obj`: Original DateTime object

**Example**:

```bash
time.display()

# Example Output:
+------------------------------------------------+
| KEY           VALUE                            |
+================================================+
| datetime_obj  2025-06-01 16:38:50.279424998    |
| week          22                               |
| timepm        "4:38 PM"                        |
| rfc2822       "Sun, 1 Jun 2025 16:38:50 +0800" |
| ordinal       152                              |
| date          "2025-06-01"                     |
| rfc3339       "2025-06-01T16:38:50.279424998+08:00" |
| time          "16:38:50"                       |
| datetime      "2025-06-01 16:38:50"            |
+------------------------------------------------+
```

---

##### **`time.stamp` - Get Unix Timestamp (Seconds)**

**Parameter Description**:

| Parameter Mode | Parameter Type         | Description                          |
|----------------|------------------------|-------------------------------------|
| No Parameters   | -                      | Returns the current timestamp       |
| Single Parameter | `DateTime` or `String` | Returns the timestamp for the specified time |

**Return Value**: `Integer` (timestamp in seconds)  
**Example**:

```bash
time.stamp()             # => 1718703045
time.stamp "2024-06-18 00:00:00"  # => 1718640000
```

---

##### **`time.stamp_ms` - Get Unix Timestamp (Milliseconds)**

**Parameter Description**: Same as `time.stamp`  
**Return Value**: `Integer` (timestamp in milliseconds)  
**Example**:

```bash
time.stamp_ms()             # => 1718703045123
```

---

#### **2. Time Parsing and Formatting Functions**

##### **`time.parse` - Parse Time String**

**Parameter Description**:

| Parameter | Type   | Description                     |
|-----------|--------|---------------------------------|
| 1 (optional) | `String` | Time format (defaults to common formats like "%Y-%m-%d %H:%M") |
| 2         | `String` | Time string to parse            |

**Supported Format Examples**:

- `%Y-%m-%d %H:%M` → "2024-06-18 15:30"
- `%Y-%m-%d %H:%M:%S`
- `%d/%m/%Y %H:%M:%S` → "18/06/2024 15:30:45"
- `%m/%d/%Y %I:%M %p` → "06/18/2024 03:30 PM"

**Return Value**: `DateTime` object  
**Example**:

```bash
time.parse "2024-06-18 15:30"
time.parse "%d/%m/%Y %-I:%M %p" "18/06/2024 3:30 PM"

# Returns
>> [DateTime] <<
2024-06-18 15:30:00
```

---

##### **`time.fmt` - Format Time**

**Parameter Description**:

| Parameter | Type   | Description                     |
|-----------|--------|---------------------------------|
| 1         | `String` | Format string                   |
| 2 (optional) | `DateTime` or `String` | Time to format (defaults to current time) |

**Format Symbols Reference**:

- `%Y`: Four-digit year (2024)
- `%m`: Month (01-12)
- `%d`: Day (01-31)
- `%H`: Hour in 24-hour format (00-23)
- `%M`: Minute (00-59)
- `%S`: Second (00-59)

**Return Value**: `String`  
**Example**:

```bash
time.fmt "%Y-%m-%d"                      # => "2024-06-18"
time.fmt "%H:%M:%S" (time.parse "2024-06-18 15:30:45")
# => "15:30:45"
```

---

##### **`time.to_string` - Convert to String**

**Parameter Description**:

| Parameter | Type   | Description                     |
|-----------|--------|---------------------------------|
| 1         | `DateTime` | Time object                     |
| 2 (optional) | `String` | Format string (defaults to RFC3339) |

**Return Value**: `String`  
**Example**:

```bash
time.to_string(time.now())                 # => "2024-06-18T15:30:45+08:00"
time.to_string time.now() "%Y%m%d"        # => "20240618"
```

---

#### **3. Time Arithmetic Functions**

##### **`time.add` - Add Time Interval**

**Parameter Description**:

| Parameter | Type   | Description                     |
|-----------|--------|---------------------------------|
| 1         | `String` or `Integer`... | Duration (string like "1h30m" or multiple numeric parameters) |
| 2 (optional) | `DateTime` | Base time (defaults to current time) |

**Duration Format**:

- `1d2h30m` = 1 day, 2 hours, 30 minutes
- `3600s` = 3600 seconds (1 hour)
- Multi-parameter mode: `seconds minutes hours days` (e.g., `0 30 0` = 30 minutes)

**Return Value**: `DateTime` object  
**Example**:

```bash
time.add "1h30m"                  # => Current time plus 1 hour and 30 minutes
time.add 3600 time.now()          # => Add 3600 seconds (1 hour)
time.add 0 30 0                   # => Add 30 minutes (old-style multi-parameter)
```

---

##### **`time.diff` - Calculate Time Difference**

**Parameter Description**:

| Parameter | Type   | Description                     |
|-----------|--------|---------------------------------|
| 1         | `String` | Unit ("s" for seconds, "m" for minutes, "h" for hours, "d" for days) |
| 2         | `DateTime` | Time1                          |
| 3         | `DateTime` | Time2                          |

**Return Value**: `Integer` (time difference in the specified unit)  
**Example**:

```bash
let t1 = (time.parse "2024-06-18 12:00")
let t2 = (time.parse "2024-06-18 13:30")
time.diff "m" t1 t2       # => 90 (minutes)
```

---

##### **`time.timezone` - Convert Timezone**

**Parameter Description**:

| Parameter | Type   | Description                     |
|-----------|--------|---------------------------------|
| 1         | `Integer` | Timezone offset (in hours, e.g., +8, -5) |
| 2         | `DateTime` | Time object                     |

**Return Value**: `DateTime` object (time adjusted for the timezone)  
**Example**:

```bash
let utc_time = (time.parse "2024-06-18 08:00")
time.timezone 8 utc_time      # => DateTime(2024-06-18T16:00:00+08:00)
```

---

#### **4. Time Component Retrieval Functions**

**General Description**:

- Functions include: `year`, `month`, `weekday`, `day`, `hour`, `minute`, `second`, `seconds`
- Parameter Modes:
  - No parameters: Get the corresponding component of the current time
  - Single parameter (`DateTime` or `String`): Get the corresponding component of the specified time

**Return Value**: `Integer`  

**Example**:

```bash
time.month                     # => 6 (current month)
time.weekday "2024-06-18"      # => 2 (Tuesday)
time.seconds                   # => 55845 (seconds since midnight)
```

---

#### **5. Other Utility Functions**

##### **`time.sleep` - Pause Execution**

**Parameter Description**:

| Parameter | Type   | Description                     |
|-----------|--------|---------------------------------|
| 1         | `Integer` or `String` | Sleep time (in milliseconds or duration string like "1s") |

**Duration Units**:

- `s`: Seconds (e.g., "30s")
- `m`: Minutes (e.g., "10m")
- `h`: Hours (e.g., "2h")
- `d`: Days (e.g., "1d")

**Return Value**: `none`  
**Example**:

```bash
time.sleep 1000      # Sleep for 1 second
time.sleep "2m30s"   # Sleep for 2 minutes and 30 seconds
```

---

##### **`time.is_leap_year` - Check for Leap Year**

**Parameter Description**:

| Parameter Mode | Parameter Type | Description                          |
|----------------|----------------|-------------------------------------|
| No Parameters   | -              | Check if the current year is a leap year |
| Single Parameter | `Integer`     | Check if the specified year is a leap year |

**Return Value**: `Boolean`  
**Example**:

```bash
time.is_leap_year()     # => false (2025 is not a leap year)
time.is_leap_year(2024) # => true (2024 is a leap year)
```

---

##### **`time.from_map` - Create Time from Components**

**Parameter Description**:

| Parameter | Type   | Description                     |
|-----------|--------|---------------------------------|
| 1         | `Map`  | Structure containing year, month, day, hour, minute, second |

**Return Value**: `DateTime` object  
**Example**:

```bash
let m = {
  year: 2025,
  month: 3,
  day: 28,
  hour: 12,
  minute: 5,
  second: 38
}
time.from_map m
# => DateTime(2025-03-28T12:05:38)
```

---

### **Comprehensive Usage Example**

**Calculate Countdown to Workdays**:

```bash
# Define target date
let target = (time.parse "2024-12-31 18:00")

# Calculate remaining workdays (assuming weekends are off)
fn workdays_remaining(target) {
  now = (time.now())
  days = (time.diff "d" now target)   # Total day difference
  weeks = (Math.floor(days / 7))
  let extra_days = (days % 7)
  let weekend_days = ((weeks * 2) + (Math.min extra_days 2))
  let workdays = (days - weekend_days)
  return workdays
}

print (workdays_remaining(target))
```

---

## Notes

In practical use, chained calls are supported, such as:
```bash
let a = t'2025-7-13 19:45'
a.diff('d', t'2024-1-1')
```
In the examples, type names are used for clarity.
