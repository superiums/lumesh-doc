---
title: Lumesh Time 模块
date: 2025-06-11 19:16:45
highlight: true
tags:
 - libs
 - datetime
categories:
 - wiki
 - libs
---

**模块路径**：`time`
  使用 `help time` 查看帮助

**功能描述**：提供时间获取、解析、计算和格式化功能

---

### **函数目录**

| 功能类别         | 函数名         | 调用格式                          | 核心功能                     |
|------------------|----------------|-----------------------------------|------------------------------|
| 时间获取         | `now`          | `Time.now [format]`              | 获取当前时间（对象或格式化字符串） |
| 时间获取         | `display`      | `Time.display`                    | 获取预格式化时间信息（多种格式的映射） |
| 时间获取         | `stamp`        | `Time.stamp [datetime]`          | 获取Unix时间戳（秒）         |
| 时间获取         | `stamp_ms`     | `Time.stamp_ms [datetime]`       | 获取Unix时间戳（毫秒）       |
| 时间解析与格式化 | `parse`        | `Time.parse [format] datetime_str ` | 解析时间字符串为时间对象     |
| 时间解析与格式化 | `fmt`          | `Time.fmt format_str [datetime]` | 格式化时间对象为字符串       |
| 时间解析与格式化 | `to_string`    | `Time.to_string datetime [format]` | 转换时间对象为字符串（默认RFC3339） |
| 时间运算         | `add`          | `Time.add duration [base_time]`  | 给时间增加持续时间           |
| 时间运算         | `diff`         | `Time.diff time1 time2 unit`     | 计算两个时间的差值（指定单位） |
| 时间运算         | `timezone`     | `Time.timezone datetime ofTimeet_hours` | 转换时区（按小时偏移）       |
| 时间组件获取     | `year`         | `Time.year [datetime]`           | 获取年份                     |
| 时间组件获取     | `month`        | `Time.month [datetime]`          | 获取月份（1-12）             |
| 时间组件获取     | `weekday`      | `Time.weekday [datetime]`        | 获取星期几（1-7，周一为1）   |
| 时间组件获取     | `day`          | `Time.day [datetime]`            | 获取日期（1-31）             |
| 时间组件获取     | `hour`         | `Time.hour [datetime]`           | 获取小时（0-23）             |
| 时间组件获取     | `minute`       | `Time.minute [datetime]`         | 获取分钟（0-59）             |
| 时间组件获取     | `second`       | `Time.second [datetime]`         | 获取秒数（0-59）             |
| 时间组件获取     | `seconds`      | `Time.seconds [datetime]`        | 获取自午夜起的秒数           |
| 其他功能         | `sleep`        | `Time.sleep duration`             | 暂停执行指定时长             |
| 其他功能         | `is_leap_year` | `Time.is_leap_year [year]`       | 判断是否为闰年               |
| 其他功能         | `from_map`   | `Time.from_map [map]` | 通过组件创建时间对象         |

---

### **函数详解**

#### **1. 时间获取函数**

##### **`Time.now` - 获取当前时间**

**参数说明**：

| 参数模式   | 参数类型 | 作用描述                          |
|------------|----------|-----------------------------------|
| 无参数     | -        | 返回当前时间的`DateTime`对象     |
| 单参数     | `String` | 使用指定格式返回格式化字符串     |

**返回值**：`DateTime`对象或`String`
**示例**：

```bash
Time.now()           # => DateTime(2024-06-18T15:30:45)
Time.now "%Y年%m月%d日"  # => "2024年06月18日"
```

---

##### **`Time.display` - 获取预格式化时间信息**

**参数说明**：无参数
**返回值**：包含多种时间格式的映射（`Map`类型），键包括：

- `time`: 24小时制时间（HH:MM:SS）
- `timepm`: 12小时制时间（h:mm AM/PM）
- `date`: 日期（YYYY-MM-DD）
- `datetime`: 日期时间（YYYY-MM-DD HH:MM:SS）
- `rfc3339`: RFC3339格式
- `rfc2822`: RFC2822格式
- `week`: 一年中的第几周
- `ordinal`: 一年中的第几天
- `datetime_obj`: 原始DateTime对象

**示例**：

```bash
Time.display()

# 输出示例：
+------------------------------------------------+
| KEY           VALUE                            |
+================================================+
| datetime_obj  2025-06-01 16:38:50.279424998    |
| week          22                               |
| timepm        "4:38 PM"                        |
| rfc2822       "Sun, 1 Jun 2025 16:38:50 +0800" |
| ordinal       152                              |
| date          "2025-06-01"                     |
| rfc3339       "2025-06-                        |
|               01T16:38:50.279424998+08:00"     |
| time          "16:38:50"                       |
| datetime      "2025-06-01 16:38:50"            |
+------------------------------------------------+
```

---

##### **`Time.stamp` - 获取Unix时间戳（秒）**

**参数说明**：

| 参数模式   | 参数类型         | 作用描述                          |
|------------|------------------|-----------------------------------|
| 无参数     | -                | 返回当前时间戳                   |
| 单参数     | `DateTime`或`String` | 返回指定时间的时间戳             |

**返回值**：`Integer`（时间戳，单位秒）
**示例**：

```bash
Time.stamp()             # => 1718703045
Time.stamp "2024-06-18 00:00:00"  # => 1718640000
```

---

##### **`Time.stamp_ms` - 获取Unix时间戳（毫秒）**

**参数说明**：同`Time.stamp`
**返回值**：`Integer`（时间戳，单位毫秒）
**示例**：

```bash
Time.stamp_ms()             # => 1718703045123
```

---

#### **2. 时间解析与格式化函数**

##### **`Time.parse` - 解析时间字符串**

**参数说明**：

| 参数 | 类型   | 作用描述                     |
|------|--------|------------------------------|
| 1（可选） | `String` | 时间格式（默认尝试常见格式，如"%Y-%m-%d %H:%M"） |
| 2    | `String` | 待解析的时间字符串           |

**支持格式示例**：

- `%Y-%m-%d %H:%M` → "2024-06-18 15:30"
- `%Y-%m-%d %H:%M:%S`
- `%d/%m/%Y %H:%M:%S` → "18/06/2024 15:30:45"
- `%m/%d/%Y %I:%M %p` → "06/18/2024 03:30 PM"

**返回值**：`DateTime`对象
**示例**：

```bash
Time.parse "2024-06-18 15:30"
Time.parse "%d/%m/%Y %-I:%M %p" "18/06/2024 3:30 PM"

# 返回
>> [DateTime] <<
2024-06-18 15:30:00
```

---

##### **`Time.fmt` - 格式化时间**

**参数说明**：

| 参数 | 类型   | 作用描述                     |
|------|--------|------------------------------|
| 1    | `String` | 格式字符串                   |
| 2（可选） | `DateTime`或`String` | 待格式化的时间（默认当前时间） |

**格式符号参考**：

- `%Y`：四位年份（2024）
- `%m`：月份（01-12）
- `%d`：日期（01-31）
- `%H`：24小时制小时（00-23）
- `%M`：分钟（00-59）
- `%S`：秒（00-59）

**返回值**：`String`
**示例**：

```bash
Time.fmt "%Y-%m-%d"                      # => "2024-06-18"
Time.fmt "%H:%M:%S" (Time.parse "2024-06-18 15:30:45")
# => "15:30:45"
```

---

##### **`Time.to_string` - 转换为字符串**

**参数说明**：

| 参数 | 类型   | 作用描述                     |
|------|--------|------------------------------|
| 1    | `DateTime` | 时间对象                     |
| 2（可选） | `String` | 格式字符串（默认RFC3339）     |

**返回值**：`String`
**示例**：

```bash
Time.to_string(Time.now())                 # => "2024-06-18T15:30:45+08:00"
Time.to_string Time.now() "%Y%m%d"        # => "20240618"
```

---

#### **3. 时间运算函数**

##### **`Time.add` - 增加时间间隔**

**参数说明**：

| 参数 | 类型   | 作用描述                     |
|------|--------|------------------------------|
| 1    | `String`或`Integer`... | 持续时间（字符串如"1h30m"或多个数字参数） |
| 2（可选） | `DateTime` | 基准时间（默认当前时间）     |

**持续时间格式**：

- `1d2h30m` = 1天2小时30分钟
- `3600s` = 3600秒（1小时）
- 多参数模式：`秒 分 时 天`（如 `0 30 0` = 30分钟）

**返回值**：`DateTime`对象
**示例**：

```bash
Time.add "1h30m"                  # => 当前时间加1小时30分钟
Time.add 3600 Time.now()          # => 加3600秒（1小时）
Time.add 0 30 0                   # => 加30分钟（旧式多参数）
```

---

##### **`Time.diff` - 计算时间差**

**参数说明**：

| 参数 | 类型   | 作用描述                     |
|------|--------|------------------------------|
| 1    | `String` | 单位（"s"秒、"m"分、"h"时、"d"天） |
| 2    | `DateTime` | 时间1                       |
| 3    | `DateTime` | 时间2                       |

**返回值**：`Integer`（时间差，按指定单位）
**示例**：

```bash
let t1 = (Time.parse "2024-06-18 12:00")
let t2 = (Time.parse "2024-06-18 13:30")
Time.diff "m" t1 t2       # => 90（分钟）
```

---

##### **`Time.timezone` - 转换时区**

**参数说明**：

| 参数 | 类型   | 作用描述                     |
|------|--------|------------------------------|
| 1    | `Integer` | 时区偏移（小时，如+8、-5）   |
| 2    | `DateTime` | 时间对象                     |

**返回值**：`DateTime`对象（调整时区后的时间）
**示例**：

```bash
let utc_time = (Time.parse "2024-06-18 08:00")
Time.timezone 8 utc_time      # => DateTime(2024-06-18T16:00:00+08:00)
```

---

#### **4. 时间组件获取函数**

**通用说明**：

- 函数包括：`year`, `month`, `weekday`, `day`, `hour`, `minute`, `second`, `seconds`
- 参数模式：
  - 无参数：获取当前时间的对应组件
  - 单参数（`DateTime`或`String`）：获取指定时间的对应组件

**返回值**：`Integer`

**示例**：

```bash
Time.month                     # => 6（当前月份）
Time.weekday "2024-06-18"      # => 2（星期二）
Time.seconds                   # => 55845（当前时间距离午夜秒数）
```

---

#### **5. 其他功能函数**

##### **`Time.sleep` - 暂停执行**

**参数说明**：

| 参数 | 类型   | 作用描述                     |
|------|--------|------------------------------|
| 1    | `Integer`或`String` | 休眠时间（毫秒数或持续时间字符串如"1s"） |

**持续时间单位**：

- `s`：秒（如"30s"）
- `m`：分钟（如"10m"）
- `h`：小时（如"2h"）
- `d`：天（如"1d"）

**返回值**：`None`
**示例**：

```bash
Time.sleep 1000      # 休眠1秒
Time.sleep "2m30s"   # 休眠2分30秒
```

---

##### **`Time.is_leap_year` - 判断闰年**

**参数说明**：

| 参数模式   | 参数类型 | 作用描述                          |
|------------|----------|-----------------------------------|
| 无参数     | -        | 判断当前年份是否为闰年            |
| 单参数     | `Integer` | 判断指定年份是否为闰年            |

**返回值**：`Boolean`
**示例**：

```bash
Time.is_leap_year()     # => false（2025年不是闰年）
Time.is_leap_year(2024)     # => true（2024年是闰年）
```

---

##### **`Time.from_map` - 通过组件创建时间**

**参数说明**：

| 参数 | 类型   | 作用描述                     |
|------|--------|------------------------------|
| 1    | `Map`  | 包含年月日，时分秒的结构     |



**返回值**：`DateTime`对象
**示例**：

```bash
let m = {
  year: 2025,
  month: 3,
  day: 28,
  hour: 12,
  minute: 5,
  second: 38
}
Time.from_map m
# => DateTime(2024-06-18T15:30:00)
```

---

### **综合使用示例**

**计算工作日倒计时**：

```bash
# 定义目标日期
let target = (Time.parse "2024-12-31 18:00")

# 计算剩余工作日（假设周六、日休息）
fn workdays_remaining(target) {
  now = (Time.now())
  days = (Time.diff "d" now target)   # 总天数差
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

实际使用时，支持链式调用，如：
```bash
let a = t'2025-7-13 19:45'
a.diff('d', t'2024-1-1')
```
在示例中，为方便理解，仅使用了类型名称调用。
