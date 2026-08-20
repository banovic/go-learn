## Aug 19, 2026

### Code organisation units
- Code hierarchy: module -> package -> code elements { function, variable, constant, structure, interface }
- module: always exists, has go.mod file (it might not exist when running single file - not important at this moment)
- package: can be many defined in module, defined by:  file and folder hierarchy, and 'package' statement - enforced by compiler
- code elements: visibility (exported or unexported): if it starts with capital letter - exported, otherwise unexported - enforced by compiler
- exported (public): visible when used from other modules, or other packages even in the same module
- unexported (private): visible only in the same package
- go.mod file: defines other (imported) module dependencies (among other things)
- dependencies (other modules): code is stored globally (unlike node_modules in node, for example)

### Numeric constants
- numeric constant can by typed or untyped; if untyped - it exists out of typed context
- untyped: that means it can precisely hold arbitrarly large (or small) numeric values which can cause problems when used in typed context - constrained by compiler (at least 256 bits)
  for example: uint64 can hold 64 bit integer at max, its value is <= 2^64, constant can hold values that are > 2^64 (2^100, ...).
  arithmetic over constants still keeps them in constants space - they are not moved into typed space when doing arithmetic
- typed constants are constrained by type

### Looping construct
- only one: for
- braces ({}) always required for block
- parts before block: init statement (before first iteration), condition expression (checked before every iteration) and post statement (after every iteration)
- has different forms: full-form (init + condition + post), while (only condition expression), infinite (no parts before block), range form (used with range)

### If statement
- always have braces ({}) around block
- can have statement before condition, for example: if x := foo(); x < 10 { ... x visible only here, or in else and elseif branches ... }

## Aug 20, 2026

### Switch statement
- no need for break statement, executes one branch only
- cases are evaluated (expressions), stops on first match (lazy)

### Defer statement
- arguments evaluated at defer time, but can be changed with lambda
- multiple defers are run FIRST IN LAST OUT - stack

### Pointers
- pointer holds memory address of a value
- no pointer arithmetic
- for any type T, *T is type 'pointer to type T'
- *<type T> type definition: pointer to T (for example *int)
- *<pointer> value at the memory address pointer points to - dereferencing, indirecting
- &<variable> creates pointer to the variable, variable must be _addressable_ (memory location of variable) - referencing, address of

### Struct
- collection of fields
- fields access by .
- &<struct literal> creates pointer to the struct value (memory location of value) - referencing, address of; composite literals are addressable, so &Point{1, 2} is legal (unlike &42)

### Arrays
- [n]T is type 'array of n elements of type T'
- dimension is part of the type

### Slices
- []T is type 'slice of type T'
- view into underlying array; does not store data (it consists of: pointer to the underlying array, length and capacity)
- changing the elements of a slice modifies the corresponding elements of its underlying array.


