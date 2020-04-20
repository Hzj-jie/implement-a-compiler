# Implement a Compiler - Generate Codes from Logic Command Definitions

The process of compiling codes of *logic* language into instruction sequences of
*primitive* processor depends on the behavior of each single statement. But
generating and analyzing the codes of *logic* language themselves only depend on
the defintions of *logic* commands.

The codes of analyzing the codes of *logic* language can create intermediate
types from the statements, for example, a type definition statement
`type int 4` will be translated into an instance of `define` class. Two
parameters are provided, `"int"` and `4`. After the analyzer reading `"type"`
text string, it expects the following two text strings are the parameters of
`"type"` command, the first one is a string text, the second one is an integer.
An analysis error will be raised if the above requirements cannot be met.

The code to generate the code of analyzer is at
[service/compiler/logic/codegen/importer_gen.cs](https://github.com/Hzj-jie/osi.net/blob/master/service/compiler/logic/codegen/importer_gen.cs).

The generated code is at
[service/compiler/logic/importer.definition.vb](https://github.com/Hzj-jie/osi.net/blob/master/service/compiler/logic/importer.definition.vb).

Meanwhile, writting the code of *logic* language is pretty error-prone, a set of
builders are also generated from the command definitions to avoid missing
parameters or mismatching parameter types. I.e. instead of writting strings like
`type int 4`, `builders.of_define("int", 4)` can be used to avoid potential
errors. The code can also be generated from the command definitions.

The code to generate the code of builder is at
[service/compiler/logic/codegen/builder_gen.cs](https://github.com/Hzj-jie/osi.net/blob/master/service/compiler/logic/codegen/builder_gen.cs).

The generated builder code is at
[service/compiler/logic/builder/builders.implementation.vb](https://github.com/Hzj-jie/osi.net/blob/master/service/compiler/logic/builder/builders.implementation.vb).

So the logic of input and output of *logic* language requires no boilerplate
or "manual" code.
