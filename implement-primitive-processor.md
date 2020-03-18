# Implement a Compiler - Implement Primitive Processor

Primitive processor contains three function-irrelevant interfaces.

1. Input and Output

Can read and write text formatted or binary formatted instruction sets.

```
Public Interface exportable
    Function export(ByRef b() As Byte) As Boolean
    Function import(ByVal i() As Byte, ByRef p As UInt32) As Boolean

    Function export(ByRef s As String) As Boolean
    Function import(ByVal s As vector(Of String), ByRef p As UInt32) As Boolean

    Function bytes_size() As UInt32
End Interface
```

2. Executor

Consumers facing functions, execution, registers and errors after execution.

```
Public Interface executor
    Inherits exportable

    Enum error_type
        instruction_pointer_overflow
        stack_access_out_of_boundary
        undefined_interrupt
        unsupported_feature
        invalid_buffer_size
    End Enum

    Structure state
        Public Shared ReadOnly empty As state
        Public ReadOnly instruction_pointer As UInt64
        Public ReadOnly stack_size As UInt64

        Public Sub New(ByVal instruction_pointer As UInt64, ByVal stack_size As UInt64)
            Me.instruction_pointer = instruction_pointer
            Me.stack_size = stack_size
        End Sub
    End Structure

    Function access_stack(ByVal p As data_ref) As pointer(Of Byte())
    Function stack_size() As UInt64
    Function instruction_pointer() As UInt64
    Function carry_over() As Boolean
    Function divided_by_zero() As Boolean
    Function imaginary_number() As Boolean
    Function access_states(ByVal p As UInt64) As state
    Function states_size() As UInt64

    Function halt() As Boolean
    Function errors() As vector(Of error_type)

    Sub execute()
End Interface
```

3. Simulating Level

Instructions facing functions, setting registers, invoking interrupts, finishing
executions.

```
Public Interface imitation
    Inherits executor
    Function interrupts() As interrupts
    Sub push_stack()
    Sub pop_stack()
    Sub store_state()
    Sub restore_state()
    Overloads Sub instruction_pointer(ByVal v As Int64)  ' data_ref.offset() returns int64
    Overloads Sub carry_over(ByVal v As Boolean)
    Overloads Sub divided_by_zero(ByVal v As Boolean)
    Overloads Sub imaginary_number(ByVal v As Boolean)
    Sub advance_instruction_pointer(ByVal v As Int64)
    Sub do_not_advance_instruction_pointer()
    Sub [stop]()
End Interface
```

An implementation implements these three interfaces above, the detailed
implementations are fairly simple and straightforward. Input and output are
slightly complex and will be discussed altogether with the implementation of
instructions.

Detailed implementation:
[simulator.vb](https://github.com/Hzj-jie/osi.net/blob/master/service/interpreter/primitive/simulator.vb)
