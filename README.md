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
  body: [ Statement ]
}
```

The whole bash program tree

# FunctionDeclaration

```js
interface FunctionDeclaration : Node {
  type: "FunctionDeclaration"
  id: Identifier
  body: [ Statement ]
}
```

A function declaration

# Statement

```js
interface Statement : Node {}
```

# VariableAssignment

```js
interface VariableAssignment : Statement {
  type: "VariableAssignment"
  left: Identifier
  right: Expression
}
```

# VariableDeclaration

```js
interface VariableDeclaration : Statement {
  type: "VariableDeclaration"
  id: Identifier
  kind: "a" | "i" | "r" | "local"
  init: Expression | null
}
```

# Expression

```js
interface Expression : Node {}
```

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

# Command

```js
interface Command {
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
