# Implement a Compiler - Variable in Logic

*Variable* simply involves functions of *scope* in *logic* language and is used
as a reference of a variable. When generating *primitive* instructions,
*variable* can provide a convenient way to locate a variable in the memory
stack. So its basic function is,

```
Public Function ref() As String
    Dim r As String = Nothing
    assert(scope.export(name, r))
    Return r
End Function
```

Since another variable can be defined after refering a variable, so the location
of the previous variable may be changed. Involing `ref` function to locate the
variable is a safe approach. In each scope, the count of variables won't be a
large number, and querying *scope* impacts only compilation performance, it's
not a quite bad approach.

The *variable* has slightly more functionalities, such as determining whether a
variable can be copied or moved to another variable, or whether can be
represented by a certain type, say, integer or boolean. *Primitive* processor
always uses big\_uint for math calculations, some restrictions have been loosen
to avoid using big\_uint everywhere. Unless *primitive* processor supports
integer overflow as common processors, the width of variables is not meanful
during math calculations. Simply using `cut` instruction to reduce the width of
variables after math calculations is sufficient.

[service/compiler/logic/variable.vb](https://github.com/Hzj-jie/osi.net/blob/master/service/compiler/logic/variable.vb).
