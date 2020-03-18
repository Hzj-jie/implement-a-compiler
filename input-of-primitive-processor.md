# Implement a Compiler - Input of Primitive Processor

An interrupt function table is required to initiate a primitive processor. The
interrupt function table can be implemented to fit different needs. For example,
in test cases, the output can be appended to a string.

Besides that, a set of instructions can be executed.

Instructions can be represented in two ways, readable text format or less
readable binary format. They are just two expressions of the instruction sets.

An example of text format:

```
push
push
push
push
push
push
push
push
## rel0 text
## rel1 len
## rel2 index
## rel3 int function id
## rel4 int return
## rel5 1 for cutl length
## rel6 equal return
## rel7 cutl return

cpc rel0 Ehello\x20world\n
sizeof rel1 rel0
cpc rel2 i0
cpc rel3 i0

## This is a simple way.
int rel3 rel0 rel4

## This is a complex way.
cpc rel5 i1
equal rel6 rel1 rel2
jumpif rel5 rel6
cutl rel7 rel0 rel2 rel5
int rel3 rel7 rel4
add rel2 rel2 rel5
jump rel-5

cpc rel2 i0
cpc rel3 i1

## This is a simple way.
int rel3 rel0 rel4

## This is a complex way.
cpc rel5 i1
equal rel6 rel1 rel2
jumpif rel5 rel6
cutl rel7 rel0 rel2 rel5
int rel3 rel7 rel4
add rel2 rel2 rel5
jump rel-5
stop
```

This instruction set uses two ways to print the text "hello world" to stdout and
stderr by int 0 and int 1.

There are some basic concepts, for example, types of constants can be identified
by the prefixes.

1. Integer, i123, means 123 in integer. 4 bytes.

2. Long, l123, means 123 in long, i.e. int64. 8 bytes.

3. Double, d123.2, means 123.2 in double. 8 bytes.

4. Boolean, b1, means true. 1 byte.

5. Byte array, aABCDEF0123456789. Two characters represent one byte.

6. C-escaped string, Eabcde\t\20. One character represents 1-3 bytes. i.e.
utf-8. Note, lots of characters are considered as separators in the string
format of instructions, they should be escaped.

7. string, sabc. One character represents 1-3 bytes. i.e. utf-8. No escape
support.

Another example is the "rel" and "abs" to define the ways of memory references.

Binary format is less readable, I won't attach an exmaple here. The basic idea
is to use a uint to represent an instruction and append operands afterward.
Operands are memory references or constants.
