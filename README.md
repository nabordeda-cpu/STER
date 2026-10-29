STER 1.0 Beta — Documentation

1. Variable Declaration

Variables must be declared at the beginning of the code. This block must contain only variable declarations and nothing else.

For example:

{
var
x = 1
b = 2
"name" = "alex"
}

Variable names must be written in quotation marks, such as ""name"".

Example of incorrect code:

{
var
x = 1
b = 3
txt_print # Error
}

The "var" block must always be at the beginning of the code or after module imports.

2. Modules

Modules extend the language's functionality and allow you to use commands that are defined in a module instead of creating them directly in your code.

Module imports usually go before the "var" block, but they can also be placed after the "var" block, provided they appear before the main code begins.

Importing modules

Modules are imported using "module.set(module_name)".

For example:

{
module.set(random)
module.set(another_module)
}

3. Output

To print something to the command line or terminal, use "txt_print()".

For example:

{
var
"name" = "alex"
x = 2
}

{
txt_print(var=name)
}

Output:

alex

You can also print text directly:

{
txt_print("Hello", "ster!")
}

Output:

Hello ster!

4. User Input

You can assign a variable's value directly from user input using "input()".

For example:

{
var
b = 0
}

{
input(var=b)
wrt_(var=result(mov_b))
}

- "input(var=b)" asks the user to enter a value for variable "b".
- "wrt_(var=result(mov_b))" writes the value from "result" into "b".

After any mathematical calculation or user input operation, the result is stored in "result". If you do not specify where the result should be stored, an error will occur.

5. Mathematical Operations

Calculations are performed using the "sum_var()" command.

For example:

sum_var(a + b)

This adds variables "a" and "b".

Operators

Operator| Description
"+"| Addition
"-"| Subtraction
"*"| Multiplication
"/"| Division
"<"| Less than (not used in this block)
">"| Greater than (not used in this block)
"="| Equal to (not used in this block; for example, "10 = 7" is false)
"!="| Not equal to (not used in this block; for example, "10 != 7" is true)

The last four operators are comparison operators, according to their intended use in this documentation.

6. Loops

Loops are implemented using "loop()".

For example:

{
var
x = 1
}

{
loop(var=x) {
    txt_print("Loops are working!")
}
}

You can also use a loop with a numeric repetition count:

loop((any_number))

Replace "any_number" with the desired number of repetitions.

7. Creating Custom Commands

To create your own commands, add "cmd_custom" near the beginning of the code, after the variable declarations and module imports.

For example:

cmd_custom
    (create_com(double(sum(2))))

You can then use the custom command:

{
double(var=a)
}

This multiplies the value of variable "a" by 2.

You can also define commands that work with time:

{
cmd_custom
    (create_com(fasttime(get.time(minutes))))
}

In this case, you do not need to use "wrt_". The system variables will be populated automatically.

8. Getting the Current Time

The "get.time()" command writes time values into four variables:

- Milliseconds
- Seconds
- Minutes
- Hours

9. Conditional Statements

The "if" command allows you to specify a condition.

For example:

{
var = 16
}

{
if(var=age > 10)
then {
    txt_print("Access allowed")
}
else {
    txt_print("Access denied")
}
}

- If the condition is true, the "then" block executes.
- If the condition is false, the "else" block executes.

10. Lists

Lists can be read from and modified by adding new elements.

Declaring a list

{
var
numbers = [10, 20, 30]
}

Reading from a list

txt_print(list_get(var=numbers, 10))

This retrieves an element from the list using the "list_get()" command. The exact indexing behavior depends on the language's implementation.

Adding an element to a list

list_add(var=numbers, 40)

This adds "40" to the "numbers" list.

11. Error Codes

Every error has its own error code.

Code range| Error category| Description
1–19| "SyntaxError"| Syntax errors
20–29| "NameError"| Variable, function, and module errors
30–39| "TypeError"| Data type and operation errors
40–49| "MathError"| Mathematical errors
50–59| "BlockError"| Block "{}" errors
60–69| "RuntimeError"| Errors that occur during execution

SyntaxError (1–19)

Code| Error name| Description
1| "UnexpectedToken"| Unexpected token
2| "MissingExpression"| Missing expression
3| "MissingParenthesis"| Missing or unclosed parenthesis
4| "InvalidSyntax"| Invalid syntax
5| "MissingOperator"| Missing operator
6| "InvalidAssignment"| Invalid assignment

NameError (20–29)

Code| Error name| Description
20| "UndefinedVariable"| Variable has not been declared
21| "DuplicateVariable"| Variable declared more than once
22| "ReservedName"| A reserved name was used
23| "UndefinedFunction"| Function not found
24| "DuplicateFunction"| Function declared more than once
25| "ArgumentCountError"| Incorrect number of arguments
26| "ModuleNotFoundError"| Module not found

TypeError (30–39)

Code| Error name| Description
30| "TypeMismatch"| Incompatible data types
31| "InvalidOperation"| Invalid operation
32| "InvalidInputType"| Invalid input data type
33| "InvalidReturnType"| Invalid return type
34| "InvalidListElement"| Invalid list element

MathError (40–49)

Code| Error name| Description
40| "DivisionByZero"| Division by zero
41| "NumericOverflow"| Numeric overflow
42| "InvalidNumber"| Invalid number
43| "MathDomainError"| Value outside the valid domain of a mathematical operation

BlockError (50–59)

Code| Error name| Description
50| "MissingOpeningBlock"| Missing opening "{"
51| "MissingClosingBlock"| Missing closing "}"
52| "UnexpectedClosingBlock"| Unexpected closing "}"
53| "InvalidBlockPlacement"| Block placed incorrectly
54| "EmptyRequiredBlock"| Required block is empty

RuntimeError (60–69)

Code| Error name| Description
60| "StackOverflow"| Maximum stack depth exceeded
61| "IndexOutOfRange"| List index out of range
62| "InvalidLoopCount"| Invalid loop repetition count
63| "FunctionNotCallable"| Object cannot be called as a function

12. Error Message Example

An error message may look like this:

NameError (code: 20)
Line 4: txt_print(score)
^^^^^^^^^^^^^^^^^^^^^^^^

Description: Variable 'score' is not declared.

This example indicates that the variable "score" has not been declared before it was used.
