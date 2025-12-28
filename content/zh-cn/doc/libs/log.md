---
title: Lumesh Log 模块
date: 2025-07-13 19:16:45
highlight: true
tags:
 - libs
 - log
categories:
 - wiki
 - libs
---

Log 模块提供了结构化日志记录功能，支持多种日志级别、彩色输出和线程安全的级别管理。  所有函数都支持管道操作，并提供统一的错误处理机制。

## 功能概览

| 功能类别 | 主要函数 | 用途 |
|---------|---------|------|
| **日志级别管理** | `set_level`, `get_level`, `disable`, `enabled` | 控制日志输出级别 |
| **日志记录** | `info`, `warn`, `debug`, `error`, `trace` | 不同级别的日志输出 |
| **原始输出** | `echo` | 无格式化的直接输出 |
| **级别常量** | `level.none`, `level.error`, `level.warn`, `level.info`, `level.debug`, `level.trace` | 日志级别常量 |

## 日志级别常量

Log 模块提供了完整的日志级别常量：

**`level`** - 日志级别常量映射
- **包含**：`none` (0), `error` (1), `warn` (2), `info` (3), `debug` (4), `trace` (5)
- **示例**：
  ```bash
  Log.set_level Log.level.debug
  # 设置日志级别为 DEBUG

  Log.set_level 3
  # 直接使用数字设置为 INFO 级别
  ```

## 日志级别管理函数

这些函数用于控制日志输出的级别：

**`set_level <level>`** - 设置日志级别
- **参数**：`level` (必需): `Integer` - 日志级别（0-5）
- **返回**：`None`
- **示例**：
  ```bash
  Log.set_level Log.level.warn
  # 只显示 WARN 及以上级别的日志

  Log.set_level 1
  # 只显示 ERROR 级别的日志
  ```

**`get_level`** - 获取当前日志级别
- **参数**：无
- **返回**：`Integer` - 当前日志级别
- **示例**：
  ```bash
  Log.get_level
  # 返回: 3 (默认 INFO 级别)

  let current_level = Log.get_level
  ```

**`disable`** - 禁用所有日志输出
- **参数**：无
- **返回**：`None`
- **示例**：
  ```bash
  Log.disable
  # 禁用所有日志输出

  Log.info "这条消息不会显示"
  ```

**`enabled <level>`** - 检查指定级别是否启用
- **参数**：`level` (必需): `Integer` - 要检查的日志级别
- **返回**：`Boolean` - 级别是否启用
- **示例**：
  ```bash
  Log.enabled Log.level.debug
  # 返回: true/false

  if (Log.enabled 4) { Log.debug "调试信息" }
  ```

## 日志记录函数

这些函数用于输出不同级别的日志消息：

**`info <message>...`** - 记录信息级别日志
- **参数**：`message` (必需): `Any` - 要记录的消息（支持多个参数）
- **返回**：`None`
- **颜色**：绿色 `[INFO]` 前缀
- **示例**：
  ```bash
  Log.info "应用程序启动成功"
  # 输出: [INFO] 应用程序启动成功

  Log.info "用户" $username "登录成功"
  # 输出: [INFO] 用户 alice 登录成功
  ```

**`warn <message>...`** - 记录警告级别日志
- **参数**：`message` (必需): `Any` - 要记录的消息（支持多个参数）
- **返回**：`None`
- **颜色**：黄色 `[WARN]` 前缀
- **示例**：
  ```bash
  Log.warn "磁盘空间不足"
  # 输出: [WARN] 磁盘空间不足

  Log.warn "配置文件" $config_file "不存在，使用默认配置"
  ```

**`debug <message>...`** - 记录调试级别日志
- **参数**：`message` (必需): `Any` - 要记录的消息（支持多个参数）
- **返回**：`None`
- **颜色**：蓝色 `[DEBUG]` 前缀
- **示例**：
  ```bash
  Log.debug "处理请求" $request_id
  # 输出: [DEBUG] 处理请求 req-12345

  Log.debug "变量值：" $var
  ```

**`error <message>...`** - 记录错误级别日志
- **参数**：`message` (必需): `Any` - 要记录的消息（支持多个参数）
- **返回**：`None`
- **颜色**：红色 `[ERROR]` 前缀
- **示例**：
  ```bash
  Log.error "数据库连接失败"
  # 输出: [ERROR] 数据库连接失败

  Log.error "文件" $filename "读取失败：" $error_msg
  ```

**`trace <message>...`** - 记录跟踪级别日志
- **参数**：`message` (必需): `Any` - 要记录的消息（支持多个参数）
- **返回**：`None`
- **颜色**：洋红色 `[TRACE]` 前缀
- **示例**：
  ```bash
  Log.trace "进入函数 process_data"
  # 输出: [TRACE] 进入函数 process_data

  Log.trace "循环第" $i "次迭代"
  ```

## 原始输出函数

**`echo <message>...`** - 无格式化输出
- **参数**：`message` (必需): `Any` - 要输出的消息（支持多个参数）
- **返回**：`None`
- **示例**：
  ```bash
  Log.echo "纯文本输出，无级别前缀"
  # 输出: 纯文本输出，无级别前缀

  Log.echo "值1" "值2" "值3"
  # 输出: 值1 值2 值3
  ```

## 多行输出支持

Log 模块支持多行消息的格式化输出：

**示例**：
```bash
Log.info "多行消息：\n第一行\n第二行\n第三行"
# 输出:
# [INFO] 多行消息：
#        第一行
#        第二行
#        第三行
```

**规则**：

1.  首行显示彩色标签
2.  后续行缩进对齐标签长度
3.  末尾自动补全换行符


## Notes

Log 模块是 Lumesh 内置模块系统中的重要组件，在模块注册表中注册为 "Log"。  该模块提供了完整的日志记录解决方案，支持彩色输出、级别控制和多行格式化。默认日志级别为 INFO (3)，可以通过 `set_level` 函数动态调整。


---

> 注意：颜色效果需在支持ANSI的终端中查看
