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
| Boolean Generation | `ratio`       | `rand.ratio [numerator] [denominator]` | Generate a boolean value based on probability |
| String Generation   | `alpha`       | `rand.alpha [length]`                  | Generate a random alphabetic string   |
| String Generation   | `alphanum`    | `rand.alphanum [length]`               | Generate a random alphanumeric string  |
| Numeric Generation   | `int`         | `rand.int [min] [max]`                 | Generate a random integer              |
| Sampling Operation   | `choose`      | `rand.choose [list]`                   | Randomly select an element from a list |
| List Operation       | `shuffle`     | `rand.shuffle [list]`                  | Randomly shuffle the order of a list   |

---

### **Function Details**

#### **1. `rand.ratio` - Probability Boolean Generation**

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
rand.ratio          # => true (50% probability)
rand.ratio 0.3      # => false (70% probability)

# Fraction form
rand.ratio 1 4      # => true (25% probability)
```

---

#### **2. `rand.alpha` - Random Alphabetic String**

**Parameter Description**:

| Parameter Mode | Parameter Type   | Description                     |
|----------------|------------------|---------------------------------|
| No Parameters   | -                | Returns a single random letter  |
| Single Parameter | `Integer`      | Specifies the length of the generated string |

**Return Value**: `String`  
**Example**:

```bash
rand.alpha       # => "k"
rand.alpha 5     # => "qjZRy"
```

---

#### **3. `rand.alphanum` - Random Alphanumeric String**

**Parameter Description**:

| Parameter Mode | Parameter Type   | Description                     |
|----------------|------------------|---------------------------------|
| No Parameters   | -                | Returns a single random letter or digit |
| Single Parameter | `Integer`      | Specifies the length of the generated string |

**Return Value**: `String`  
**Example**:

```bash
rand.alphanum        # => "7"
rand.alphanum 8      # => "F3g9K2wQ"
```

---

#### **4. `rand.int` - Random Integer Generation**

**Parameter Description**:

| Parameter Mode | Parameter Type   | Description                     |
|----------------|------------------|---------------------------------|
| No Parameters   | -                | Returns a random integer in the entire `i64` range |
| Single Parameter | `Integer`      | Returns an integer in the range [0, max] (supports negative numbers) |
| Two Parameters   | `Integer, Integer` | Returns an integer in the range [min, max] |

**Return Value**: `Integer`  
**Example**:

```bash
rand.int             # => -327683491 (example)
rand.int 100         # => 57
rand.int -50 50      # => -12
```

---

#### **5. `rand.choose` - List Random Sampling**

**Parameter Description**:

| Parameter | Type  | Description                     |
|-----------|-------|---------------------------------|
| 1         | `List`| The data list to sample from    |

**Return Value**: Any element from the list (returns `None` for an empty list)  
**Example**:

```bash
rand.choose [1 2 3]          # => 2
rand.choose ["A" "B" "C"]    # => "B"
```

---

#### **6. `rand.shuffle` - List Randomization**

**Parameter Description**:

| Parameter | Type  | Description                     |
|-----------|-------|---------------------------------|
| 1         | `List`| The data list to randomize      |

**Return Value**: A new list with elements in random order  
**Example**:

```bash
ori = list.from(0..12)
rand.shuffle ori    # => [3, 8, 7, 11 ...]
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
  name = (list.join "-" [rand.alpha(3), rand.int(1000, 9999)])
  age = (rand.int 18 60)
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
