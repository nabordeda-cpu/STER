# STER 1.0 Guide

STER 1.0 Beta language · STER Compiler 1.0

## 1. Running a program

```text
ster main.ster     Run a program
ster -version      Show the version
ster -help         Show help
```

The main program file uses the `.ster` extension. Module files use the `.st` extension.

## 2. Program structure

All code must be written inside `{ }` blocks. The block order is:

1. Module imports (`module.set`)
2. Variable declarations (`var`)
3. Custom commands (`cmd_custom`)
4. Main code

The module and variable blocks may appear in either order. Both must come before `cmd_custom` and the main code. Comments begin with `#`.

```ster
{
module.set(random)
}

{
var
name = "alex"
x = 10
}

{
cmd_custom
    (create_com(double(sum(2))))
}

{
txt_print("Hello", var=name)
}
```

Code outside `{ }` causes error 50. An invalid block order causes error 53.

## 3. Variables

Variables can only be declared inside a `var` block. Only declarations are allowed in this block.

```ster
{
var
x = 1
price = 2.5
name = "alex"
numbers = [10, 20, 30]
}
```

- Variable names are written without quotation marks. Quotation marks are used for strings.
- Supported types: integers, floating-point numbers, strings, and lists.
- Redeclaring a variable causes error 21. Using an undeclared variable causes error 20.
- A variable can be referenced as `var=name` or simply as `name`.

System variables are always available and must not be declared (error 22): `result`, `ms`, `s`, `minutes`, and `hours`.

Reserved words: `var`, `if`, `then`, `else`, `loop`, `module.set`, `txt_print`, `input`, `sum_var`, `wrt_`, `cmd_custom`, `create_com`, `get.time`, `list_add`, `list_get`, `sum`.

## 4. Output: `txt_print`

```ster
txt_print("Hello", "ster!")      # Hello ster!
txt_print(var=name)               # alex
txt_print("x =", var=x)           # x = 10
txt_print("")                     # Prints an empty line
```

Arguments are printed separated by spaces, followed by a newline. Lists are printed in the form `[10, 20, 30]`.

## 5. Input: `input`

```ster
input(var=b)
```

`input(var=b)` reads a line and stores it in `b`. If the input is a number, the value is stored as a number; otherwise, it is stored as a string. No prompt is printed automatically; use `txt_print` to display one.

Calling `input()` without a variable stores the input in `result`. If the input stream ends, error 32 occurs.

## 6. Calculations: `sum_var`

```ster
sum_var(x + 5)       # result = x + 5; x remains unchanged
sum_var(10 + 20)     # result = 30
wrt_(x)              # Copies result into x
```

Every calculation stores its result in `result`; variables used in the expression are not changed. To save the result to a variable, use `wrt_(name)`.

| Operator | Action |
|---|---|
| `+`, `-`, `*` | Addition, subtraction, multiplication |
| `/` | Division. For example, `10 / 4 = 2.5`, while `10 / 5 = 2` |
| `%` | Remainder |

Parentheses and negative numbers are supported: `sum_var((a + 2) * 3)`. Division by zero causes error 40; integer overflow causes error 41. Arithmetic on strings and lists causes errors 30 or 31.

## 7. Conditions

```ster
if(var=age > 10)
then {
    txt_print("Access allowed")
}
else {
    txt_print("Access denied")
}
```

Comparison operators (available only in `if` conditions):

- `=` — equal to
- `!=` — not equal to
- `<` — less than
- `>` — greater than

The `else` block is optional, and conditions can be nested. Numbers can be compared with numbers, and strings with strings. Other type combinations cause error 30. An empty block causes error 54.

## 8. Loops

```ster
loop(5) {
    txt_print("Hello")
}

loop(var=x) {
    txt_print("Loops work!")
}
```

The iteration count can be a number or a variable. A negative or non-integer count causes error 62. Zero is allowed. Loops can be nested.

## 9. Lists

```ster
{
var
numbers = [10, 20, 30]
}

{
txt_print(list_get(var=numbers, 0))     # Prints 10
list_add(var=numbers, 40)
list_get(var=numbers, 3)                # result = 40
}
```

- Indices start at 0. An invalid index causes error 61.
- Lists can contain numbers and strings. Nested lists are not allowed (error 34).
- As a standalone statement, `list_get(...)` stores the element in `result`. Inside `txt_print(...)` and `sum_var(...)`, it returns the element as a value.

## 10. Time: `get.time`

```ster
get.time()           # Stores ms, s, minutes, and hours
get.time(minutes)    # Stores only minutes
```

`ms` means milliseconds, `s` means seconds, and `minutes` and `hours` have their usual meanings. These system variables are populated automatically; do not use `wrt_` to set them.

## 11. Custom commands

Custom commands are declared in a `{ cmd_custom ... }` block before the main code.

Example: a command that doubles a value:

```ster
{
cmd_custom
    (create_com(double(sum(2))))
}
```

`double(var=a)` stores `a * 2` in `result` without changing `a`. To save the result back to `a`, add `wrt_(a)`. `double(7)` stores `14` in `result`.

A command can also be based on another command:

```ster
{
cmd_custom
    (create_com(fasttime(get.time(minutes))))
}
```

Custom commands can contain multiple actions separated by `;` and can use parameters:

```ster
{
var
hp = 100
}

{
cmd_custom
    (create_com(damage(sum_var(hp - var=amount); wrt_(hp))))
}

{
damage(20)      # hp is now 80
}
```

An identifier in the form `var=name` that is not declared in the `var` block is treated as a parameter. Arguments are assigned to parameters in order of their first appearance in the command body. For example, `random(1, 10)` passes `1` to `lo` and `10` to `hi`. Parameters are local to the command; other variables are shared.

Rules:

- The number of arguments must match the number of parameters (error 25). An unknown command causes error 23. A duplicate command name causes error 24.
- A command can call other commands and itself. The call depth is limited to 200 (error 60).
- A command is not a value. `txt_print(random(1, 10))` is invalid. Instead, call the command and then print `result`:

```ster
random(1, 10)
txt_print(var=result)
```

## 12. Modules

```ster
{
module.set(random)
}
```

Module names are written without quotation marks. The module file is searched for in this order:

1. `modules/name.st` next to the main program file
2. `name.st` next to the main program file
3. The STER standard library

A module is loaded only once. Modules can import other modules. Module variables and commands are shared with the program. A missing module or a circular import causes error 26.

Module files may contain only `module.set`, `var`, and `cmd_custom` blocks. Main program code is not allowed in a module.

Standard modules:

| Module | Commands |
|---|---|
| `random` | `random(lo, hi)`: stores a random integer from `lo` to `hi` in `result` |
| `time` | `fasttime()`: stores the current time in `minutes` |

## 13. Errors

Error format:

```text
NameError (code: 20)
File: main.ster
Line 4: txt_print(score)
^^^^^^^^^^^^^^^^^^^^^^^^

Description: Variable 'score' is not declared.
```

Every error includes a category, code, filename, line number, source line, and description. Errors are displayed separately from regular output, and the program exits with code 1.

| Category | Codes |
|---|---|
| `SyntaxError` | 1 `UnexpectedToken`, 2 `MissingExpression`, 3 `MissingParenthesis`, 4 `InvalidSyntax`, 5 `MissingOperator`, 6 `InvalidAssignment` |
| `NameError` | 20 `UndefinedVariable`, 21 `DuplicateVariable`, 22 `ReservedName`, 23 `UndefinedFunction`, 24 `DuplicateFunction`, 25 `ArgumentCountError`, 26 `ModuleNotFoundError` |
| `TypeError` | 30 `TypeMismatch`, 31 `InvalidOperation`, 32 `InvalidInputType`, 34 `InvalidListElement` |
| `MathError` | 40 `DivisionByZero`, 41 `NumericOverflow`, 42 `InvalidNumber` |
| `BlockError` | 50 `MissingOpeningBlock`, 51 `MissingClosingBlock`, 52 `UnexpectedClosingBlock`, 53 `InvalidBlockPlacement`, 54 `EmptyRequiredBlock` |
| `RuntimeError` | 60 `StackOverflow`, 61 `IndexOutOfRange`, 62 `InvalidLoopCount`, 63 `FunctionNotCallable` |

Syntax, name, and block errors are detected before the program runs, so no program output is produced. Type, math, and runtime errors occur during execution; any text already printed remains visible.

## 14. Installation

- **Android (Termux):** Place `STER-Compiler-src.zip` in Downloads and run `sh ~/install-ster.sh`. The script builds and installs the compiler.
- **Linux:** Extract `ster-1.0-linux-x86_64.tar.gz` and run `sudo ./install.sh`.

To verify the installation, run:

```text
ster -version
```

## 15. Command reference

| Command | Description |
|---|---|
| `module.set(name)` | Loads the `name.st` module |
| `var` | Declares a variable block |
| `cmd_custom (create_com(...))` | Declares a custom command |
| `txt_print(a, b, ...)` | Prints arguments separated by spaces, followed by a newline |
| `input()` or `input(var=x)` | Reads a line and stores it in `result` or variable `x` |
| `sum_var(expression)` | Evaluates an expression and stores the result in `result` |
| `wrt_(name)` | Copies `result` into variable `name` |
| `if(a ? b) then {...} else {...}` | Conditional; `?` is one of `=`, `!=`, `<`, `>` |
| `loop(n) {...}` | Repeats a block `n` times |
| `list_add(var=list, value)` | Appends a value to a list |
| `list_get(var=list, index)` | Retrieves an element, storing it in `result` or returning it as a value |
| `get.time()` or `get.time(field)` | Stores all time fields, or only the specified field |
| `name(arguments)` | Calls a custom command |

Where a single value is required (for example, as an argument to `txt_print`, `list_add`, `loop`, or a custom command), you can use a number, a string, a variable (`x` or `var=x`), or `list_get(...)`. Full expressions can be used in `sum_var` and `if`.

## 16. Expressions and types

- **Precedence:** parentheses, unary minus, then `*`, `/`, `%`, followed by `+` and `-`. Operators with the same precedence are evaluated from left to right.
- An operation between integers produces an integer. An operation involving a floating-point number produces a floating-point number. Division produces an integer only when the division is exact: `10 / 5 = 2`, while `10 / 4 = 2.5`.
- Integers range from `-9223372036854775808` to `9223372036854775807`. Exceeding these limits causes error 41.
- Floating-point numbers are printed with up to 12 significant digits. For example, `0.1 + 0.2` is printed as `0.3`.
- Strings and lists are not allowed in arithmetic: error 30 is raised when operand types differ, and error 31 when they are the same.
- Numbers can be compared with numbers (for example, `1 = 1.0` is true), and strings can be compared with strings in character order (for example, `"apple" < "banana"`). Other comparisons cause error 30; comparing lists causes error 31.
- Input `42` is parsed as the number `42`, `2.5` as the number `2.5`, and any other input as a string.
- Strings support `\"`, `\\`, `\n`, and `\t`. Cyrillic characters are supported.

## 17. Assigning a value to a variable

Any calculation or `list_get` operation stores its result in `result`. To save it to a variable, use `wrt_(name)`, which copies `result` into the specified variable.

```ster
sum_var(x + 7)
wrt_(x)          # x is now equal to x + 7
```

To assign a number using an expression, write `sum_var(0 + 7)` and then `wrt_(x)`.

Values can also be assigned when a variable is declared in a `var` block or by using `input(var=x)`. To store a string from input, call `input()` and then `wrt_(name)`.

`wrt_` copies a number or a string. It cannot write to a list (error 30) or to an undeclared variable (error 20).

## 18. Custom commands in detail

A command body can take these forms:

```text
create_com(double(sum(2)))                         Multiplies an argument by a number
create_com(fasttime(get.time(minutes)))            Calls a built-in command
create_com(twice(txt_print(var=w); txt_print(var=w)))  Performs multiple actions separated by ;
```

Example with a parameter:

```ster
{
cmd_custom
    (create_com(twice(txt_print(var=word); txt_print(var=word))))
}

{
twice("hi")
}
```

This prints `hi` twice.

Rules:

- A parameter is an identifier in the form `var=name` that is not declared in a `var` block. Parameters receive values in the order they first appear in the command body and are available only inside that command. Other variables are shared.
- The number of arguments in a call must match the number of parameters (error 25). A command without parameters is called as `name()`.
- A command declared with `sum(N)` accepts one argument and stores the multiplication result in `result`. It cannot be combined with named parameters.
- A command can call only commands declared earlier, or itself. The call depth is limited to 200 (error 60).
- A command name cannot be a reserved word (error 22) or a duplicate (error 24). Calling a variable as a command causes error 63; calling an unknown name causes error 23.

## 19. Block rules

| Code | When it occurs |
|---|---|
| 50 | Code appears outside `{ }`, including a `cmd_custom` block |
| 51 | A block is not closed; `}` is missing |
| 52 | An extra `}` appears |
| 53 | A `var` block appears after the main code; a `var` block contains something other than declarations; `cmd_custom` appears after the main code; `module.set` appears after `cmd_custom`; or main code appears in a module |
| 54 | An empty `{ }` block, or an empty body for `loop`, `then`, or `else` |

## 20. Example: a custom module

File `modules/game.st`:

```ster
{
var
    hp = 100
}

{
cmd_custom
    (create_com(damage(sum_var(hp - var=amount); wrt_(hp))))
}
```

File `main.ster`:

```ster
{
module.set(game)
}

{
damage(30)
txt_print("HP:", var=hp)
}
```

Output:

```text
HP: 70
```

Module variables are shared with the program, so the same variable name cannot be declared twice (error 21). A module is loaded only once, even if `module.set` names it multiple times.

## 21. Complete example: guess the number

```ster
{
module.set(random)
}

{
var
    target = 0
    guess = 0
}

{
random(1, 10)
wrt_(target)
txt_print("Guess a number from 1 to 10:")
input(var=guess)

if(var=guess = var=target)
then {
    txt_print("Correct!")
}
else {
    txt_print("No, the number was", var=target)
}
}
```

## 22. Common errors

| What you see | Cause | What to do |
|---|---|---|
| `SyntaxError 1` at `txt_print(random(1, 10))` | A command is used as a value | Call `random(1, 10)`, then use `txt_print(var=result)` |
| `BlockError 50` at `cmd_custom` | Missing `{ }` around the block | Wrap the block in `{ }` |
| `NameError 20` at `txt_print(hello)` | An unquoted word is treated as a variable | Write `"hello"` |
| `NameError 22` at `result = 0` | `result` is a system variable | Remove the declaration |
| `NameError 22` at `s = 1` | `s`, `ms`, `minutes`, and `hours` are reserved | Choose another name |
| `NameError 26` | The module was not found | Put `name.st` in `modules/` or next to the program |
| A variable did not change after `sum_var(a + b)` | The result is stored only in `result` | Add `wrt_(a)` |
| `MathError 40` in `random(10, 1)` | The second number is less than the first | Put the smaller number first |
| `TypeError 30` in `if` | A number is being compared with a string | Check that the input is a number |
| `RuntimeError 61` | The index is out of range; indexing starts at 0 | Use an index from `0` to `size - 1` |

## 23. File commands

File commands are built into the compiler; no module import is required. Paths use this form: `drive:folder(subfolder(file(arguments)))`.

```ster
{
file_mkdir(c:myproject(files))
file_create(c:myproject(notes.txt("Hello STER!")))
file_append(c:myproject(notes.txt(" More.")))
file_read(c:myproject(notes.txt(var=content)))
}
```

| Command | Description |
|---|---|
| `file_create` | Creates a file. Text is optional. The folder must exist (error 70), and the file must not already exist (error 71). |
| `file_write` | Replaces the contents of an existing file (error 74 if the file does not exist). |
| `file_append` | Appends text to a file (error 74 if the file does not exist). |
| `file_edit` | `("Apple", "STER")` replaces all matches. `("Apple", (line-3) "STER")` replaces matches only on line 3. If the text is not found, error 75 occurs. |
| `file_read` | Reads a file into `var=name` or into `result`. |
| `file_rename`, `file_copy` | Use `c:folder(old_name, new_name)`. If the new name already exists, error 71 occurs. |
| `file_delete`, `file_clear` | Deletes a file or clears its contents. |
| `file_mkdir`, `file_rmdir` | Creates nested folders; removes a folder and its contents. |
| `file_move` | Uses `c:source(file), c:destination(file)`. If the destination file already exists, error 71 occurs. |
| `file_size` | Use `var=size, unit=MB`. Units: `B`, `KB`, `MB`, `GB`. Without `unit`, the size is returned in bytes. |
| `file_check` | Use `file_check(dir c:folder)` or `file_check(file c:folder)`. Stores the text `"true"` or `"false"` in `result`. |
| `file_info` | `name=var=name`, `extension=var=ext`, `size=var=n`; also supports `created`, `modified`, and `path`. |

File error codes (`FileError`, range 70–100):

| Code | Name |
|---|---|
| 70 | `DirectoryNotFound` |
| 71 | `FileAlreadyExists` |
| 72 | `AccessDenied` |
| 73 | `WriteError` |
| 74 | `FileNotFound` |
| 75 | `TextNotFound` |
| 76 | `InvalidFilePath` |
| 77 | `ReadError` |
| 78 | `FileLocked` |
| 79 | `InvalidFileOperation` |

Codes 80–100 are unused.

Important:

- On Windows, `c:` refers to the actual C: drive. On Linux and Android, `c:` maps to `~/ster_drives/c`. This location can be changed with the `STER_DRIVES` environment variable.
- File and folder names may contain letters, digits, underscores (`_`), and periods. Hyphens, spaces, and names beginning with a digit are not supported. On Linux, names are case-sensitive: `notes.TXT` and `notes.txt` are different files.
- Paths containing `..` are not allowed. Symbolic links in paths are prohibited (error 76). The root of a drive cannot be deleted.
- Text to be written must be a string or a variable reference in the form `var=name`.


Installation

STER is available for Windows, Linux, and Android.

 Android (Termux)

1. Download `ster-1.0-android.tar.gz`.
2. Open Termux.
3. Navigate to the directory where you downloaded the archive.
4. Run the installation script:

sh install.sh

 Linux

1. Download `ster-1.0-linux-x86_64.tar.gz`.
2. Extract the archive.
3. Open a terminal in the extracted directory.
4. Run:

sudo ./install.sh

Windows

1. Download `windowsSTER-1.0-Setup.exe`.
2. Open the installer.
3. Follow the installation instructions.

Checking the installation

Open a terminal, CMD, or Termux and run:

ster -version

Expected output:

STER Compiler 1.0
Target:
platform: ...

The platform information depends on your operating system.
