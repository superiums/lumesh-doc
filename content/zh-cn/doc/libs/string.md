---
title: Lumesh String 模块
date: 2025-07-13 19:16:45
highlight: true
tags:
 - libs
 - string
categories:
 - wiki
 - libs
---

String 模块提供了丰富的字符串操作函数，涵盖验证检查、子串操作、分割操作、修改操作、格式化、样式和高级操作等功能。所有函数都支持管道操作，并提供统一的错误处理机制。

## 功能概览

| 功能类别 | 主要函数 | 用途 |
|---------|---------|------|
| **类型转换** | `to_int`, `to_float`, `to_filesize`, `to_time`, `to_table` | 字符串转换为其他数据类型 |
| **基础验证** | `is_empty`, `is_whitespace`, `is_alpha`, `is_numeric`, `is_lower`, `is_upper` | 检查字符串属性 |
| **子串检查** | `starts_with`, `ends_with`, `contains` | 检查字符串包含关系 |
| **分割操作** | `split`, `split_at`, `chars`, `words`, `lines`, `paragraphs`, `concat` | 字符串分割和连接 |
| **修改操作** | `repeat`, `replace`, `substring`, `remove_prefix`, `remove_suffix`, `trim` | 字符串内容修改 |
| **大小写转换** | `to_lower`, `to_upper`, `to_title` | 大小写格式转换 |
| **格式化** | `pad_start`, `pad_end`, `center`, `wrap`, `format` | 字符串格式化和布局 |
| **样式** | `bold`, `color`,`color_bg`,`green`, `underline`, `strip` | 终端样式和颜色 |
| **高级操作** | `caesar`, `get_width`, `grep` | 加密、测量和搜索 |

## 类型转换函数

这些函数将字符串转换为其他数据类型：

**`to_int <value>`** - 将字符串转换为整数
- **参数**：`value` (必需): `String` - 要转换的字符串
- **返回**：`Integer` - 转换后的整数
- **示例**：`String.to_int "123"` 返回 `123`

**`to_float <value>`** - 将字符串转换为浮点数
- **参数**：`value` (必需): `String` - 要转换的字符串
- **返回**：`Float` - 转换后的浮点数

**`to_filesize <size_str>`** - 解析文件大小字符串为字节数
- **参数**：`size_str` (必需): `String` - 文件大小字符串（如 "1KB", "2MB"）
- **返回**：`Integer` - 字节数

**`to_time <datetime_str> [datetime_template]`** - 将字符串转换为日期时间
- **参数**：
  - `datetime_str` (必需): `String` - 日期时间字符串
  - `datetime_template` (可选): `String` - 日期时间模板
- **返回**：`DateTime` - 日期时间对象

**`to_table <command_output>`** - 将第三方命令输出转换为表格
- **参数**：`command_output` (必需): `String` - 命令输出字符串
- **返回**：`List[Map]` - 表格数据

## 字符串验证函数

### 基础类型检查

**`is_empty <string>`** - 检查字符串是否为空
- **参数**：`string` (必需): `String` - 要检查的字符串
- **返回**：`Boolean` - 字符串为空时返回 true
- **示例**：`String.is_empty ""` 返回 `true`

**`is_whitespace <string>`** - 检查字符串是否全为空白字符
- **参数**：`string` (必需): `String` - 要检查的字符串
- **返回**：`Boolean` - 所有字符都是空白字符时返回 true
- **示例**：`String.is_whitespace "   \t\n"` 返回 `true`

**`is_alpha <string>`** - 检查字符串是否全为字母
- **参数**：`string` (必需): `String` - 要检查的字符串
- **返回**：`Boolean` - 所有字符都是字母时返回 true
- **示例**：`String.is_alpha "Hello"` 返回 `true`

**`is_alphanumeric <string>`** - 检查字符串是否全为字母数字
- **参数**：`string` (必需): `String` - 要检查的字符串
- **返回**：`Boolean` - 所有字符都是字母或数字时返回 true
- **示例**：`String.is_alphanumeric "Hello123"` 返回 `true`

**`is_numeric <string>`** - 检查字符串是否全为数字
- **参数**：`string` (必需): `String` - 要检查的字符串
- **返回**：`Boolean` - 所有字符都是数字时返回 true
- **示例**：`String.is_numeric "12345"` 返回 `true`

### 大小写检查

**`is_lower <string>`** - 检查字符串是否全为小写
**`is_upper <string>`** - 检查字符串是否全为大写
**`is_title <string>`** - 检查字符串是否为标题格式
- **参数**：`string` (必需): `String` - 要检查的字符串
- **返回**：`Boolean` - 符合相应格式时返回 true

## 子串检查函数

**`starts_with <substring> <string>`** - 检查字符串是否以指定子串开头
- **参数**：
  - `substring` (必需): `String` - 要检查的前缀
  - `string` (必需): `String` - 目标字符串
- **返回**：`Boolean` - 以指定子串开头时返回 true
- **示例**：`String.starts_with "Hello" "Hello World"` 返回 `true`

**`ends_with <substring> <string>`** - 检查字符串是否以指定子串结尾
- **参数**：
  - `substring` (必需): `String` - 要检查的后缀
  - `string` (必需): `String` - 目标字符串
- **返回**：`Boolean` - 以指定子串结尾时返回 true

**`contains <substring> <string>`** - 检查字符串是否包含指定子串
- **参数**：
  - `substring` (必需): `String` - 要查找的子串
  - `string` (必需): `String` - 目标字符串
- **返回**：`Boolean` - 包含指定子串时返回 true

## 字符串分割函数

### 基础分割操作

**`split [delimiter] <string>`** - 按分隔符分割字符串
- **参数**：
  - `delimiter` (可选): `String` - 分隔符，默认使用环境变量 IFS 或空格
  - `string` (必需): `String` - 要分割的字符串
- **返回**：`List[String]` - 分割后的字符串列表
- **示例**：
  - `String.split "," "a,b,c"` 返回 `["a", "b", "c"]`
  - `String.split "a b c"` 返回 `["a", "b", "c"]`（使用默认分隔符）

**`split_at <index> <string>`** - 在指定位置分割字符串
- **参数**：
  - `index` (必需): `Integer` - 分割位置的索引
  - `string` (必需): `String` - 要分割的字符串
- **返回**：`List[String]` - 包含两部分的列表

### 特殊分割操作

**`chars <string>`** - 将字符串分割为字符列表
- **参数**：`string` (必需): `String` - 要分割的字符串
- **返回**：`List[String]` - 每个字符作为单独字符串的列表
- **示例**：`String.chars "Hello"` 返回 `["H", "e", "l", "l", "o"]`

**`words <string>`** - 按空白字符分割为单词列表
- **参数**：`string` (必需): `String` - 要分割的字符串
- **返回**：`List[String]` - 单词列表

**`lines <string>`** - 按行分割字符串
- **参数**：`string` (必需): `String` - 要分割的字符串
- **返回**：`List[String]` - 行列表
- **示例**：`String.lines "Line1\nLine2\nLine3"` 返回 `["Line1", "Line2", "Line3"]`

**`paragraphs <string>`** - 按段落分割字符串（双换行符分割）
- **参数**：`string` (必需): `String` - 要分割的字符串
- **返回**：`List[String]` - 段落列表

**`concat <string>...`** - 连接多个字符串
- **参数**：`string` (可变): `String...` - 要连接的字符串列表
- **返回**：`String` - 连接后的字符串

## 字符串修改函数

### 大小写转换

**`to_lower <string>`** - 转换为小写
**`to_upper <string>`** - 转换为大写
**`to_title <string>`** - 转换为标题格式（每个单词首字母大写）
- **参数**：`string` (必需): `String` - 要转换的字符串
- **返回**：`String` - 转换后的字符串

### 空白字符处理

**`trim <string>`** - 去除首尾空白字符
**`trim_start <string>`** - 去除开头空白字符
**`trim_end <string>`** - 去除结尾空白字符
- **参数**：`string` (必需): `String` - 要处理的字符串
- **返回**：`String` - 处理后的字符串

### 内容替换和提取

**`replace <old> <new> <string>`** - 替换所有匹配的子串
- **参数**：
  - `old` (必需): `String` - 要替换的子串
  - `new` (必需): `String` - 替换后的内容
  - `string` (必需): `String` - 目标字符串
- **返回**：`String` - 替换后的字符串

**`substring <start> <end> <string>`** - 提取子串
- **参数**：
  - `start` (必需): `Integer` - 起始位置（支持负数索引）
  - `end` (可选): `Integer` - 结束位置（支持负数索引）
  - `string` (必需): `String` - 目标字符串
- **返回**：`String` - 提取的子串

**`remove_prefix <prefix> <string>`** - 移除前缀（如果存在）
**`remove_suffix <suffix> <string>`** - 移除后缀（如果存在）
- **参数**：
  - `prefix/suffix` (必需): `String` - 要移除的前缀/后缀
  - `string` (必需): `String` - 目标字符串
- **返回**：`String` - 移除前缀/后缀后的字符串

**`repeat <count> <string>`** - 重复字符串指定次数
- **参数**：
  - `count` (必需): `Integer` - 重复次数（负数视为0）
  - `string` (必需): `String` - 要重复的字符串
- **返回**：`String` - 重复后的字符串
- **示例**：`String.repeat 3 "Hi"` 返回 `"HiHiHi"`

## 格式化函数

**`pad_start <length> [pad_char] <string>`** - 在字符串开头填充到指定长度
- **参数**：
  - `length` (必需): `Integer` - 目标长度
  - `pad_char` (可选): `String` - 填充字符，默认为空格
  - `string` (必需): `String` - 要填充的字符串
- **返回**：`String` - 填充后的字符串

**`pad_end <length> [pad_char] <string>`** - 在字符串末尾填充到指定长度
- **参数**：
  - `length` (必需): `Integer` - 目标长度
  - `pad_char` (可选): `String` - 填充字符，默认为空格
  - `string` (必需): `String` - 要填充的字符串
- **返回**：`String` - 填充后的字符串

**`center <length> [pad_char] <string>`** - 居中对齐字符串
- **参数**：
  - `length` (必需): `Integer` - 目标长度
  - `pad_char` (可选): `String` - 填充字符，默认为空格
  - `string` (必需): `String` - 要居中的字符串
- **返回**：`String` - 居中对齐后的字符串

**`wrap <width> <string>`** - 按指定宽度换行文本
- **参数**：
  - `width` (必需): `Integer` - 每行最大字符数
  - `string` (必需): `String` - 要换行的文本
- **返回**：`String` - 换行后的文本

**`format <format_string> <args>...`** - 使用 {} 占位符格式化字符串
- **参数**：
  - `format_string` (必需): `String` - 包含占位符的格式字符串
  - `args` (可变): `Any...` - 要插入的参数
- **返回**：`String` - 格式化后的字符串

## 样式函数

### 文本样式

**`bold <string>`** - 应用粗体样式
**`faint <string>`** - 应用暗淡样式
**`italics <string>`** - 应用斜体样式
**`underline <string>`** - 应用下划线样式
**`blink <string>`** - 应用闪烁效果
**`invert <string>`** - 反转前景/背景色
**`strike <string>`** - 应用删除线样式
- **参数**：`string` (必需): `String` - 要应用样式的字符串
- **返回**：`String` - 应用样式后的字符串

### 颜色函数

#### 8位色
**标准颜色**：`black`, `red`, `green`, `yellow`, `blue`, `magenta`, `cyan`, `white`
**暗色版本**：`dark_black`, `dark_red`, `dark_green`, `dark_yellow`, `dark_blue`, `dark_magenta`, `dark_cyan`, `dark_white`

- **参数**：`string` (必需): `String` - 要着色的字符串
- **返回**：`String` - 应用颜色后的字符串

#### 256色
`color256 <color_spec> <text>` - 前景色
- **参数**：`color_spec` (必需): `Integer(0-255)` - 要着色的字符串
- **参数**：`text` (必需): `String` - 要着色的字符串
- **返回**：`String` - 应用颜色后的字符串


`color256_bg <color_spec> <text>` - 背景色
- _同上_


#### 真彩色
`color <color_name|hex_color|r,g,b> <text>` - 前景色
- **参数**：
`color_name`: `String` - 颜色名字，如 'olive'
`hex_color`: `String` - 十六进制颜色代码, 如 '#ff0000'
`r,g,b`: `Integer,Integer,Integer` - rgb颜色，如 255,0,0
`color_name`: `String` - 颜色名字

- **参数**：`text` (必需): `String` - 要着色的字符串
- **返回**：`String` - 应用颜色后的字符串


`color_bg <color_name|hex_color|r,g,b> <text>` - 背景色
- _同上_


`colors [skip_colorize?]` - 列出颜色列表
- **参数**：`skip_colorize`： `boolean` - 是否跳过着色的颜色卡片
- **返回**：`Map` - 可用的颜色列表

#### 其他
**`href <url> <text>`** - 创建终端超链接
- **参数**：
  - `url` (必需): `String` - 链接地址
  - `text` (必需): `String` - 显示文本
- **返回**：`String` - 超链接格式的字符串

**`blink <text>`** - 闪烁

## 高级操作函数

**`caesar <shift> <string>`** - 凯撒密码加密
- **参数**：
  - `shift` (必需): `Integer` - 位移量，默认为13（ROT13）
  - `string` (必需): `String` - 要加密的字符串
- **返回**：`String` - 加密后的字符串
- **说明**：只对ASCII字母进行加密，其他字符保持不变

**`get_width <string>`** - 获取字符串的显示宽度
- **参数**：`string` (必需): `String` - 要测量的字符串
- **返回**：`Integer` - 字符串的最大行宽度
- **说明**：对于多行字符串，返回最长行的宽度

**`grep <substring> <string>`** - 查找包含子串的行
- **参数**：
  - `substring` (必需): `String` - 要搜索的子串
  - `string` (必需): `String` - 要搜索的文本
- **返回**：`List[String]` - 包含子串的行列表

**`pprint [headers] <string>`** - 自动转为table并美化打印

## 使用示例

### 管道操作示例
```bash
# 处理文本数据的管道操作
"Hello World" | String.to_upper() | String.replace "WORLD" "UNIVERSE"
# 结果: "HELLO UNIVERSE"

# 分析文本内容
"The quick brown fox" | String.words() | List.map(String.to_upper)
# 结果: ["THE", "QUICK", "BROWN", "FOX"]

# 多步骤文本处理
"  Hello, World!  " | String.trim() | String.split "," | List.map(String.trim)
# 结果: ["Hello", "World!"]
```

### 样式和格式化示例
```bash
# 应用样式
"Important" | String.bold() | String.red()
# 结果: 粗体红色文本

# 格式化文本
String.center 20 "*" "Title"
# 结果: "*******Title********"

```

## Notes

String 模块提供了全面的字符串处理能力，从基础的验证和操作到高级的格式化和样式功能。所有函数都经过优化，支持 Unicode 字符，并提供一致的错误处理。参数类型说明中，`<>` 表示必需参数，`[]` 表示可选参数，`...` 表示可变参数。

实际使用时，支持链式调用，如：
```bash
"Important".bold().red()
```
在示例中，为方便理解，仅使用了类型名称调用。
