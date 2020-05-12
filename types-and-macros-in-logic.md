# Implement a Compiler - Types and Macros in Logic

*Logic* language supports width-based types, so a high level language can
provide different types, e.g. widely used `int`, `bool`, `byte` and `string`,
less common `big_uint`. Meanwhile, high level languages do not need to consider
variables or return-types of functions during the compilation stage. For
example,

```
int f() { ... }
int i = f();
```

will be compiled into

```
callee int { ... }
define i int
define raw_value_unique_name {return-type-of-f}
caller raw_value_unique_name f ( )
move i raw_value_unique_name
```

In the forth statement, *logic* language automatically checks the consistency
of type between `raw_value_{unique_name}` and `i`. So to avoid recording types
in both high level languages and *logic* language, *logic* language supports a
set of macros.

*types* is a parameter for *logic* language compiler; it takes responsibility of
recording all types defined by `type` command. In short, it's a dictionary from
text strings as type names to their widths.

Besides this, *logic* language provides two special types, named `type0` and
`type*`. `type0` is a type without width; it's used to implement a type similar
to `void` in c language. `type*` is a variant width type; it's used to implement
string, array or `big_uint`. These two types have special copy rules, i.e.
`type0` cannot be copied to any other types; it cannot accept values from any
types as well. In contrast, `type*` can accept values from any types, but can
only be copied to `type*`. Meanwhile defining types with zero-width or infinite-
width are disallowed.

Source code of `types`:
[service/compiler/logic/types.vb](https://github.com/Hzj-jie/osi.net/blob/master/service/compiler/logic/types.vb)

*macros* implements macros in *logic* language; it's used to rewrite *logic*
source code during the compilation stage by querying *scope*, *anchors* and
*types*. For example, rewrite the `{return-type-of-f}` in the previous example
into `int`. Unlike the common macros used during precompilation stage, these
macros are used during compilation stage. I.e. before compiling
`int f() { ... }`, the compiler of *logic* language does not know the
`{return-type-of-f}` is `int`.

*macros* is also a simple way to implement overload in high level languages.
Depends on the definition of the high level language, including the type
information or width of parameters into the function name can be used to
differentiate functions. It will be discussed later.

Source code of `macros`:
[service/compiler/logic/macros.vb](https://github.com/Hzj-jie/osi.net/blob/master/service/compiler/logic/macros.vb)
