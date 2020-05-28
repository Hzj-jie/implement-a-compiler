# Implement a Compiler - Lexical Analysis and Syntax Analysis

`primitive` processor and `logic` language simplify the implementations of high
level languages. High level languages only need to translate source codes into
`logic` language, then `logic` language compiles them into the sequences of
`primitive` instructions. The sequences can be executed directly on `primitive`
processor.
So the implementation of a high level language cares only lexical analysis and
syntax analysis and how to convert the syntax tree into the source codes of
`logic` language.

Lexical analysis converts a string of characters into an array of `word`s with
type information. Its API is:

```
class typed_word {
 public:
  const string& ref;
  const size_t start;
  const size_t end;
  const size_t len;  // end - start.
  const uint type;  // A unique int to define a type.
}

bool lexer(const string& input, vector<typed_word>& result);
```

Syntax analysis converts the array of "typed" `word`s into a tree, knowing as
`AST`, Abstract Syntax Tree. Its API is:

```
class typed_node {
 public:
  const vector<typed_word>& ref;
  const size_t start;
  const size_t end;
  const uint type;  // A unique int to define a type.
  const typed_node* const parent;  // nullable.
  const vector<typed_node> subnodes;
}

bool syntaxer(const vector<typed_word>& words, typed_node& root);
```

Surely `lexer` or `syntaxer` may encounter errors; the errors can be identified
by return values.

Lexical analysis and syntax analysis are not bound to a certain high level
language or related to how the generated ASTs are processed. They are falling
into the category of automata. Some implementation details may impact the
following compilation, but their implementations can use rule files to generate
automatas to match the input.
