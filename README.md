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

# Test

## TestConstruct

```js
interface TestConstruct {
  type: "TestConstruct"
  condition:
  kind: "test" | "extended"
}
```

## BinaryComparison

```js
interface BinaryComparison : Comparison {
  type: "BinaryComparison"
  operator: string
  left: Identifier
  right: Expression
}
```

# Statement

```js
interface Statement : Node {}
```

A statement is a thing that can stand alone.

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
  left: Identifier | MemberConstruct
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

# Construct

```js
interface Construct : Node {}
```

## MemberConstruct

```js
interface MemberConstruct : Construct {

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
// $a
interface VariableSubstitution : Expression {
  type: "VariableSubstitution"

}
```
