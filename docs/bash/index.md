# Bash

Bash is both an interactive shell and a scripting language. This section covers
the fundamentals needed to work efficiently at the command line and build small,
reliable scripts for system administration, automation, and security testing.

The examples are designed for authorized lab environments and real-world
engagements where you have permission to test a system.

## Learning Path

### Shell and Script Fundamentals

- [Bash Shell Scripts](01.%20Bash%20Shell%20Script.md): Create, execute, and make
	scripts executable.
- [Variables](02.%20Variables.md): Store values, use parameter expansion, manage
	scope, export variables, and work with arrays.
- [Passing Arguments](03.%20Passing%20Arguments.md): Read positional arguments,
	validate input, and build reusable scripts.
- [Executing Shell Commands](04.%20Executing%20Shell%20Command.md): Use command
	substitution and understand subshell behavior.
- [Reading User Input](05.%20Reading%20User%20Input.md): Read single values,
	multiple values, and arrays from the terminal.

### Script Control and Data Handling

- [The `trap` Command](06.%20Bash%20trap%20Command.md): Handle signals, clean up
	temporary resources, and respond to errors.
- [Bash Arrays](07.%20Bash%20Arrays.md): Store collections of values and iterate
	over their contents.
- [`if`, `else`, and `fi` Statements](08.%20Bash%20if,%20else,%20and%20fi%20Statements.md):
	Branch based on conditions and file checks.
- [Comparisons](09.%20Bash%20Comparisons.md): Compare numbers and strings.
- [File Testing](10.%20Bash%20File%20Testing.md): Check files, directories,
	permissions, sizes, and symbolic links.
- [Loops](11.%20Bash%20Loops.md): Repeat work with `for`, `while`, and `until`.
- [Functions](12.%20Bash%20Function.md): Organize reusable commands and pass
	arguments between functions.

### Menus, Quoting, and Output

- [`select`](13.%20Bash%20Select.md): Build simple interactive terminal menus.
- [`case`](14.%20Bash%20Case%20Statement.md): Match values and patterns across
	multiple conditions.
- [Quotes and Quotations](15.%20Bash%20Quotes%20and%20Quotations.md): Control
	expansion, whitespace, escaping, and command substitution.
- [Arithmetic Operations](16.%20Bash%20Arithmetic%20Operations.md): Perform
	integer, base-conversion, and floating-point calculations.
- [Redirections](17.%20Bash%20Redirections.md): Manage standard input, output,
	errors, logging, and pipelines.

## Security Applications

These Bash fundamentals support common security workflows such as:

- Automating reconnaissance and enumeration tasks.
- Processing command output and checking files or permissions.
- Running repeatable checks across hosts and services.
- Capturing output and errors for troubleshooting and reporting.
- Building small, auditable scripts for authorized testing.

Use explicit targets, validate input, quote variables, and avoid executing
untrusted content directly from the network.

