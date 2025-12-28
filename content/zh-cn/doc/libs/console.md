---
title: Lumesh Console模块
date: 2025-06-11 19:16:45
highlight: true
tags:
 - libs
 - console
categories:
 - wiki
 - libs
---

控制台操作

**模块路径**：`console`
**功能说明**：提供终端控制、光标操作、键盘输入等控制台交互功能。支持两种调用方式：

*   函数式调用：`Console.func(arg0, arg1...)`
*   命令式调用：`Console.func arg0 arg1...`

---

#### 函数分类及详细说明

##### 一、终端基本信息

1.  **`Console.width`**
    **功能**：获取终端宽度（列数）
    **参数**：无
    **返回值**：`integer`（失败返回`none`）
    **示例**：

    ```bash
        Console.width()  # => 120
    ```


2.  **`Console.height`**
    **功能**：获取终端高度（行数）
    **参数**：无
    **返回值**：`integer`（失败返回`none`）
    **示例**：

    ```bash
        Console.height()  # => 40
    ```


---

##### 二、终端控制

1.  **`Console.write`**
    **功能**：在指定位置输出文本
    **参数**：

    *   `x: integer`（列位置）
    *   `y: integer`（行位置）
    *   `text: string`（要输出的内容）
    **返回值**：`none`
    **示例**：

    ```bash
        Console.write 10 5 "Hello"  # 在第5行第10列输出"Hello"
    ```


2.  **`Console.title`**
    **功能**：设置终端窗口标题
    **参数**：`title: string`
    **返回值**：`none`
    **示例**：

    ```bash
        Console.title "My App"
    ```


3.  **`Console.clear`**
    **功能**：清空终端屏幕
    **参数**：无
    **返回值**：`none`
    **示例**：

    ```bash
        Console.clear()
    ```


4.  **`Console.flush`**
    **功能**：强制刷新输出缓冲区
    **参数**：无
    **返回值**：`none`
    **示例**：

    ```bash
        Console.flush()
    ```


---

##### 三、终端模式控制

1.  **`Console.mode_raw`**
    **功能**：启用原始输入模式（禁用行缓冲）
    **参数**：无
    **返回值**：`none`
    **示例**：

    ```bash
        Console.mode_raw()
    ```


2.  **`Console.mode_normal`**
    **功能**：禁用原始输入模式（启用行缓冲）
    **参数**：无
    **返回值**：`none`
    **示例**：

    ```bash
        Console.mode_normal()
    ```


3.  **`Console.screen_alternate`**
    **功能**：启用备用屏幕（保留主屏幕内容）
    **参数**：无
    **返回值**：`none`
    **示例**：

    ```bash
        Console.screen_alternate()
    ```


4.  **`Console.screen_normal`**
    **功能**：禁用备用屏幕（返回主屏幕）
    **参数**：无
    **返回值**：`none`
    **示例**：

    ```bash
        Console.screen_normal()
    ```


---

##### 四、光标控制

1.  **`Console.cursor_to`**
    **功能**：移动光标到指定位置
    **参数**：

    *   `x: integer`（列位置）
    *   `y: integer`（行位置）
    **返回值**：`none`
    **示例**：

    ```bash
        Console.cursor_to 10 5
    ```


2.  **方向移动**：

    | 函数                          | 参数               | 功能         |
    |-------------------------------|--------------------|--------------|
    | `Console.cursor_up()`         | `lines: integer`    | 上移N行     |
    | `Console.cursor_down()`       | `lines: integer`    | 下移N行     |
    | `Console.cursor_left()`       | `cols: integer`     | 左移N列     |
    | `Console.cursor_right()`      | `cols: integer`     | 右移N列     |

    **示例**：

    ```bash
        Console.cursor_down 3  # 下移3行
    ```


3.  **`Console.cursor_save`**
    **功能**：保存当前光标位置
    **参数**：无
    **返回值**：`none`
    **示例**：

    ```bash
        Console.cursor_save()
    ```


4.  **`Console.cursor_restore`**
    **功能**：恢复保存的光标位置
    **参数**：无
    **返回值**：`none`
    **示例**：

    ```bash
        Console.cursor_restore()
    ```


5.  **`Console.cursor_hide`**
    **功能**：隐藏光标
    **参数**：无
    **返回值**：`none`
    **示例**：

    ```bash
        Console.cursor_hide()
    ```


6.  **`Console.cursor_show`**
    **功能**：显示光标
    **参数**：无
    **返回值**：`none`
    **示例**：

    ```bash
        Console.cursor_show()
    ```


---

##### 五、键盘输入

1.  **`Console.read_line`**
    **功能**：读取一行输入（显示输入内容）
    **参数**：无
    **返回值**：`string`
    **示例**：

    ```bash
        let input = Console.read_line()
    ```


2.  **`Console.read_password`**
    **功能**：读取密码（隐藏输入内容）
    **参数**：无
    **返回值**：`string`
    **示例**：

    ```bash
        let pwd = Console.read_password()
    ```


3.  **`Console.read_key`**
    **功能**：读取单个按键
    **参数**：无
    **返回值**：`string`（特殊按键的转义序列）
    **示例**：

    ```bash
        let key = Console.read_key()
    ```


4.  **特殊按键常量**：

    ```bash
        Console.keys.enter      # 回车键
        Console.keys.backspace  # 退格键
        Console.keys.tab        # Tab键
        Console.keys.esc        # ESC键
        Console.keys.up         # 上箭头
        Console.keys.down       # 下箭头
        Console.keys.left       # 左箭头
        Console.keys.right      # 右箭头
        Console.keys.f1         # F1功能键
        ...                             # 其他功能键类似
    ```


---

#### 通用规则

1.  **坐标系统**：

    *   左上角为原点 (0, 0)
    *   X轴向右递增（列）
    *   Y轴向下递增（行）

2.  **错误处理**：

    *   参数类型错误时抛出异常
    *   终端操作失败返回`none`

3.  **ANSI转义序列**：
    所有控制功能均使用标准ANSI转义序列实现
