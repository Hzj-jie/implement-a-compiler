# Implement a Compiler - Function and Call in Logic

A very important feature supported by *logic* language is function and function
call. In short, function uses *anchors* to mark the location of the entry;
function call uses *anchors* to jump to the location.

A function can always be represented as the following instructions,

```
jmp {end-of-function}
## Entry of the function
## A set of instructions
rest
## end-of-function
```

The entry of a function is the second instruction of the function, the exit is
always a `rest` instruction. The first instruction jumps to the next instruction
after the `rest` instruction. When a function is invoked, the instructions are
executed from the entry, otherwise the function will be jumped over. With this
approach, the tree structure of function calls can be translated into
instruction set in punched tape format.

The function call can always be represented as,

```
push
## The first push instruction reserves the memory location of return value.
## To reduce the complexity of the implementation, functions with void / type0
## return types also have the memory reservation for return values.

push
cp rel0 {parameter1}
## Multiple push and cp pairs are used to initialize the parameters, note,
## by-value parameters are used instead of by-ref.
## Data of all parameters are copied into a temporary location with the cp
## instruction; after function calls, they won't be transfered to the original
## locations with the mov instruction.
## If mov instruction is used to transfer the temporary values to the original
## locations, the by-ref parameters can be achieved. But as an experimental
## language, this function has not been implemented.

stst  ## Store the current state.

jmp {begin-of-function}
## Jump to the entry of the function - the second instruction of the function.
move rel{number of parameters} {address-of-return-value}
## Move the return value of the function back to the predefined location.
pop
## Multiple pop instructions are used to delete the memory reservations of
## parameters and return value.
```

There is a very common question - who will initialize the parameters and return
value; who will destroy them; what the order of parameters and return value is.
Discussions can be found for this question. In *logic* language, a very common
and reliable solution is used, the caller initializes and destroys the memory
reservations of parameters and return value. The order of parameters and return
value is not quite critical; the order used here is that the return value is
placed at the begining; and parameters are placed from left to right.

The principle is usually simpler than the implementation, implementation itself
needs to consider whether the definition can be found in *scope*. Because
*logic* language always uses variable names but not memory locations.

As mentioned before, a common operation of *scope* is defining a variable and
marks its memory location in stack. But in the function calls, the caller and
the function itself do not share the same *scope* concept, these two pieces of
source codes are compiled separately.

Considering the following function definition,

```
callee copy-one-byte1 type* { source type* target type* pos uint len uint ) {
  ...
}
```

It needs to consider the memory locations of parameters and return value in the
current stack. This is not so complex, the *logic* compiler only needs to follow
the rules of pushing stack by callers. During the compilation stage, these
locations need to be defined in the *scope*, but no `push` instruction is
necessary to reserve the memory locations in the stack during the runtime. So
even the stack top has not been changed, the location in scope has been moved
downwardly. The memory reservations here will be shared by both the caller and
the function itself. The `define` function of *scope* is used here directly
instead of the `define` command; the later one is a pair of *scope*.`define` and
`push` instruction.

There is also another small problem, before finishing the compilation of a
function, the total length of the instruction set is not predictable. So the
compiler would reserve the first `jmp` instruction and fill it later. But this
is just some tiny implementation detail.

The source code of `callee`,
[service/compiler/logic/callee.vb](https://github.com/Hzj-jie/osi.net/blob/master/service/compiler/logic/callee.vb)


The invoker of the functions considers several more problems,

```
caller result ( param1 param2 param3 )
```

All the information in *anchors* are used when compiling this statement.

```
begin - The location of the entry of the function, used to generate the last jmp
        instruction.

return_type - The type of the return value of the function, used to determind
              the compatibility of the result variable accepting the return
              value of the function.

parameters - The types of parameters of the function, used to determind the
             compatibility of the parameters, both count of parameters and their
             types.
```

As mentioned before, *variable* provides the function to check the compatibility
of types based on the lengths. To reduce the complexity of the implementation,
two variables with specified names are used.

```
@parameter_{x}_of_{function_name}_place_hodler
@return_value_of_{function_name}_place_hodler
```

The former ones are the parameters, the later one is the return value. The types
of them are queried from *anchors*, but in the implementation, simply using
*variable*.`move_from` or *variable*.`copy_from` can achieve the type checks.

The source code of `caller`,
[service/compiler/logic/caller.vb](https://github.com/Hzj-jie/osi.net/blob/master/service/compiler/logic/caller.vb)


`Return` statement is an important concept of a function, this statement does
two things, placing the return value to the memory location in the stack
reserved by the caller; jumping to the original location of the caller. Though
not required, to maintain the consistency, in the implmentation of `callee`,
the return value is also named as
`@return_value_of_{function_name}_place_holder`. And the simplest way to finish
a function is using `rest` instruction.

This approach indeed breaks the common "single entry & single exit" principle of
assembly coding. But since *logic* is an experimental language, users do not
usually manually write *primitive* instructions or *logic* code, so this is not
quite critical.

The source code of `return`,
[service/compiler/logic/return.vb](https://github.com/Hzj-jie/osi.net/blob/master/service/compiler/logic/return.vb)


Also `stst` and `rest` instructions of *primitive* processor are designed to
reduce the complexity of the implementation of *logic* language, but they are
not must-have. A processor can rationally allocate memory space, for example,
split the memory space into two stacks, a set of instructions can be used to
push the processor states into one of the stacks. But similar as `or`
instruction which is the combination of `and` and `not` instructions, providing
several more instructions in *primitive* processor won't complicate the
implementation of the processor itself, but can heavily simplified the
implementation of the *logic* language. Similar `call` and `ret` instruction
pair is also included in x86 instruction set.
