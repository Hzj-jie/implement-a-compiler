# Implement a Compiler - Implement Primitive Instructions

An instruction implements two function-independent interfaces.

1. Input and Output

Same as the interface of primitive processor, an instruction can input and
output both text formatted and binary formatted instruction set.

```
Public Interface exportable
    Function export(ByRef b() As Byte) As Boolean
    Function import(ByVal i() As Byte, ByRef p As UInt32) As Boolean

    Function export(ByRef s As String) As Boolean
    Function import(ByVal s As vector(Of String), ByRef p As UInt32) As Boolean

    Function bytes_size() As UInt32
End Interface
```

2. Execution

Operates simulating level of primitive processor and implements the
functionality of the instruction itself.

```
Public Interface instruction
    Inherits exportable

    Sub execute(ByVal imi As imitation)
End Interface
```

Instead of hand-writting code, the implementation of the input and output
interface can be generated directly from the definition of the instructions. A
[commands-parser.cs](https://github.com/Hzj-jie/osi.net/blob/master/service/interpreter/primitive/codegen/commands-parser.cs)
uses the instruction definition file
[commands.txt](https://github.com/Hzj-jie/osi.net/blob/master/service/interpreter/primitive/commands.txt)
to create following source codes.

- commands.enum

Enumeration of the instructions in binary format, to ensure the uniqueness of
the identity of each instruction.

- instructions

The impementation of input and output of each instruction.

- instructions-exportable-test

The tests of the input and output interface of each instruction.

Besides these files, the operations of instructions are manually implemented in
[instructions.execute.vb](https://github.com/Hzj-jie/osi.net/blob/master/service/interpreter/primitive/instructions.execute.vb).

The primitive processor is implemented after the steps above. This processor now
can support executing operations:
[service/tests/interpreter/primitive/cases](https://github.com/Hzj-jie/osi.net/tree/master/service/tests/interpreter/primitive/cases).
