---
title: Lumesh console 库
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

*   函数式调用：`console.func(arg0, arg1...)`
*   命令式调用：`console.func arg0 arg1...`

---

#### 函数分类及详细说明

##### 一、终端基本信息

1.  **`console.width`**
    **功能**：获取终端宽度（列数）
    **参数**：无
    **返回值**：`integer`（失败返回`none`）
    **示例**：

    ```bash
        console.width()  # => 120
    ```


2.  **`console.height`**
    **功能**：获取终端高度（行数）
    **参数**：无
    **返回值**：`integer`（失败返回`none`）
    **示例**：

    ```bash
        console.height()  # => 40
    ```


---

##### 二、终端控制

1.  **`console.write`**
    **功能**：在指定位置输出文本
    **参数**：

    *   `x: integer`（列位置）
    *   `y: integer`（行位置）
    *   `text: string`（要输出的内容）
    **返回值**：`none`
    **示例**：

    ```bash
        console.write 10 5 "Hello"  # 在第5行第10列输出"Hello"
    ```


2.  **`console.title`**
    **功能**：设置终端窗口标题
    **参数**：`title: string`
    **返回值**：`none`
    **示例**：

    ```bash
        console.title "My App"
    ```


3.  **`console.clear`**
    **功能**：清空终端屏幕
    **参数**：无
    **返回值**：`none`
    **示例**：

    ```bash
        console.clear()
    ```


4.  **`console.flush`**
    **功能**：强制刷新输出缓冲区
    **参数**：无
    **返回值**：`none`
    **示例**：

    ```bash
        console.flush()
    ```


---

##### 三、终端模式控制

1.  **`console.mode_raw`**
    **功能**：启用原始输入模式（禁用行缓冲）
    **参数**：无
    **返回值**：`none`
    **示例**：

    ```bash
        console.mode_raw()
    ```


2.  **`console.mode_normal`**
    **功能**：禁用原始输入模式（启用行缓冲）
    **参数**：无
    **返回值**：`none`
    **示例**：

    ```bash
        console.mode_normal()
    ```


3.  **`console.screen_alternate`**
    **功能**：启用备用屏幕（保留主屏幕内容）
    **参数**：无
    **返回值**：`none`
    **示例**：

    ```bash
        console.screen_alternate()
    ```


4.  **`console.screen_normal`**
    **功能**：禁用备用屏幕（返回主屏幕）
    **参数**：无
    **返回值**：`none`
    **示例**：

    ```bash
        console.screen_normal()
    ```


---

##### 四、光标控制

1.  **`console.cursor_to`**
    **功能**：移动光标到指定位置
    **参数**：

    *   `x: integer`（列位置）
    *   `y: integer`（行位置）
    **返回值**：`none`
    **示例**：

    ```bash
        console.cursor_to 10 5
    ```


2.  **方向移动**：

    | 函数                          | 参数               | 功能         |
    |-------------------------------|--------------------|--------------|
    | `console.cursor_up()`         | `lines: integer`    | 上移N行     |
    | `console.cursor_down()`       | `lines: integer`    | 下移N行     |
    | `console.cursor_left()`       | `cols: integer`     | 左移N列     |
    | `console.cursor_right()`      | `cols: integer`     | 右移N列     |

    **示例**：

    ```bash
        console.cursor_down 3  # 下移3行
    ```


3.  **`console.cursor_save`**
    **功能**：保存当前光标位置
    **参数**：无
    **返回值**：`none`
    **示例**：

    ```bash
        console.cursor_save()
    ```


4.  **`console.cursor_restore`**
    **功能**：恢复保存的光标位置
    **参数**：无
    **返回值**：`none`
    **示例**：

    ```bash
        console.cursor_restore()
    ```


5.  **`console.cursor_hide`**
    **功能**：隐藏光标
    **参数**：无
    **返回值**：`none`
    **示例**：

    ```bash
        console.cursor_hide()
    ```


6.  **`console.cursor_show`**
    **功能**：显示光标
    **参数**：无
    **返回值**：`none`
    **示例**：

    ```bash
        console.cursor_show()
    ```


---

##### 五、键盘输入

1.  **`console.read_line`**
    **功能**：读取一行输入（显示输入内容）
    **参数**：无
    **返回值**：`string`
    **示例**：

    ```bash
        let input = console.read_line()
    ```


2.  **`console.read_password`**
    **功能**：读取密码（隐藏输入内容）
    **参数**：无
    **返回值**：`string`
    **示例**：

    ```bash
        let pwd = console.read_password()
    ```


3.  **`console.read_key`**
    **功能**：读取单个按键
    **参数**：无
    **返回值**：`string`（特殊按键的转义序列）
    **示例**：

    ```bash
        let key = console.read_key()
    ```


4.  **特殊按键常量**：

    ```bash
        console.keys.enter      # 回车键
        console.keys.backspace  # 退格键
        console.keys.tab        # Tab键
        console.keys.esc        # ESC键
        console.keys.up         # 上箭头
        console.keys.down       # 下箭头
        console.keys.left       # 左箭头
        console.keys.right      # 右箭头
        console.keys.f1         # F1功能键
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
