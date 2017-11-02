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

# Comparison

```
```

## BinaryComparison

# Statement

```js
interface Statement : Node {}
```

A statement is a thing that can stand alone.

## Control Flow

### IfStatements

```js
interface IfStatement : Statement {
  type: "IfStatement"
  test: [ Comparison | Command ]
  consequent: BlockStatement
}
```

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
// a=1
interface VariableAssignment : Statement {
  type: "VariableAssignment"
  left: Identifier
  right: Expression
}
```

## VariableDeclaration

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

# Expression

```js
interface Expression : Node {}
```

An expression is what can be used as "value".

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
  elements: []
}
```

## Substitution

### CommandSubstitution

```js
interface CommandSubstitution : Expression {
  type: "CommandSubstitution"
  command: Command
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
// $a
interface VariableSubstitution : Expression {
  type: "VariableSubstitution"

}
```
