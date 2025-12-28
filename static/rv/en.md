# Lume Vs Bash

---

| Comparison Item |    Lume       |     Bash      |     Dash      |     Fish      |
|-----------------|---------------|---------------|---------------|---------------|
| Speed (Million Loops) |     *****     |     ***       |     ****      |    *          |
| Interactivity    |     ****      |     **        |     *         |    *****      |
| Syntax           |     *****     |     **        |     *         |    ****       |
| Size             |     ****      |     ***       |     *****     |    **         |
| Error Messages   |     *****     |     *         |     *         |    ***        |
| Error Handling   |     *****     |     *         |     *         |    *          |

---

| Comparison Item |    Lume       |     Bash      |     Dash      |     Fish      |
|-----------------|---------------|---------------|---------------|---------------|
| Built-in Libraries |     *****     |               |               |    *         |
| Key Bindings     |     ☑         |               |               |      ☑        |
| Structured Pipelines |     ☑     |               |               |               |
| AI Interaction    |     ☑        |               |               |               |

---

 - Size and Memory Usage

|         |    Lume       |     Bash      |     Dash      |     Fish      |
|---------|---------------|---------------|---------------|---------------|
| Version |    v0.7.1    |    v5.3.0     |    v0.5.12    |   v4.0.2      |
| Size    |    6.6 MB     |    9.54 MB    |    153.8 KiB  |   21.64 MB    |
| Memory Usage |    7 MB     |    8 MB     |    2 MB       |   12 MB       |

---

### Performance Testing

| ![highlight](/images/mem_chart.png) | ![highlight](/images/time_chart.png) |
|------------------------|------------------------|


Million Loops: Over 10 times faster than Bash, with lower memory usage than Bash.

---

### Pipeline Efficiency
```bash
# Example of extracting all commands in the lf file manager
lf -remote `query $id cmds` >> /tmp/cmds
```

- Bash
```bash
# /tmp/cc.sh
for ((i=0;i<=100;i++))
do
	cat /tmp/cmds | awk -F'\t' 'NR>1 {print $1}'
done
```

- Lume
```bash
# /tmp/cc.lm
for i in 0..100 {
  Fs.read /tmp/cmds | String.lines() | List.drop(1) \
    | List.map(x -> {String.split("\t\t", $x) \
    | List.first()}) | print
}
```

---

Pipeline Execution Efficiency Comparison (100 times)

```bash
# Bash
> time bash /tmp/cc.sh
________________________________________________________
Executed in  511.61 millis    fish           external
   usr time  230.11 millis    1.69 millis  228.42 millis
   sys time  454.95 millis    0.00 millis  454.95 millis
```

```bash
# Lume
> time lume /tmp/cc.lm
________________________________________________________
Executed in   32.25 millis    fish           external
   usr time   25.65 millis  859.00 micros   24.79 millis
   sys time    6.79 millis  588.00 micros    6.20 millis
```

---

# Syntax Comparison

---

### Variable Usage

- Bash

```bash
declare -i a = 10
echo $a

if [ "$a" -gt 0 ]; then
    echo "Variable a is greater than zero"
fi
```

- Lume

```bash
let a = 10                # Non-strict mode, can omit let
echo $a                   # Non-strict mode, can omit $

if a > 0 {
    echo "Variable a is greater than zero"
}
```

---

### Arithmetic Operations

- Bash

```bash
$((a + b))                # Arithmetic addition
[ "$a" == "$b" ]          # Equality comparison
"$a$b"                    # String concatenation
[[ "str" =~ "pattern" ]]  # Inclusion comparison
```

- Lume

```bash
a + b                  # Arithmetic addition
a == b                 # Equality comparison
`$a$b`                 # String concatenation, can also use a + b
"str" ~: "pattern"     # Inclusion comparison
# In non-strict mode, $ can be omitted
```

---

### Multi-condition Judgement

- Bash

```bash
if [[ "$a" -gt 0 && "$b" -lt 0 ]]; then # Note the double brackets, quotes, and semicolon
    echo "Variables a and b are both greater than zero"
fi
```

- Lume

```bash
if a > 0 && b < 0 {
    echo "Variables a and b are both greater than zero"
}
```

---

### Array Usage

- Bash

```bash
my_array=(1 2 3)
echo "Length: " ${#my_array[@]}       # Remember the symbols?
for value in "${my_array[@]}"; do
    echo $value
done
```

- Lume

```bash
let my_array = [1, 2, 3]
print "Length: " my_array.len()
for value in my_array {
    print value
}
```

---

### Dictionary Usage

- Bash

```bash
declare -A my_dict
my_dict["key1"]="value1"
my_dict["key2"]="value2"

for key in "${!my_dict[@]}"; do    # Remember the symbols?
    echo "$key: ${my_dict[$key]}"
done
```

- Lume

```bash
let my_dict = {key1: value1, key2: value2}
for key in my_dict.keys() {
    print key my_dict[key]
}
```

---

### Loop Statements

- Bash

```bash
sum=0
for (( i=1; i<=10000; i++ )); do     # Note the double brackets and semicolon
    sum=$((sum + i))
done

echo "The sum from 1 to 10000 is: $sum"
```

- Lume

```bash
sum = 0
for i in 1..10000 {
    sum += i
}
print `The sum from 1 to 10000 is: $sum`
```

---

### Match Statements

- Bash

```bash
if [ $# -eq 0 ]; then          # Note the $# symbol
    echo "Please provide a parameter."; exit 1
fi
case "$1" in
    start)                     # Note the single quote syntax
        echo "Starting service..."
        ;;                     # Note the double semicolon syntax
    stop)
        echo "Stopping service..."
        ;;
    *)
        echo "Invalid parameter: \$1"
        ;;
esac
```

---

- Lume

```bash
if $argv.len() == 0 {
    print "Please provide at least one parameter."; exit 1
}

match $argv[0] {
    start => print "Starting service..."
    stop => print "Stopping service..."
    _ => print "Invalid parameter" $argv[0]
}
```

---

### Function Definition

- Bash

```bash
function add {
    a=$1; b=$2            # Cannot define parameters, can only read variables from $#
    local result=$((a + b))
    echo $result          # Cannot return execution result, can only print to standard output
}
sum=$(add 5 10)           # Capture result from standard output
```

- Lume
```bash
fn add(a, b = 0) {    # Can directly define parameters and set default values
    a + b             # Can directly return execution result (the last statement's return can be omitted)
}
sum = add(5, 10)     # Directly receive result
# Lume also supports higher-order functions, decorators, and other advanced features; it also supports Lambda and other convenient functions
```

---

### Error Handling

- Bash

```bash
ls /non_exist
# Cannot capture errors, can only check the error code to determine execution status
if [ $? -ne 0 ]; then
    echo "Error: Directory does not exist."
fi
```

- Lume

```bash
ls /none_exist ?: e -> pprint e
# Lume supports error capture and provides detailed error information:
# Such as error code, error message, syntax tree, expression, etc.
# Also supports convenient methods like error printing and error ignoring
```

---

### Pipelines

- Bash
```bash
# Bash pipelines can only handle text stream data, so must rely on external tools
# Find tmpfs type. (Fortunately, other columns do not have tmpfs.)
df -h | awk -F' ' '/tmpfs/{print $1,$2}' 
# Find used > 1G. (But what if there is 1TB?)
df -h | awk '$3 ~ /G/ && $3 + 0 > 1'       
```

- Lume
```bash
# Lume pipelines support structured data streams, so tasks can be efficiently completed without relying on third-party programs
df -h  | Into.table(fs, size, used, rest, up, mp)  \
  | where(fs == 'tmpfs') | select(mp, size)
df -h  | Into.table(fs, size, used, rest, up, mp) | .drop(1) \
  | where(used.to_filesize() > 1G )
```

---

- Lume's unique advanced pipelines
```bash
# Positional pipeline
1...6 |_ print 'all data come here:' _ 'instead of end'

# Loop dispatch pipeline
1...6 |> print 'one data come here:' _ 'instead of end'
#     This avoids the complexity of xargs parameters!
```

---

### Lazy Assignment, Multi-variable Assignment

- Lazy Assignment
```bash
let a := b + 3
let c := ls -l
eval c
```

- Multi-variable Definition
```bash
let a, b, c = 1, 2, 3
let a, b, c = 5
```

---

### More Convenience

- Destructuring Assignment
```bash
let [a, b, *c] = [1, 2, 3, 4, 5]
let {name: user, age} = {name: wang, age: 25}
```
- Providing Default Values
```bash
let a = $argv[0] ?: print "no args" && exit  # Exit directly if no parameters
let a = $argv[0] ?: 0                        # Provide default value
```

---

- More Types
```bash
let p = r'\w+'          # Regex type
let day = t'2025-7-20'  # Date type
let s = 3000M           # File size type
let range = 0..10       # Interval
let arr = 0...10        # Array
```

---

### Overloaded Operators
```bash
# Use conventional arithmetic operators to handle more common tasks:
[1, 2] + 3         # Array append
[1, 2] + [3, 4, 5]   # Array merge
{a: b} + c         # Dictionary append
{a: b} + {c: d}     # Automatic merge
"1" + 23          # Automatically convert to string, "123"
23 + "1"          # Automatically convert to number, 24
```

---

### Functional Programming

Built-in a large number of utility function libraries to achieve functional programming, such as:
- Collection Operations:     `List`
- File System:     `Fs`
- String Processing:   `String`, `Regex`
- Time Operations:     `Time`
- Data Conversion:     `Into`, `From`
- Mathematical Calculations:     `Math`, `Rand`
- UI Operations:       `Ui`
- Logging:     `Log`

```bash
0...10 | List.filter(x -> x % 2 == 0)
0..10 | .map(x -> x * 2)               # Pipeline method
```

---

### Chained Calls

```bash
"hello world".split(' ').join(',').to_upper().green()
# Equivalent to:
String.split(' ', "hello world") | List.join(',') | String.to_upper() | String.green()

data | .filter(x -> x > 0) | .join(',')
# Equivalent to:
List.filter(x -> x > 0, data) | List.join(',')
```
Chained calls can omit the corresponding library name and can be called consecutively.

---

# More Comparisons
- Syntax Highlighting
- Auto Suggestions
- Local AI Interaction
- Detailed Error Messages
- Key Bindings, Common Command Favorites
- More features await your discovery

---

### Thanks

- Documentation   [https://lumesh.codeberg.page](https://lumesh.codeberg.page)
- Source Code [https://codeberg.org/santo/lumesh](https://codeberg.org/santo/lumesh)

