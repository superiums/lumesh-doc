---
title: Lumesh Map Module  
date: 2025-07-13 19:16:45  
highlight: true  
tags:  
 - libs  
 - map  
 - dictionary  
categories:  
 - wiki  
 - libs  
---

The Map module provides comprehensive key-value pair data structure operation functions, supporting the creation, querying, modification, merging, and conversion of maps. All functions are designed to be immutable, returning new maps without modifying the original map.

## Function Overview

| Function Category | Main Functions | Purpose |
|-------------------|----------------|--------|
| **Print Output** | `pprint` | Pretty print maps |
| **Basic Operations** | `len`, `insert`, `flatten` | Basic data operations |
| **Check Operations** | `has` | Check if a key exists |
| **Data Retrieval** | `get`, `at`, `items`, `keys`, `values` | Retrieve map data |
| **Search Operations** | `find`, `filter` | Conditional search and filtering |
| **Modification Operations** | `remove`, `set` | Modify map contents |
| **Creation Operations** | `from_items` | Create a map from data |
| **Set Operations** | `union`, `intersect`, `difference`, `merge` | Operations between maps |
| **Conversion Operations** | `map` | Map transformation |

## Print Output Functions

**`pprint <map>`** - Pretty print a map  
- **Parameters**: `map` (required): `Map` - The map to print  
- **Purpose**: Display map data in a table format  

## Basic Operation Functions

**`len <map>`** - Get the length of a map  
- **Parameters**: `map` (required): `Map` - The map to calculate length  
- **Returns**: `Integer` - Number of key-value pairs in the map  

**`insert <key> <value> <map>`** - Insert a key-value pair into a map  
- **Parameters**:
  - `key` (required): `String` - Key name  
  - `value` (required): `Any` - Value  
  - `map` (required): `Map` - Target map  
- **Returns**: `Map` - New map after inserting the key-value pair  

**`flatten <map>`** - Flatten a nested map structure  
- **Parameters**: `map` (required): `Map` - Nested map to flatten  
- **Returns**: `Map` - Flattened map  

## Check Operation Functions

**`has <key> <map>`** - Check if the map contains the specified key  
- **Parameters**:
  - `key` (required): `String` - Key name to check  
  - `map` (required): `Map` - Source map  
- **Returns**: `Boolean` - Returns true if the key is present  

## Data Retrieval Functions

**`get <path> <map|list|range>`** - Access values from nested structures using dot notation  
- **Parameters**:
  - `path` (required): `String` - Access path, supports dot separation  
  - `data` (required): `Map|List|Range` - Data structure to access  
- **Returns**: `Any` - Value at the specified path  

**`at <key> <map>`** - Get the value of the specified key from the map  
- **Parameters**:
  - `key` (required): `String` - Key name  
  - `map` (required): `Map` - Source map  
- **Returns**: `Any` - Value corresponding to the key  
- **Error**: Throws an error if the key does not exist  

**`items <map>`** - Get a list of key-value pairs from the map  
- **Parameters**: `map` (required): `Map` - Source map  
- **Returns**: `List[List]` - List of key-value pairs, each element is [key, value]  
- **Example**: `Map.items({"a": 1, "b": 2})` returns `[["a", 1], ["b", 2]]`  

**`keys <map>`** - Get all keys from the map  
- **Parameters**: `map` (required): `Map` - Source map  
- **Returns**: `List[String]` - List of key names  

**`values <map>`** - Get all values from the map  
- **Parameters**: `map` (required): `Map` - Source map  
- **Returns**: `List[Any]` - List of values  

## Search Operation Functions

**`find <predicate_fn> <map>`** - Find the first matching key-value pair  
- **Parameters**:
  - `predicate_fn` (required): `Function|Lambda` - Predicate function, accepts (key, value) parameters  
  - `map` (required): `Map` - Source map  
- **Returns**: `List|None` - Matching key-value pair [key, value], returns None if not found  

**`filter <predicate_fn> <map>`** - Filter the map by condition  
- **Parameters**:
  - `predicate_fn` (required): `Function|Lambda` - Filtering function, accepts (key, value) parameters  
  - `map` (required): `Map` - Source map  
- **Returns**: `Map` - New map containing key-value pairs that meet the condition  

## Modification Operation Functions

**`remove <key> <map>`** - Remove the specified key from the map  
- **Parameters**:
  - `key` (required): `String` - Key name to remove  
  - `map` (required): `Map` - Source map  
- **Returns**: `Map` - New map after removing the key  

**`set <key> <value> <map>`** - Set the value of an existing key in the map  
- **Parameters**:
  - `key` (required): `String` - Key name (must already exist)  
  - `value` (required): `Any` - New value  
  - `map` (required): `Map` - Source map  
- **Returns**: `Map` - New map after setting the value  
- **Error**: Throws an error if the key does not exist  

## Creation Operation Functions

**`from_items <items>`** - Create a map from a list of key-value pairs  
- **Parameters**: `items` (required): `List[List]` - List of key-value pairs, each element is [key, value]  
- **Returns**: `Map` - Created map  
- **Example**: `Map.from_items([["a", 1], ["b", 2]])` returns `{"a": 1, "b": 2}`  

## Set Operation Functions

**`union <map1> <map2>`** - Merge two maps  
- **Parameters**:
  - `map1` (required): `Map` - First map  
  - `map2` (required): `Map` - Second map  
- **Returns**: `Map` - Merged map (values from map2 will overwrite values from map1 with the same keys)  

**`intersect <map1> <map2>`** - Get the intersection of two maps  
- **Parameters**:
  - `map1` (required): `Map` - First map  
  - `map2` (required): `Map` - Second map  
- **Returns**: `Map` - New map containing keys common to both maps  

**`difference <map1> <map2>`** - Get the difference between two maps  
- **Parameters**:
  - `map1` (required): `Map` - First map  
  - `map2` (required): `Map` - Second map  
- **Returns**: `Map` - New map containing keys that are in map1 but not in map2  

**`merge <map1> <map2> [<map3> ...]`** - Recursively merge multiple maps  
- **Parameters**:
  - `map1` (required): `Map` - Base map  
  - `map2` (required): `Map` - Map to merge  
  - `map3...` (optional): `Map...` - Additional maps to merge  
- **Returns**: `Map` - Deeply merged map  
- **Purpose**: For nested maps, it will recursively merge instead of simply overwriting  

## Conversion Operation Functions

**`map <key_fn> <val_fn> <map>`** - Transform the keys and values of a map  
- **Parameters**:
  - `key_fn` (required): `Function|Lambda` - Key transformation function  
  - `val_fn` (optional): `Function|Lambda` - Value transformation function, defaults to identity function  
  - `map` (required): `Map` - Source map  
- **Returns**: `Map` - New map after transformation  

## Usage Examples

### Basic Operation Examples
```bash
# Create and manipulate maps
let user = {"name": "Alice", "age": 30}
Map.has("name", user)  # Returns true
Map.at("name", user)   # Returns "Alice"
Map.keys(user)         # Returns ["name", "age"]
```

### Set Operation Examples
```bash
# Map merging
let map1 = {"a": 1, "b": 2}
let map2 = {"b": 3, "c": 4}
Map.union(map1, map2)     # Returns {"a": 1, "b": 3, "c": 4}
Map.intersect(map1, map2) # Returns {"b": 2}
Map.difference(map1, map2) # Returns {"a": 1}
```

### Functional Operation Examples
```bash
# Filtering and transforming
users | Map.filter((k, v) -> v.age > 18) | Map.map((k, v) -> k.toUpper(), (k, v) -> v.name)
```

## Notes

The Map module provides comprehensive capabilities for handling key-value pair data structures, supporting functional programming paradigms and immutable data operations. All functions are optimized, offering consistent error handling and type safety. Parameter type descriptions indicate that `<>` denotes required parameters, `[]` denotes optional parameters, and `...` denotes variable parameters.

It is particularly important to note that the `set` function can only modify existing keys, while the `insert` function can add new keys.

In practical use, chained calls are supported, such as:
```bash
let map1 = {"a": 1, "b": 2}
map1.get('a')
```
In the examples, type names are used for clarity.
