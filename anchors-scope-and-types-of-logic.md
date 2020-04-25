# Implement a Compiler - Anchors, Scope and Types of logic

Comparing to common programming languages, the lexicon and syntax of *logic*
language have significant differences. But it implements basic functions using
in common programming languages, say, variables and functions. The
implementation of variables relies on *scope*; the implementation of functions
relies on *anchors*.

*Logic* language generates a sequence of *primitive* instructions, the location
of each instruction is fixed in the final sequence, so the entry of a function
is fixed as well. A function call is simply setting parameters and using `jmp`
instruction to jump to the entry of the function. So during the compilation, a
dictionary is used to record the mapping between the function name and the
location of the entry of the function; it's used to search the location for
`jmp` instruction when involing the function. The dictionary is called
*anchors*.

*Anchors* records the function definition, including name, location, types of
parameters and return value; it's used to generate *primitive* instructions. For
example,

```
push  # prepare for the return-value
push
cp ref0 {parameter1}
push
cp ref0 {parameter2}
push
cp ref0 {parameter3}
jmp {entry of callee}
```

Meanwhile, the information is also used to check the count and types of
parameters and the type of the return value.

There is a problem, link or not link? I.e. is there a way to know the locations
of all functions during the compilation stage? If the precondition of function
A calling function B is the function B is defined before function A, then there
is no need of the linkage stage, the *primitive* instructions can be generated
directly during the compilation stage. Otherwise, according to the orders of the
pieces of the instructions, the locations of jumps may vary, thus an extra
linkage stage is required to modify these locations for the final binary.

As a simple experimental language, linkage stage is not important, so it's not
supported yet.

The source code of anchors is at
[service/compiler/logic/anchros.vb](https://github.com/Hzj-jie/osi.net/blob/master/service/compiler/logic/anchors.vb).


*Scope* is used to record names, types and locations of variables. Similar as
common processors, *primitive* also uses absolute or relative positions to refer
variables. When generating a certain instruction, the memory location it needs
to access is also fixed.

Unlike *anchors*, *scope*, as its name, is a specific stack. When searching for
a variable, it always starts from current *scope*, then parent *scope*, until
the root *scope*. Using the following source code as an example,

```
define output type*

type uint 4
type bool 1

define 0 uint
copy_const 0 i0
define 1 uint
copy_const 1 i1
define 2 uint
copy_const 2 i2
define 3 uint
copy_const 3 i3

callee method type0 ( n type* ) {
  append_slice output n  // line 1
}
callee main type0 ( ) {
  define 100 uint
  copy_const 100 i100
  define i type*
  copy_const i i1
  define continue bool
  less_or_equal continue i 100
  while_then continue {
    caller method ( i )  // line 2
    add i i 1  // line 3
    less_or_equal continue i 100  // line 4
  }
}

caller main ( )
```

The `n` in line 1 is defined within `method` function, `output` is defined in
the global, or root *scope*.

The `i` in line 2 is defined within `main` function.

The `1` in line 3 is defined in the global *scope*.

The `continue` in line 4 is defined within `main` function.

The implementation of *scope* is not complex, the very basic operations are,

- Searching the location and type of a variable according to its name;
- Defining a variable requires only type and how many variables have been
defined.

The implementation is at
[service/compiler/logic/scope.vb](https://github.com/Hzj-jie/osi.net/blob/master/service/compiler/logic/scope.vb).

When defining a function parameter list, a function call or another very common
scenario, entering a new paragraph, a new child *scope* is created.

```
type int 4
callee method int ( p1 int p2 int ) {  // line 1
  define v int  // line 2
  return v
}

define 0 int
copy_const 0 i0
define 1 int
copy_const 1 i1
caller method ( 0 1 )  // line 3
```

line 1: When compiling `callee method`, its parameter list does not belong to
the root *scope*, so a new *scope* is needed. Surely the same *scope* can be
used during the compiling of the paragraph, but to reduce the complexity, one
more scope won't cause any problem.

line 2: When compiling a paragraph, a new *scope* is always needed.

line 3: When compiling a function call, the parameter list does not belong to
the current *scope*, so a new *scope* is needed as well. Surely new variables
defined in the new child *scope* are not a must-have, the variables in the
current *scope* can be used as well. But the benefits of using a new *scope*
are:

- Always use pass-by-value rather than pass-by-reference.
- The same way can be used to check whether the parameter types are consistent
  with the function defination.
  It's simply because, all the values need to be copied into the new variables.

There are two use cases of *scope*,

1. Using `define` operation. After defining a new variable in the *scope*, a
   `push` instruction is used to reserve a block in the memory stack for further
   use. Meanwhile, when the *scope* finished, same count of `pop` instructions
   will be used to restore the original memory stack top.

1. Using the stack spaces defined in a function-call in a function
   implementation. In this scenario, no `push` instruction is necessary, the
   variable name is not necessary to be consistent between function-call and
   function implementation, they belong to two different scopes. The function
   implementation needs only to find the correct locations in the memory stack.

The first use case is used in function-calls and new paragraphs, so a
`scope_wrapper` is implemented to share the logic.
[service/compiler/logic/scope\_wrapper.vb](https://github.com/Hzj-jie/osi.net/blob/master/service/compiler/logic/scope_wrapper.vb).
