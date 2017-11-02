- [Node objects](#node-objects)
- [Identifier](#identifier)
- [Literal](#literal)
- [Program](#program)
- [Functions](#function)
- [Declarations](#declarations)
  - [VariableDeclaration](#variabledeclaration)

# Node Objects

```js
interface Node {
  type: string
  loc: SourceLocation | null
}
```

```js
interface SourceLocation {
  source: string | null
  start: Position
  end: Position
}
```

```js
interface Position {
  line: number   // 1-indexed
  column: number // 0-indexed
}
```

# Identifier

```js
interface Identifier : Node {
  type: "Identifier"
  name: string
}
```

# Program

```js
interface Program : Node {
  type: "Program"
  body: BlockStatement
}
```

The whole bash program tree

# BlockStatement

```js
interface BlockStatement : Node {
  type: "BlockStatement"
  body: [ Statement ]
}
```

# Statement

```js
interface Statement : Node {}
```

A statement is a thing that can stand alone.

## SubShell

```js
interface SubShell : Statement {
  type: "SubShell"
  body: BlockStatement
}
```

## AritheticStatement

## Control Flow

## Choice

### IfStatement

```js
interface IfStatement : Statement {
  type: "IfStatement"
  test: [ Comparison | Command ]
  consequent: BlockStatement
  alternate: Statement | null
  kind: "elif" | "else if" | null
}
```

### CaseStatement

## FunctionDeclaration

```js
interface FunctionDeclaration : Statement {
  type: "FunctionDeclaration"
  id: Identifier
  body: [ Statement ]
}
```

A function declaration

## VariableAssignment

```js
interface VariableAssignment : Statement {
  type: "VariableAssignment"
  // a=1 | a[1]=1
  left: Identifier | ArrayMemberConstruct
  right: Expression
}
```

## VariableDeclaration (?)

```js
interface VariableDeclaration : Statement {
  type: "VariableDeclaration"
  id: Identifier
  // declare -a a=1
  kind: "a" | "i" | "r" | "local"
  init: Expression | null
}
```

Actually `declare -a a=foo` is a command, but it is better to be treated as a `Statement` due to its complication.

## Command

```js
interface Command : Statement {
  type: "Command"
  name: string
  argv: Arguments
}
```

# Arguments

```js
interface Arguments {
  type: "Arguments"
  args: [ Expression ]
}
```

The length of the arguments depends on the result of substitution

# Construct

```js
interface Construct : Node {}
```

## ArrayMemberConstruct

```js
interface MemberConstruct : Construct {
  type: "ArrayMemberConstruct"
  array: Identifier
  property: Literal
}
```

## TestConstruct

```js
interface TestConstruct {
  type: "TestConstruct"
  condition:
  // [] | [[]]
  kind: "test" | "extended"
}
```

## Expansion

### BraceExpansion

```js
interface BraceExpansion : Expansion {
  type: "BraceExpansion"
  expansions: [ Literal ]
  prefix: Literal | Expansion
  suffix: Literal
}
```

### ExtendedBraceExpansion

```js
// {a..z}
interface ExtendedBraceExpansion : Expansion {
  type: "ExtendedBraceExpansion"
  start: Literal
  end: Literal
  prefix: Literal | Expansion
  suffix: Literal
}
```

# Expression

```js
interface Expression : Node {}
```

An expression is what can be assigned to identifiers or as the item of arguments.

## Literal

```js
interface Literal : Expression {
  type: "Literal"
  // 'a'
  value: string
  // '"a"'
  raw: string
}
```

## ArrayExpression

```js
interface ArrayExpression : Expression {
  type: "ArrayExpression"
  elements: Arguments
}
```

## Substitution

### CommandSubstitution

```js
interface CommandSubstitution : Expression {
  type: "CommandSubstitution"
  command: Command
  // `command -r` | $(command -r)
  kind: "backtick" | "bracket"
}
```

### StringSubstitution

```js
// "$a"
interface StringSubstitution : Expression {
  type: "StringSubstitution"
  contents: [ Literal | VariableSubstitution ]
}
```

### VariableSubstitution

```js
// ${a:-default}
interface VariableSubstitution : Expression {
  type: "VariableSubstitution"
  kind: "brace" | "no-brace"
  // null for positional variables
  id: Identifier | null
  pattern: VariableDirective | null
}
```

### ArithmeticSubstitution

```js
// $(( 2 + 3))
interface ArithmeticSubstitution : Expression {
  type: "ArithmeticSubstitution"
  contents:
}
```

### ProcessSubstitution

```js
interface ProcessSubstitution : Expression {
  type: "ProcessSubstitution"
  command: Command
  // >() | <()
  kind: "in" | "out"
}
```

# VariableDirective

## MemberAccessDirective

```js
interface MemberAccessDirective : VariableDirective {
  type: "MemberAccessDirective"
  property: Literal
}
```

## GetterDirective

```js
// value[kind]arg
interface GetterDirective : VariableDirective {
  type: "GetterDirective"
  kind: "-" | ":-" | "=" | ":=" | "+" | ":+" | "?" | ":?"
  arg: Literal | Expression
}
```

## LengthDirective

```js
// #value[property]
interface LengthDirective : VariableDirective {
  type: "LengthDirective"
  property: "*" | "@" | null
}
```

## CropDirective

```js
// value[kind]pattern
interface CropDirective : VariableDirective {
  type: "CropDirective"
  pattern: Literal | Expression
  kind: "#" | "##" | "%" | "%%"
}
```

## SliceDirective

```js
// value:start[:end]
interface SliceDirective : VariableDirective {
  type: "SliceDirective"
  start: Literal
  end: Literal | null
}
```

## ReplaceDirective

```js
// value/[kind]pattern/replacement
interface ReplaceDirective : VariableDirective {
  type: "ReplaceDirective"
  pattern: Literal
  replacement: Literal
  kind: "/" | "#" | "%" | null
}
```

## SearchDirective

```js
// !value[kind]
interface SearchDirective : VariableDirective {
  type: "SearchDirective"
  // the same
  kind: "*" | "@"
}
```
