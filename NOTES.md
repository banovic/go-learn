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
- untyped: that means it can precisely hold arbitrarly large (or small) numeric values which can cause problems when used in typed context (spec guarantees at least 256 bits)
  for example: uint64 can hold 64 bit integer at max, its value is <= 2^64 - 1, constant can hold values that are > 2^64 (2^100, ...).
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
- array value contains all its elements, so copying copies all that data

### Slices
- []T is type 'slice of type T'
- view into underlying array; does not store data (it consists of: pointer to the underlying array, length and capacity)
- changing the elements of a slice modifies the corresponding elements of its underlying array.
- zero value is nil
- defined with: pointer to underlying array, length, capacity
- length: number of elements it contains (len(s))
- capacity: number of elements in underlying array counting from the start of slice (cap(s))
- append(): variadic function
- append(): creates a new slice, adds element to the end of given slice and returns a new slice; 
- append(): if there is room in underlying array it will write to underlying array, otherwise it will allocate new underlying array and copy existing array into it
- make(): creates a new slice, creates underlying array as well
- slice value contains 3 elements (pointer to array, length, capacity), so copying copies those 3 elements

### Range
- range iterates over a slice (among other types)
- over slice: 2 values returned for each iteration: index and copy of the element at that index

## Aug 30, 2026

### Maps
- type: map[KeyType]ValueType
- zero value: nil
- create: make(map[KeyType]ValueType)
- key type: must be comparable using == and !=
- cannot be key: slice, map, function
- insert or update: m[key] = value
- retrieve: value = m[key]
- remove: delete(m, key)
- check exists: elem, ok  = m[key] , if key is in m, ok is true, otherwise it's false and elem is 0-value for map's value type
- map value contains a pointer to runtime hash map, so copying copies that pointer

### Functions
- functions are values
- functions can be used as arguments and return values
- functions may be closures. Closure is function value with environment, that can be referenced from within function (free variables). Captured variables (which form environment) are shared by reference.

### Methods
- go can define methods on types (it does not have classes)
- method: function with a special receiver argument
- methods can only be defined with a receiver whose type is defined in the same package as the method, and receiver must not iteself be a pointer type or an interface type
- methods can be defined for pointer receivers (*T as well as T) and in these case they modify receiver; with value receiver they work on copy of receiver (as all other arguments do)
- for convience, if v is type T, go interprets the statement v.Method() as (&v).Method() when Method is defined on pointer receiver (\*T); v must be addressable
- for convience, if p is type \*T, go interprets the statement p.Method() as (\*p) .Method() when Method is defined on value receiver (T)
- value vs pointer receiver: pointer receiver can modify and avoids copying
- for type all methods should be uniform - either all on value receiver or all on pointer receiver

### Interfaces
- interface is a type defined by a set of method signatures
- a value of interface type can hold any value that implements those methods
- a type implements an interface by implementing its methods
- interface type that specifies 0 methods: interface{}
- any: alias for interface{}, these 2 (any and interface{} are interchangeable)
- concrete interface value: (type, value) pair: dynamic type + dynamic value
- nil interface = (nil, nil); calling a method on it panics
- interface holding a nil pointer is NOT nil: (*T, nil) != nil

### Type assertions
- type assertion: provides access to an interface's value underlying concrete (interface) value
- t := i.(T) // i must be interface type, this statement asserts that value i of type interface, has underlying value (a pair component) of type T, and assigns it to a value t
-            // if T is concrete type, i's dynamic type must be exactly T
-            // if T is interface type, i's dynamic type must implement T; result t is of interface type T
-            // if i does not hold value of type T, this statement will panic - runtime error
- t, ok := i.(T) // this tests whether i (of type interface) has an underlying value of type T, ok is bool, and t is either value of type T (if ok == true) or zero for wanted type
-                // no panic

## Aug 31, 2026

### Type switches
- type switch: construct that allows several type assertions in series
- type switch: like a regular switch statements, but cases are types and those cases are compared by interface value underlying type
- example
```go
switch v := i.(type) {
case T:
    // here v has type T
case S:
    // here v has type S
default:
    // no match; here v has the same type as i
}
```

### Errors
- go programs express error state with `error` values
- `error` type is built in interface:
```
type error interface {
    Error() string
}
```
- a nil error denotes success, a non-nil error denotes failure

## Sep 1, 2026

### Type parameters
- go functions can work on types defined by type parameters
- example: `func Index[T comparable](s []T, x T) int`

### Generic types
- type can be parametrized by type parameter

### Goroutines
- goroutine is lightweigh thread managed by go runtime
- `go f(x, y, z)` evaluates f, x, y, z in current gorouting, but execution of `f(x, y, z)` happens in new goroutine
- goroutines share same address space, so access to shared data must by synchronised

### Channels
- channel is conduit through which data can be sent and received
- channel operator `<-`, data flows in direction of arrow: `ch <- v` sends v to channel, `v := <- ch` receive v from channel
- channels must be created before use: `make(chan int)`
- by default sends and receives block until other side is ready
- channels can be buffered: `make(chan int, 100)` (second arg is buffer size)
- sends to a buffered channel block only when buffer is full
- receives from a buffere channel block only when buffer is empty
- sender can close channel to indicate that no more values will be sent (only sender should close channel, never receiver, sending on closed channel is panic)
- receivers can test whether channel has been closed by assigning second parameter: `v, ok := <-ch` (ok is false when channel is closed)
- the loop `for v := range ch` will receive values from channel repeatedly until channel is closed
- select statement lets gorouting wait on multiple communication operations (ie. each case is either send or receive)
- select blocks until one of its cases can run, then executes that case
- select chooses one case randomly if multiple are ready
- default case in select statement runs if no other case is ready
- default case - send or receive without blocking
- select with no cases blocks forever
