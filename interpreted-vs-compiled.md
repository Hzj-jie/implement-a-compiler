# Implement a Compiler - Interpreted v.s. Compiled

A programming language needs to be executed. There are two way to execute.
Interpreted language, compiler and executor generate no intermediate output. The
other is compiled language, compiler compiles and generates intermediate output;
what executor consumes is the output of the compiler.

Surely, there is no significant boundary between these two types of execution
mechanism. Say, by hiding the involving of clang, c-language can be considered
as an "interpreted language" as well. An example is the online c++ ide. Or in
the opposite way, some interpreted languages support to export the compiler
results into some intermediate outputs for better execution performance, e.g.,
PyCodeObject / pyc.

Interpreted languages hide the compiling stage in the executor itself. So each
execution requires compilation at first, they fit relatively simple, less
performance critical scenarios. Compiled languages do not need to repeat the
compilation, so the execution performance are significantly better, they fit
more performance critical scenarios. But the code cannot be loaded as "data"
during runtime, since the compiling stage is not omittable. Say, some servers
have no clang preinstalled at all.

Since the boundary is not significant, and the compiling stage cannot be
ignored, we can first assume there is a "compiler".


The "object" generated from the code is the output of the compiler. There are
two types of output as well, one is directly executable on the processor. It has
several different names, for instance, "native", "binary". Languages like asm,
c, c++, go, generate "native" output. The other type of output can be executed
on some virtual machines, the output of the compilation is usually called
"intermediate" language, say, bytecode, cil.

There are lots of different processor instruction sets, x86, amd64, arm, arm64,
misp, and the fashion OpenRISC. Each instruction set also has several different
versions. Compiler needs to adjust the output according to the instruction set.
This is not an easy thing for regular users. Not everyone prefers the step of
downloading the source code and compiling locally. Neither users, nor developers
like to do so. So SUN proposed "java", "jvm" and "bytecode" concepts at the
earliest. bytecode is an abstraction of the processor instruction set, jvm
executes the bytecode, java compiler compiles java source code into bytecode. So
jvm can be adjusted and optimized according to the hardware without needing to
recompile the java source code. Meanwhile, bytecode is fairly hard to be
decompiled into java code, it makes developers happy.

So we assume the output of the compiler is a type of intermediate language, in
the implementation, it's called "primitive".


------

Back to the [Table of Contents](README.md)
