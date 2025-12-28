---
title: Lumesh Math 模块
date: 2025-07-13 19:16:45
highlight: true
tags:
 - libs
 - math
 - mathematics
categories:
 - wiki
 - libs
---

Math 模块提供了全面的数学计算功能，包括数学常量、基础运算、三角函数、指数对数函数、位运算和逻辑比较等。所有函数都经过优化，支持整数和浮点数的混合运算，提供精确的数学计算能力。

## 功能概览

| 功能类别 | 主要函数 | 用途 |
|---------|---------|------|
| **数学常量** | `E`, `PI`, `TAU`, `PHI` | 重要数学常量 |
| **基础数学** | `max`, `min`, `sum`, `average`, `abs`, `clamp` | 基本数学运算 |
| **位运算** | `bit_and`, `bit_or`, `bit_xor`, `bit_not`, `bit_shl`, `bit_shr` | 二进制位操作 |
| **逻辑比较** | `gt`, `ge`, `lt`, `le`, `eq`, `ne` | 数值比较运算 |
| **三角函数** | `sin`, `cos`, `tan`, `asin`, `acos`, `atan` | 三角运算 |
| **双曲函数** | `sinh`, `cosh`, `tanh`, `asinh`, `acosh`, `atanh` | 双曲运算 |
| **指数对数** | `pow`, `exp`, `exp2`, `sqrt`, `cbrt`, `log`, `ln` | 指数和对数运算 |
| **舍入函数** | `floor`, `ceil`, `round`, `trunc` | 数值舍入 |
| **其他函数** | `isodd`, `to_str` | 辅助函数 |

## 数学常量

Math 模块提供了重要的数学常量，可以直接使用：

- **`E`** - 自然对数的底数 (≈ 2.718281828)
- **`PI`** - 圆周率 (≈ 3.141592654)
- **`TAU`** - 2π (≈ 6.283185307)
- **`PHI`** - 黄金比例 (≈ 1.618033989)

## 基础数学函数

**`max <num1> <num2> ... | <array>`** - 获取最大值
**`min <num1> <num2> ... | <array>`** - 获取最小值
**`sum <num1> <num2> ... | <array>`** - 求和
**`average <num1> <num2> ... | <array>`** - 求平均值
- **参数**：可变数量的数字参数或数字数组
- **返回**：相应的计算结果
- **示例**：
  - `Math.max(1, 5, 3)` 返回 `5`
  - `Math.sum([1, 2, 3, 4])` 返回 `10`

**`abs <number>`** - 获取绝对值
- **参数**：`number` (必需): `Integer|Float` - 输入数值
- **返回**：`Integer|Float` - 绝对值
- **示例**：`Math.abs(-5)` 返回 `5`

**`clamp <min> <max> <value>`** - 将值限制在指定范围内
- **参数**：
  - `min` (必需): `Number` - 最小值
  - `max` (必需): `Number` - 最大值
  - `value` (必需): `Number` - 要限制的值
- **返回**：`Number` - 限制后的值

## 位运算函数

**`bit_and <int1> <int2>`** - 按位与运算
**`bit_or <int1> <int2>`** - 按位或运算
**`bit_xor <int1> <int2>`** - 按位异或运算
**`bit_not <integer>`** - 按位非运算
- **参数**：`integer` (必需): `Integer` - 整数值
- **返回**：`Integer` - 位运算结果

**`bit_shl <shift_bits> <integer>`** - 按位左移
**`bit_shr <shift_bits> <integer>`** - 按位右移
- **参数**：
  - `shift_bits` (必需): `Integer` - 移位位数
  - `integer` (必需): `Integer` - 要移位的整数
- **返回**：`Integer` - 移位后的结果

## 逻辑比较函数

**`gt <number> <number_base>`** - 大于比较
**`ge <number> <number_base>`** - 大于等于比较
**`lt <number> <number_base>`** - 小于比较
**`le <number> <number_base>`** - 小于等于比较
**`eq <number> <number_base>`** - 等于比较
**`ne <number> <number_base>`** - 不等于比较
- **参数**：
  - `number` (必需): `Number` - 比较的数值
  - `number_base` (必需): `Number` - 基准数值
- **返回**：`Boolean` - 比较结果

注意numbet_base的参数位置，放在最后，这是为了方便管道操作和链式调用。
如：
```bash
let a = 5
a.gt(2)           # True
5 | .gt(2)        # True
```

## 三角函数

**`sin <radians>`** - 正弦函数
**`cos <radians>`** - 余弦函数
**`tan <radians>`** - 正切函数
- **参数**：`radians` (必需): `Number` - 弧度值
- **返回**：`Float` - 三角函数值

**`asin <value>`** - 反正弦函数
**`acos <value>`** - 反余弦函数
**`atan <value>`** - 反正切函数
- **参数**：`value` (必需): `Number` - 输入值
- **返回**：`Float` - 反三角函数值（弧度）

## 双曲函数

**`sinh <value>`** - 双曲正弦函数
**`cosh <value>`** - 双曲余弦函数
**`tanh <value>`** - 双曲正切函数
**`asinh <value>`** - 反双曲正弦函数
**`acosh <value>`** - 反双曲余弦函数
**`atanh <value>`** - 反双曲正切函数
- **参数**：`value` (必需): `Number` - 输入值
- **返回**：`Float` - 双曲函数值

## π倍三角函数

**`sinpi <value>`** - sin(value × π)
**`cospi <value>`** - cos(value × π)
**`tanpi <value>`** - tan(value × π)
- **参数**：`value` (必需): `Number` - 输入值（将乘以π）
- **返回**：`Float` - 计算结果

## 指数与对数函数

**`pow <exponent> <base>`** - 幂运算
- **参数**：
  - `exponent` (必需): `Number` - 指数
  - `base` (必需): `Number` - 底数
- **返回**：`Float` - base^exponent

**`exp <exponent>`** - e的指数次幂
**`exp2 <exponent>`** - 2的指数次幂
- **参数**：`exponent` (必需): `Number` - 指数
- **返回**：`Float` - 指数运算结果

**`sqrt <number>`** - 平方根
**`cbrt <number>`** - 立方根
- **参数**：`number` (必需): `Number` - 输入数值
- **返回**：`Float` - 根值

**`log <number> <base>`** - 指定底数的对数
**`log2 <number>`** - 以2为底的对数
**`log10 <number>`** - 以10为底的对数
**`ln <number>`** - 自然对数
- **参数**：`number` (必需): `Number` - 输入数值
- **返回**：`Float` - 对数值

## 舍入函数

**`floor <number>`** - 向下取整
**`ceil <number>`** - 向上取整
**`round <number>`** - 四舍五入到最近整数
**`trunc <number>`** - 截断小数部分
- **参数**：`number` (必需): `Number` - 输入数值
- **返回**：`Integer|Float` - 舍入后的值

## 其他函数

**`isodd <integer>`** - 检查是否为奇数
- **参数**：`integer` (必需): `Integer` - 要检查的整数
- **返回**：`Boolean` - 是奇数时返回 true

**`to_str <number>`** - 将数字转换为字符串
- **参数**：`number` (必需): `Number` - 要转换的数值
- **返回**：`String` - 字符串表示

## 使用示例

### 基础数学运算
```bash
# 基本运算
Math.max(1, 5, 3, 2)      # 返回 5
Math.sum([1, 2, 3, 4])    # 返回 10
Math.abs(-3.14)           # 返回 3.14

# 使用数学常量
Math.PI * 2               # 等同于 Math.TAU
```

### 三角函数计算
```bash
# 计算30度的正弦值（需要转换为弧度）
Math.sin(Math.PI / 6)     # 返回 0.5

# 使用π倍函数更简洁
Math.sinpi(1/6)           # 同样返回 0.5
```

### 位运算操作
```bash
# 位运算
Math.bit_and(12, 10)      # 返回 8 (1100 & 1010 = 1000)
Math.bit_shl(2, 5)        # 返回 20 (5 << 2 = 20)
```

### 管道操作示例
```bash
# 数据处理管道
[1, -2, 3, -4] | List.map(Math.abs) | Math.sum()
# 结果: 10

# 复杂数学计算
Math.PI | Math.sin() | Math.abs() | Math.sqrt()
```

## Notes

Math 模块提供了完整的数学计算能力，所有函数都经过优化以支持高精度计算。三角函数使用弧度制，如需角度制请手动转换。位运算函数只接受整数参数，而其他数学函数支持整数和浮点数的混合运算。参数类型说明中，`<>` 表示必需参数，`[]` 表示可选参数，`...` 表示可变参数。

实际使用时，支持链式调用，如：
```bash
let a = 1
a.max(3)
```
在示例中，为方便理解，仅使用了类型名称调用。
