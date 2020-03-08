# Implement a Compiler - A Processor Named Primitive

An intermediate should be easy to be executed; also comparing to existing
languages, it needs some specialties. So I designed a "processor" with the
concept of the processor containing instructions, registers and memory.

An instruction is an operator and several operands. For example, a copy
operator, copy data of A to location of B. There are two types of operands,
references and constants. A reference indicates a chunk of data in memory stack;
a constant is a way to store a constant into the language.

A register indicates an aspect of the state of the processor, e.g. the
instruction currently executing.

The memory is splitted into three parts,

1. instructions, readonly, are an array of instructions.
2. Data stack, the memory space accessible to the instructions. Each spot within
the stack is a variable-sized array.
3. State stack, store the current snapshot of registers for context switching.

A data reference (data\_ref) represents a location in the data stack of the
memory; there are two ways to refer the location, relative anchors and absolute
anchors. A relative anchor marks the distance between the referred location and
the top of the stack, for example, rel1 indicates the slot of data right below
the top of the stack. An absolute anchor marks the distance between the referred
location and the bottom of the stack, for example, abs2 indicates the third slot
of data in the stack.

A constant (data\_block) is an array of bytes, it can be used to represent, for
instance, a text string.

Registers can be impacted by the execution of the instructions, but instructions
cannot directly modify these registers.

1. IP, instruction pointer, uint64, the position of the executing instruction in
the array of the instructions. Unless Do-Not-Advance-Instruction-Pointer (DNAIP)
instruction is set to true, execution of each instruction will increase the
value of the register by one.

2. CO, carry over, boolean, whether the execution of the last instruction causes
a carry over. Instructions in the category of calculation set the value of this
register.

3. DBZ, divide by zero error, boolean, whether the execution of the last
instruction causes a divide by zero error. Instructions related to devide set
the value of this register.

4. IN, imaginary number error, boolean, whether the execution of the last
instruction causes an imaginary number error. Instructions related to extraction
set the value of this register.

5. DNAIP, do not advance instruction pointer, boolean, writeonly. If the value
of this register is set to true, the instruction pointer won't be
self-increased, but the value in this register will be restored to false.

Set of instructions

```
push	#push a data slot in to stack
pop	#pop a data slot from stack
jump	data_ref	#jump to instruction @A
cpc	data_ref	data_block	#(*A) = B
mov	data_ref	data_ref	#(*A) = (*B), clr B
cp	data_ref	data_ref	#(*A) = (*B)
add	data_ref	data_ref	data_ref	#(*A big_uint) = (*B big_uint) + (*C big_uint)
sub	data_ref	data_ref	data_ref	#(*A big_uint) = (*B big_uint) - (*C big_uint)
mul	data_ref	data_ref	data_ref	#(*A big_uint) = (*B big_uint) * (*C big_uint)
div	data_ref	data_ref	data_ref	data_ref	#(*A big_uint) = (*C big_uint) / (*D big_uint) (*B big_uint) = (*C big_uint) % (*D big_uint)
ext	data_ref	data_ref	data_ref	data_ref	#(*A big_uint) = extract((*C big_uint), (*D big_uint)) (*B big_uint) = remainder(extract((*C big_uint), (*D big_uint)))
pow	data_ref	data_ref	data_ref	#(*A big_uint) = pow((*B big_uint), (*C big_uint))
jumpif	data_ref	data_ref	#jump to instruction @A, if (*B bool) is true
cpco	data_ref	#(*A) = CO
cpdbz	data_ref	#(*A) = DBZ
cpin	data_ref	#(*A) = IN
stop	#finish execution, and leave the executor in a stop but not error state.
equal	data_ref	data_ref	data_ref	#(*A) = ((*B big_uint) == (*C big_uint))
less	data_ref	data_ref	data_ref	#(*A) = ((*B big_uint) < (*C big_uint))
app	data_ref	data_ref	#(*A) += (*B)
sapp	data_ref	data_ref	#(*A) += sizeof(*B) + (*B)
cut	data_ref	data_ref	data_ref	#(*A) = sub-array(src=(*B), from=(*C uint))
cutl	data_ref	data_ref	data_ref	data_ref	#(*A) = sub-array(src=(*B), from=(*C uint), len=(*D uint))
int	data_ref	data_ref	data_ref	#execute an interrupt function (*A uint), with parameter (*B), return value will be set to (*C)
clr	data_ref	#set (*A) to empty array
scut	data_ref	data_ref	data_ref	#(*A) = the (*C uint) chunk of (*B)
sizeof	data_ref	data_ref	#(*A) = array-size(*B)
empty	data_ref	data_ref	#(*A) = ((*B) == null)
and	data_ref	data_ref	data_ref	#(*A big_uint) = (*B big_uint) AND (*C big_uint)
or	data_ref	data_ref	data_ref	#(*A big_uint) = (*B big_uint) OR (*C big_uint)
not	data_ref	data_ref	#(*A big_uint) = NOT (*B big_uint)
stst	#push current state into states, such as IP_AFTER_THIS_INSTRUCTION, i.e. IP + 1, Stack Size, a typical usage is [ stst, jump ? ]
rest	#pop extra (StackSize - states.top.StackSize) slots from stack, and jump to instruction @(* states.top.IP uint64)
```

Besides common instructions, "int" instruction (interrupt) is a simple way to
invoke functions unimplementable by other instructions, say, reading from
standard input stream or writing to standard output stream.

An interrupt is invoked by its predefined unsigned int, so the definition of the
indexes of the interrupts cannot be changed arbitrarily. It's also part of the
concept of the processor.

```
0 stdout, write input to stdout and return null
1 stderr, write input to stderr and return null
2 stdin, read from stdin and return the byte-array
3 current_ms, return the current milliseconds as int64 byte-array
```

Unlike other commonly used processors, in the memory stack of the primitive
processor, the size of each slot is variable, so big integer is supported by any
math calculation instructions.


------

Back to the [Table of Contents](README.md)
