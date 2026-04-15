# Crafting Interpreters by Robert Nystrom

# Notes 

## 2. Map of territory 

Scanning - Takes in linear stream of characters and chunks then together into a series of words, In programming languages these are called tokens. 

Parsing - This is where our syntax gets a grammar. It takes the flat sequence of tokens and buils a tree structure that mirrors the nested nature of the grammar. 

Static Analysis - First bit of analysis that most languages do is called binding or resolution. For each identifier , we find out where that name is defined and wire them together. This is where scope comes into play. 

We need to store all this analysis somewhere , there are few places that we can store this . 
- Often it gets stored right back as attributes on the syntax tree itself. 
- We may store data in a lookup stable off to the side. They keys to this table are identifiers - names of vars and declarations . 
- Transform the tree into a entirely new data structure that more directly expresses the semantics of the code. Thats explained below. 

Intermediate representations - This lets you support multiple source languages and target platforms with less effort . Write one front end and each for each source language that produces the IR and then one backend for each target architecture. 

Optimization - Once we understand what the user program means , we are free to swap it out with a different program that has the same semantics but implements them more efficiently . 

Example of this is constant folding , if some expression always evaluates to the exact same value we can do the evaluation at compile time and replace the code for the expression with its result. 

Code generation - generating primite assembly like instructions a cpu runs

Virtual machine - If compiler produces byte code , work isnt done as there is no chip that runs the byte code, its our job to translate. This can be thought of as an intermediate representation. or we can write a virtual machine, a program that emulates hypothetical chip supporting your virtual architecture at run time. 

Run time - If we have compiled it to machine code , we directly tell os to load the executable and off it goes or if we compiled it to bytecode , we need to start up VM and load the program into it.

Shortcuts and Alternate Routes : 

Single pass compilers - Some simple compilers interleave parsing analysis and code generation so that they produce output code directly in the parser without ever allocating any syntax trees or other IRs. Pascal and C were designed around this limitation. At that time memory was so precious taht compiler might not even be able to hold  an entire source file in memory. This is why pascals grammar required type declarations to appear first in a block. 

Tree walk interpreters - Some programming languages begin executing code right after parsing it to an AST. To run the program the interpreter traverses that syntax tree one branch and leaf at a time and evaluating each node as it goes. 

Transpilers - Writing backend for a language can be lot of work. If you have some existing IR to target , you could bolt your front end into that. We write front end and in the backend it converts to lower level primitive target language and using existing compilation tools that is compiled. This is called source to source compiler or transcompiler. 

Just-in-time compilation -  on the user end machine when the program is loaded you compile it to the native code for the architecture their computer supports , This is called just in time compilation. 

Compilers and Interpreters : 
- Compiling is an implementation technique that involves translating a source language to someother usually lower level form. 
- compiler translates the source to some other form but doesnt execute it. 
- Interpreter takes in source code and exectues immediately , It runs program from source. 
- gcc and clang take your c code and compile to machine code these are compilers not interpreters . 
- cpython is an interpreter and it has a compiler.

## 3. The Lox Language

Lox is the programming language designed and implemented in the book. It is a high-level, dynamically typed language with automatic memory management (garbage collection), and a familiar C-like syntax.

**Data Types:**
- **Booleans:** `true` and `false`.
- **Numbers:** Simple double-precision floating-point numbers (`1234`, `12.34`).
- **Strings:** Enclosed in double quotes (`"I am a string"`).
- **Nil:** Represents "no value" (`nil`).

**Expressions:**
- **Arithmetic:** `+`, `-`, `*`, `/`. The `+` operator also concatenates strings.
- **Comparison and Equality:** `<`, `<=`, `>`, `>=`, `==`, `!=`. Values of different types are never equivalent.
- **Logical Operators:** Prefix `!`, infix `and`, and `or`. Like in C, `and` and `or` short-circuit.
- **Grouping:** Uses parentheses `()` to explicitly control precedence.

**Statements:**
- **Expression Statements:** Expressions followed by a semicolon `;` promote an expression to a statement.
- **Print Statement:** A specific `print "Hello";` statement is built-in as a hack to easily produce output without setting up a standard library.
- **Blocks:** Curly braces `{ ... }` group multiple statements together and introduce their own scope.

**Variables:**
- Declared using the `var` keyword.
- If not initialized explicitly, a variable defaults to `nil`.
- Example: `var breakfast = "bagels";`

**Control Flow:**
- **If Statements:** `if (condition) { ... } else { ... }`
- **While Loops:** `while (condition) { ... }`
- **For Loops:** A basic C-style `for` loop (`for (var a = 1; a < 10; a = a + 1) { ... }`).

**Functions:**
- Defined using the `fun` keyword. 
- Functions are **first-class**, meaning they can be passed as arguments, returned, and stored in variables.
- By default, a function implicitly returns `nil` if there is no explicit `return` statement.
- **Terminology:** An *argument* is the actual value passed to a function call, while a *parameter* is the variable that holds the value inside the function.
- **Closures:** Functions "close over" and hold onto references of surrounding variables they use, keeping them an active part of memory even after the outer function has returned.

**Classes and Object Orientation:**
- Lox is class-based (rather than prototype-based like JavaScript).
- **Classes:** Declared using the `class` keyword.
- **Instances:** Created by calling the class itself like a function (e.g., `var myInstance = Breakfast();`).
- **Fields:** Properties can be dynamically added to or accessed on objects (e.g., `myInstance.meat = "sausage";`).
- **`this`:** A keyword used within methods to refer to the current object.
- **Initialization:** An `init()` method functions as the class constructor if present.
- **Inheritance:** Single inheritance is supported using the `<` operator (`class Brunch < Breakfast`). Superclass methods can be accessed via `super`.

**Standard Library:**
- Intentionally minimal (almost non-existent). It includes only a `clock()` function to track time for benchmarking and the `print` statement.