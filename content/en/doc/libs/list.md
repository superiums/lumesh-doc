---
title: Lumesh List Module
date: 2025-07-13 19:16:45
highlight: true
tags:
 - libs
 - list
 - array
categories:
 - wiki
 - libs
---

The List module is one of the most important data processing modules in Lumesh, providing a rich set of list operation functions that support functional programming paradigms. All functions are designed to be immutable, returning new lists without modifying the original list.

## Function Overview

| Function Category | Main Functions | Purpose |
|-------------------|----------------|--------|
| **Print Output** | `pprint` | Pretty print lists |
| **Mathematical Statistics** | `max`, `min`, `sum`, `average` | Numerical calculations and statistics |
| **Basic Operations** | `get`, `len`, `insert`, `rev`, `flatten` | Basic data operations |
| **Element Access** | `first`, `last`, `at`, `take`, `drop` | Access list elements |
| **Search Operations** | `contains`, `find`, `find_last` | Element searching and locating |
| **Modification Operations** | `append`, `prepend`, `unique`, `split_at`, `sort`, `group`, `remove_at`, `remove`, `set` | Modify list structure |
| **Creation Operations** | `concat`, `from` | List creation and combination |
| **Functional Operations** | `map`, `filter`, `filter_map`, `reduce`, `any`, `all`, `each` | Higher-order function processing |
| **Conversion Operations** | `join`, `to_map`, `items` | Data format conversion |
| **Structural Operations** | `transpose`, `chunk`, `zip`, `unzip`, `foldl`, `foldr` | Complex structure transformations |

## Print Output Functions

**`pprint <list>`** - Pretty print a list
- **Parameters**: `list` (required): `List` - The list to print
- **Purpose**: Display list data in a table format

## Mathematical Statistics Functions

**`max <num1> <num2> ... | <array>`** - Get the maximum value
**`min <num1> <num2> ... | <array>`** - Get the minimum value
**`sum <num1> <num2> ... | <array>`** - Calculate the sum
**`average <num1> <num2> ... | <array>`** - Calculate the average
- **Parameters**: Variable number of numeric parameters or numeric arrays
- **Returns**: Corresponding calculation result
- **Example**:
  - `List.max(1, 5, 3)` returns `5`
  - `List.sum([1, 2, 3, 4])` returns `10`

## Basic Operation Functions

**`get <path> <map|list|range>`** - Access values from nested structures using dot notation
- **Parameters**:
  - `path` (required): `String` - Access path, supports dot separation
  - `data` (required): `Map|List|Range` - Data structure to access
- **Returns**: `Any` - Value at the specified path
- **Example**: `List.get("0.name", [{"name": "Alice"}])` returns `"Alice"`

**`len <list>`** - Get the length of a list
- **Parameters**: `list` (required): `List` - The list to calculate length
- **Returns**: `Integer` - Length of the list

**`insert <index> <value> <list>`** - Insert an element into a list
- **Parameters**:
  - `index` (required): `Integer` - Index position to insert
  - `value` (required): `Any` - Value to insert
  - `list` (required): `List` - Target list
- **Returns**: `List` - New list after inserting the element

**`rev <list>`** - Reverse a list
- **Parameters**: `list` (required): `List` - The list to reverse
- **Returns**: `List` - New reversed list

**`flatten <collection>`** - Flatten a nested list
- **Parameters**: `collection` (required): `List` - Nested list to flatten
- **Returns**: `List` - Flattened list

## Element Access Functions

### Basic Access Operations

**`first <list>`** - Get the first element of a list
- **Parameters**: `list` (required): `List` - The list to access
- **Returns**: `Any` - The first element
- **Error**: Throws an error for an empty list
- **Example**: `List.first([1, 2, 3])` returns `1`

**`last <list>`** - Get the last element of a list
- **Parameters**: `list` (required): `List` - The list to access
- **Returns**: `Any` - The last element
- **Error**: Throws an error for an empty list
- **Example**: `List.last([1, 2, 3])` returns `3`

**`at <index> <list>`** - Get the element at a specified position
- **Parameters**:
  - `index` (required): `Integer` - Element index, supports negative indexing (counting from the end)
  - `list` (required): `List` - The list to access
- **Returns**: `Any` - Element at the specified position
- **Example**:
  - `List.at(1, [1, 2, 3])` returns `2`
  - `List.at(-1, [1, 2, 3])` returns `3` (last element)

### List Slicing Operations

**`take <count> <list>`** - Get the first n elements
- **Parameters**:
  - `count` (required): `Integer` - Number of elements to retrieve
  - `list` (required): `List` - Source list
- **Returns**: `List` - New list containing the first n elements
- **Example**: `List.take(2, [1, 2, 3, 4])` returns `[1, 2]`

**`drop <count> <list>`** - Skip the first n elements and return the remainder
- **Parameters**:
  - `count` (required): `Integer` - Number of elements to skip
  - `list` (required): `List` - Source list
- **Returns**: `List` - New list after skipping the first n elements
- **Example**: `List.drop(2, [1, 2, 3, 4])` returns `[3, 4]`

## Search Operation Functions

**`contains <item> <list>`** - Check if the list contains an element
- **Parameters**:
  - `item` (required): `Any` - Element to search for
  - `list` (required): `List` - Source list
- **Returns**: `Boolean` - Returns true if the element is found

**`find <item|fn> [start_index] <list>`** - Find the index of an element
- **Parameters**:
  - `item` (required): `Any|Function` - Element or function to search for
  - `start_index` (optional): `Integer` - Index to start searching from
  - `list` (required): `List` - Source list
- **Returns**: `Integer|None` - Index of the element, returns None if not found
- **Example**: `List.find(3, [1, 2, 3, 4])` returns `2`

**`find_last <item|fn> [start_index] <list>`** - Find the last index of an element
- **Parameters**:
  - `item` (required): `Any|Function` - Element or function to search for
  - `start_index` (optional): `Integer` - Index to start searching from
  - `list` (required): `List` - Source list
- **Returns**: `Integer|None` - Last index of the element, returns None if not found

## List Modification Functions

### Element Addition Operations

**`append <element> <list>`** - Add an element to the end of the list
- **Parameters**:
  - `element` (required): `Any` - Element to add
  - `list` (required): `List` - Target list
- **Returns**: `List` - New list after adding the element
- **Example**: `List.append(4, [1, 2, 3])` returns `[1, 2, 3, 4]`

**`prepend <element> <list>`** - Add an element to the beginning of the list
- **Parameters**:
  - `element` (required): `Any` - Element to add
  - `list` (required): `List` - Target list
- **Returns**: `List` - New list after adding the element
- **Example**: `List.prepend(0, [1, 2, 3])` returns `[0, 1, 2, 3]`

### List Combination Operations

**`unique <list>`** - Remove duplicate elements while maintaining order
- **Parameters**: `list` (required): `List` - List to deduplicate
- **Returns**: `List` - New list after deduplication
- **Example**: `List.unique([1, 2, 2, 3, 1])` returns `[1, 2, 3]`

**`split_at <index> <list>`** - Split the list at a specified position
- **Parameters**:
  - `index` (required): `Integer` - Position to split
  - `list` (required): `List` - List to split
- **Returns**: `List[List]` - A list containing two sublists [first half, second half]
- **Example**: `List.split_at(2, [1, 2, 3, 4])` returns `[[1, 2], [3, 4]]`

**`sort [key_fn|key_list|keys...] <string|list>`** - Sort a list or string
- **Parameters**:
  - `key_fn` (optional): `Function|Lambda` - Sorting key function
  - `key_list` (optional): `List[String]` - Sorting key list (for map lists)
  - `keys` (optional): `String...` - Sorting key names (for map lists)
  - `data` (required): `List|String` - Data to sort
- **Returns**: `List` - New sorted list

**`group <key_fn|key> <list>`** - Group by key function or key name
- **Parameters**:
  - `key_fn` (required): `Function|Lambda|String` - Grouping key function or key name
  - `list` (required): `List` - List to group
- **Returns**: `Map` - Grouping result, with keys as group identifiers and values as lists of elements

### Element Removal and Modification Operations

**`remove_at <index> [count] <list>`** - Remove n elements from the specified index
- **Parameters**:
  - `index` (required): `Integer` - Index to start removing from
  - `count` (optional): `Integer` - Number of elements to remove, defaults to 1
  - `list` (required): `List` - Source list
- **Returns**: `List` - New list after removing elements

**`remove <item> [all?] <list>`** - Remove the first matching element
- **Parameters**:
  - `item` (required): `Any` - Element to remove
  - `all` (optional): `Boolean` - Whether to remove all matching elements
  - `list` (required): `List` - Source list
- **Returns**: `List` - New list after removing elements

**`set <index> <value> <list>`** - Set the element at the specified index
- **Parameters**:
  - `index` (required): `Integer` - Index position to set
  - `value` (required): `Any` - New element value
  - `list` (required): `List` - Source list
- **Returns**: `List` - New list after setting the element

### Creation Operations

**`concat <list1|item1> <list2|item2> ...`** - Concatenate multiple lists or elements
- **Parameters**: `items` (variable): `List|Any...` - Lists or elements to concatenate
- **Returns**: `List` - New list after concatenation
- **Example**: `List.concat([1, 2], [3, 4], 5)` returns `[1, 2, 3, 4, 5]`

**`from <range|item...>`** - Create a list from a range or elements
- **Parameters**: `range|item` (variable): `Range|Any...` - Range or list of elements
- **Returns**: `List` - Newly created list
- **Example**:
    - `List.from(1..3)` returns `[1, 2, 3]`
    - `List.from(1, 2, 3)` returns `[1, 2, 3]`

## Functional Programming Operations

### Mapping and Filtering

**`map <fn> <list>`** - Apply a function to each element
- **Parameters**:
    - `fn` (required): `Function|Lambda` - Function to apply
    - `list` (required): `List` - Source list
- **Returns**: `List` - New list after applying the function
- **Example**: `List.map((x -> x * 2), [1, 2, 3])` returns `[2, 4, 6]`

**`filter <fn> <list>`** - Filter elements by condition
- **Parameters**:
    - `fn` (required): `Function|Lambda|Expression` - Filtering condition
    - `list` (required): `List` - Source list
- **Returns**: `List` - New list containing elements that meet the condition
- **Special Variables**:
    - `LINENO` - Current element index
    - `LINES` - Total length of the list
- **Example**: `List.filter((x -> x > 2), [1, 2, 3, 4])` returns `[3, 4]`

**`filter_map <fn> <list>`** - Filter and map in one step
- **Parameters**:
    - `fn` (required): `Function|Lambda` - Transformation function, returns None to filter out
    - `list` (required): `List` - Source list
- **Returns**: `List` - New list after transformation and filtering

### Aggregation Operations

**`reduce <fn> <init> <list>`** - Reduce a list using an accumulator function
- **Parameters**:
    - `fn` (required): `Function|Lambda` - Accumulator function, accepts (accumulated value, current element)
    - `init` (required): `Any` - Initial accumulated value
    - `list` (required): `List` - Source list
- **Returns**: `Any` - Final accumulated result
- **Example**: `List.reduce((acc, x) -> acc + x, 0, [1, 2, 3])` returns `6`

**`any <fn> <list>`** - Test if any element passes the condition
- **Parameters**:
    - `fn` (required): `Function|Lambda` - Test function
    - `list` (required): `List` - Source list
- **Returns**: `Boolean` - Returns true if any element passes the test

**`all <fn> <list>`** - Test if all elements pass the condition
- **Parameters**:
    - `fn` (required): `Function|Lambda` - Test function
    - `list` (required): `List` - Source list
- **Returns**: `Boolean` - Returns true if all elements pass the test

**`each <fn> <list>`** - Execute a function on each element (side-effect operation)
- **Parameters**:
    - `fn` (required): `Function|Lambda` - Function to execute
    - `list` (required): `List` - Source list
- **Returns**: `None` - No return value
- **Purpose**: Perform side-effect operations, such as printing or modifying external state

## Conversion Operations

**`join <separator> <list>`** - Join a list of strings into a single string
- **Parameters**:
    - `separator` (required): `String` - Separator
    - `list` (required): `List[String]` - List of strings
- **Returns**: `String` - The joined string
- **Example**: `List.join(", ", ["a", "b", "c"])` returns `"a, b, c"`

**`to_map [key_fn] [val_fn] <list>`** - Convert a list to a map
- **Parameters**:
    - `key_fn` (optional): `Function|Lambda` - Key extraction function
    - `val_fn` (optional): `Function|Lambda` - Value extraction function
    - `list` (required): `List` - Source list
- **Returns**: `Map` - Converted map
- **Example**:
    - `List.to_map((x -> x.id), (y -> y.name), [{id: 1, name: "wang"}, {id: 2, name: "fang"}])` returns `{1: "wang", 2: "fang"}`
    - `List.to_map((x -> x.id), [{id: 1}, {id: 2}])` returns `{1: {id: 1}, 2: {id: 2}}`
    - `List.to_map(["Q1", 45, "Q2", 52, "Q3", 49, "Q4", 87])` returns `{"Q1": 45, "Q2": 52, "Q3": 49, "Q4": 87}`

**`items <list>`** - Get index-value pairs list
- **Parameters**: `list` (required): `List` - Source list
- **Returns**: `List[List]` - List of index-value pairs
- **Example**: `List.items(["a", "b"])` returns `[[0, "a"], [1, "b"]]`

## Structural Operation Functions

### Matrix Operations

**`transpose <matrix>`** - Transpose a matrix (list of lists)
- **Parameters**: `matrix` (required): `List[List]` - Matrix to transpose
- **Returns**: `List[List]` - Transposed matrix
- **Example**: `List.transpose([[1, 2], [3, 4]])` returns `[[1, 3], [2, 4]]`

**`chunk <size> <list>`** - Split a list into chunks of specified size
- **Parameters**:
    - `size` (required): `Integer` - Size of the chunks
    - `list` (required): `List` - Source list
- **Returns**: `List[List]` - List of chunks
- **Example**: `List.chunk(2, [1, 2, 3, 4, 5, 6, 7])` returns `[[1, 2], [3, 4], [5, 6], [7]]`

### Pairing Operations

**`zip <list1> <list2>`** - Pair two lists
- **Parameters**:
    - `list1` (required): `List` - First list
    - `list2` (required): `List` - Second list
- **Returns**: `List[List]` - List of pairs
- **Example**: `List.zip([1, 2, 3], ["a", "b", "c"])` returns `[[1, "a"], [2, "b"], [3, "c"]]`

**`unzip <list_of_pairs>`** - Unzip a paired list
- **Parameters**: `list_of_pairs` (required): `List[List]` - Paired list
- **Returns**: `List[List]` - List containing two lists
- **Example**: `List.unzip([[1, "a"], [2, "b"]])` returns `[[1, 2], ["a", "b"]]`

### Folding Operations

**`foldl <fn> <init> <list>`** - Fold a list from left to right
- **Parameters**:
    - `fn` (required): `Function|Lambda` - Accumulator function, accepts (accumulated value, current element)
    - `init` (required): `Any` - Initial accumulated value
    - `list` (required): `List` - Source list
- **Returns**: `Any` - Final accumulated result
- **Example**: `List.foldl((acc, x) -> acc + x, 0, [1, 2, 3])` returns `6`

**`foldr <fn> <init> <list>`** - Fold a list from right to left
- **Parameters**:
    - `fn` (required): `Function|Lambda` - Accumulator function, accepts (current element, accumulated value)
    - `init` (required): `Any` - Initial accumulated value
    - `list` (required): `List` - Source list
- **Returns**: `Any` - Final accumulated result
- **Example**: `List.foldr((x, acc) -> acc + x, 0, [1, 2, 3])` returns `6`

## Usage Examples

### Functional Programming Examples
```bash
# Data processing pipeline
[1, 2, 3, 4, 5] | List.filter((x -> x > 2)) | List.map((x -> x * 2))
# Result: [6, 8, 10]

# Statistical operations
List.sum([1, 2, 3, 4, 5])  # Returns 15
List.average([1, 2, 3, 4, 5])  # Returns 3

# Grouping and aggregation
users | List.group("department") | Map.map((dept, users) -> List.len(users))
```

### Structural Operation Examples
```bash
# Matrix transposition
List.transpose([[1, 2, 3], [4, 5, 6]])  # Returns [[1, 4], [2, 5], [3, 6]]

# List chunking
List.chunk(2, [1, 2, 3, 4, 5, 6, 7])  # Returns [[1, 2], [3, 4], [5, 6], [7]]

# List pairing
List.zip([1, 2, 3], ["a", "b", "c"])  # Returns [[1, "a"], [2, "b"], [3, "c"]]
```

## Notes

The List module provides comprehensive list processing capabilities, supporting functional programming paradigms and immutable data operations. All functions are optimized, offering consistent error handling and type safety. Parameter type descriptions indicate that `<>` denotes required parameters, `[]` denotes optional parameters, and `...` denotes variable parameters. It is particularly important to use parentheses around lambda expressions when using higher-order functions to avoid parsing ambiguities.

In practical use, chained calls are supported, such as:
```bash
[1, 2, 3].max()
```
In the examples, type names are used for clarity.
