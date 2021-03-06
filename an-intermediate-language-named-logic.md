# Implement a Compiler - An Intermediate Language Named Logic

The instruction sequence of primitive cannot be considered as a language; also
the sequence needs not to be compiled to be executed. But compiling source codes
of a high-level language directly into the instruction sequences is usually
fairly complex. On the other side, functions of high-level languages are
similar, e.g. function, condition, loop and variable. These basic functions can
be used in implementations of various high-level languages. So an intermediate
language named logic is implemented to cover these basic functions.

This intermediate language covers following features.

1. Types based on the width of variables.

2. Named variables, no need to use absolute and relative addresses to access
variables any more.

3. Function definitions and executions.

4. Widely used flow controls, like conditions and loops.

5. No concept of lexical analysis, words and symbols are separated by spaces.

6. The syntax is extremely simple, almost no singularity. The source code of
logic language is close to a sequence of commands.

7. Can be compiled into primitive instruction sequence and executed on primitive
processor.

The definition of "logic" intermediate language is

```
# The following definitions are predefined.
#  typed_parameters = ( string string * )
#  parameters = ( string * )
#  paragraph = { statement * }

# define a type:
#  type uint 4
#  type biguint 0
# setting size to 0 to define a variable-length type.
type	string	uint

# execute append_slice command against two variables:
#  append_slice target source
append_slice	string	string

# execute cut command against two variables and store the result in the first variable:
#  cut target source length
cut	string	string	string

# execute cut_slice command against three variables and store the result in the first variable:
#  cut_slice target source from length
cut_slice	string	string	string	string

# execute clear command against the variable:
#  clear target
clear	string

# execute add command against two variables and store the result in the first variable:
#  add target source1 source2
add	string	string	string

# execute subtract command against two variables and store the result in the first variable:
#  subtract target source1 source2
subtract	string	string	string

# execute multiply command against two variables and store the result in the first variable:
#  multiply target source1 source2
multiply	string	string	string

# execute power command against two variables and store the result in the first variable:
#  power target source1 source2
power	string	string	string

# execute and command against two variables and store the result in the first variable:
#  and target source1 source2
and	string	string	string

# execute or command against two variables and store the result in the first variable:
#  power target source1 source2
or	string	string	string

# define a callee / function with name, return type, parameters and body:
#  callee f uint (a uint) { return f a; }
callee	string	string typed_parameters	paragraph

# execute a function with parameters and store its return value:
#  caller f return-value (p1, p2)
caller	string	string	parameters

# execute a function with parameters and discard the return value:
#  caller f (p1, p2)
caller	string	parameters

# execute less command against two variables and store the result in the first variable:
#  less target source1 source2
less	string	string	string

# execute more command against two variables and store the result in the first variable:
#  more target source1 source2
more	string	string	string

# execute equal command against two variables and store the result in the first variable:
#  equal target source1 source2
equal	string	string	string

# execute less_or_equal command against two variables and store the result in the first variable:
#  less_or_equal target source1 source2
less_or_equal	string	string	string

# execute more_or_equal command against two variables and store the result in the first variable:
#  more_or_equal target source1 source2
more_or_equal	string	string	string

# if the variable is true (access_stack_as_bool()), execute the first paragraph, otherwise execute the second one:
#  if a-bool-value { } else { }
if	string	paragraph	else	paragraph

# if the variable is true (access_stack_as_bool()), execute the paragraph:
#  if a-bool-value { }
if	string	paragraph

# execute copy command:
#  copy target source
copy	string	string

# execute copy_const command:
#  copy target <data_block>
copy_const	string	data_block

# define a variable with type:
#  define variable uint
define	string	string

# execute the paragraph until the variable is true:
#  do_until a-bool-value { }
do_until	string	paragraph

# execute the paragraph while the varaible is true:
#  do_while a-bool-value { }
# equals to do { } while (a_bool_value); in C.
do_while	string	paragraph

# execute an interrupt function with parameter and store the result in the third variable:
#  interrupt function-name parameter result
interrupt	string	string	string

# execute move command:
#  move target source
move	string	string

# execute devide against two variables and store the result (quotient and remainder) in the first two variables:
#  divide quotient remainder dividend divisor
divide	string	string	string	string

# execute extract against two varaibles and store the result (root and remainder) in the first two variables:
#  extract root remainder radicand index
extract	string	string	string	string

# exit a function without return value:
# to avoid increasing the complexity in this level, the language contains no "syntax" concept, but only fixed rules. so
# the "*" is used as a placeholder to make the parsing logic same as return string string.
#  return f *
return	string	*

# exit a function with return value:
#  return f result
return	string	string

# execute append command and store the result in the first variable:
#  append target source
append	string	string

# execute not command and store the result in the first variable:
#  not target source
not	string	string

# execute sizeof command and store the result in the first variable:
#  sizeof target source
sizeof	string	string

# execute empty command and store the result in the first variable:
#  empty target source
empty	string	string

# execute paragraph when the variable is true:
#  while_then a-bool-value { }
# equals to while(a_bool_value) { }; in C.
while_then	string	paragraph

# execute stop command:
#  stop
stop
```

These commands can be categorized into

1. Representations of primitive instructions, but instead of using addresses,
named variables are supported.

2. Flow controls.

The input of the compiler of logic language is text string, the output is the
instruction sequence of primitive processor.
