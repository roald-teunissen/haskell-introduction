# Notes on chapter 2

## Usefull GHCi commands

|Command|Meaning|
|--- |--- |
|:load name|load script name|
|:reload|reload current script|
|:set editor name|set editor to name|
|:edit name| edit script name|
|:edit|edit current script|
|:type expr|show type of expr|
|:?|show all commands|
|:quit|quit GHCi|

## Valid function names

```myFun fun1 arg_2 x` ```

## Reserved keywords

```case class data default deriving do else foreign if import in infix infixl infixr instance let module newtype of then type where```

## Layout rule

With the use of indentation, Haskell is able to determine the grouping of definitions. The following scripts are valid examples.

### Example 1: standard

```
a = b + c
    where
        b = 1
        c = 2
d = a * 2
```

### Example 2: combined into a single line

```
a = b + c where {b = 1; c = 2}; d = a * 2
```

Although it is required to use curly parentheses and semi-colons in the single line example, it is still possible to add either curly parentheses or semi-colon or both to the first example.


## Convention: ``s`` suffix for list arguments

By convention, list arguments in Haskell usually have the suffix ``s`` on their name to indicate that they may contain mulitple values. For example, a list of numbers might be named ``ns``, a list of arbitrary values might be named ``xs``, and a list of lists of characters might be named ``css``.

## Comments

```-- This is an ordinary comment```

```
{-
This is a nested comment
-}
```
