---
title: Lumesh Rand Module  
date: 2025-07-13 19:16:45  
highlight: true  
tags:  
 - libs  
 - random  
categories:  
 - wiki  
 - libs  
---

**Module Name**: `rand`  
You can view help using `help rand`.

**Function Description**: Provides random number generation, random sampling, and randomization operations.

---

### **Function Directory**

| Function Category | Function Name | Calling Format                          | Core Functionality                     |
|-------------------|---------------|-----------------------------------------|----------------------------------------|
| Boolean Generation | `ratio`       | `Rand.ratio [numerator] [denominator]` | Generate a boolean value based on probability |
| String Generation   | `alpha`       | `Rand.alpha [length]`                  | Generate a random alphabetic string   |
| String Generation   | `alphanum`    | `Rand.alphanum [length]`               | Generate a random alphanumeric string  |
| Numeric Generation   | `int`         | `Rand.int [min] [max]`                 | Generate a random integer              |
| Sampling Operation   | `choose`      | `Rand.choose [list]`                   | Randomly select an element from a list |
| List Operation       | `shuffle`     | `Rand.shuffle [list]`                  | Randomly shuffle the order of a list   |

---

### **Function Details**

#### **1. `Rand.ratio` - Probability Boolean Generation**

**Parameter Description**:

| Parameter Mode | Parameter Type   | Description                          |
|----------------|------------------|-------------------------------------|
| No Parameters   | -                | Defaults to returning `true` with 50% probability |
| Single Parameter | `Float`        | Specifies the probability of `true` (0.0 to 1.0) |
| Two Parameters  | `Integer, Integer` | Specifies probability in fraction form (e.g., `3 5` means 3/5 probability) |

**Return Value**: `Boolean`  
**Example**:

```bash
# Basic call
Rand.ratio          # => true (50% probability)
Rand.ratio 0.3      # => false (70% probability)

# Fraction form
Rand.ratio 1 4      # => true (25% probability)
```

---

#### **2. `Rand.alpha` - Random Alphabetic String**

**Parameter Description**:

| Parameter Mode | Parameter Type   | Description                     |
|----------------|------------------|---------------------------------|
| No Parameters   | -                | Returns a single random letter  |
| Single Parameter | `Integer`      | Specifies the length of the generated string |

**Return Value**: `String`  
**Example**:

```bash
Rand.alpha       # => "k"
Rand.alpha 5     # => "qjZRy"
```

---

#### **3. `Rand.alphanum` - Random Alphanumeric String**

**Parameter Description**:

| Parameter Mode | Parameter Type   | Description                     |
|----------------|------------------|---------------------------------|
| No Parameters   | -                | Returns a single random letter or digit |
| Single Parameter | `Integer`      | Specifies the length of the generated string |

**Return Value**: `String`  
**Example**:

```bash
Rand.alphanum        # => "7"
Rand.alphanum 8      # => "F3g9K2wQ"
```

---

#### **4. `Rand.int` - Random Integer Generation**

**Parameter Description**:

| Parameter Mode | Parameter Type   | Description                     |
|----------------|------------------|---------------------------------|
| No Parameters   | -                | Returns a random integer in the entire `i64` range |
| Single Parameter | `Integer`      | Returns an integer in the range [0, max] (supports negative numbers) |
| Two Parameters   | `Integer, Integer` | Returns an integer in the range [min, max] |

**Return Value**: `Integer`  
**Example**:

```bash
Rand.int             # => -327683491 (example)
Rand.int 100         # => 57
Rand.int -50 50      # => -12
```

---

#### **5. `Rand.choose` - List Random Sampling**

**Parameter Description**:

| Parameter | Type  | Description                     |
|-----------|-------|---------------------------------|
| 1         | `List`| The data list to sample from    |

**Return Value**: Any element from the list (returns `None` for an empty list)  
**Example**:

```bash
Rand.choose [1 2 3]          # => 2
Rand.choose ["A" "B" "C"]    # => "B"
```

---

#### **6. `Rand.shuffle` - List Randomization**

**Parameter Description**:

| Parameter | Type  | Description                     |
|-----------|-------|---------------------------------|
| 1         | `List`| The data list to randomize      |

**Return Value**: A new list with elements in random order  
**Example**:

```bash
ori = List.from(0..12)
Rand.shuffle ori    # => [3, 8, 7, 11 ...]
```

**Output Example**:

```
┌───┬───┬───┬────┬───┬───┬───┬───┬───┬────┬───┬───┐
│ 3 │ 8 │ 7 │ 11 │ 9 │ 2 │ 5 │ 0 │ 6 │ 10 │ 4 │ 1 │
└───┴───┴───┴────┴───┴───┴───┴───┴───┴────┴───┴───┘
```

---

### **Usage Scenario Examples**

**Batch Generate Test Data**:

```bash
# Generate 10 random usernames
fn gen_user() {
  name = (List.join "-" [Rand.alpha(3), Rand.int(1000, 9999)])
  age = (Rand.int 18 60)
  return [name, age]
}

repeat 10 { gen_user | print }
```

**Output Example**:

```bash
+-------------------------+
| USERNAME      AGE       |
+=========================+
| xQz-4823      34        |
| kFt-7391      22        |
| ...           ...       |
+-------------------------+
```
