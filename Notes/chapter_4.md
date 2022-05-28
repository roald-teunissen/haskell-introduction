# Notes on chapter 4

In this chapter we introduce a range of mechanisms for defining functions in Haskell. We start with conditional expressions and guarded equations, then in- troduce the simple but powerful idea of pattern matching, and conclude with the concepts of lambda expressions and operator sections.

## 4.1 New from old
Combining one or more existing functions.

```haskell
even :: Integral a => a -> Bool
even n = n ‘mod‘ 2 == 0

recip :: Fractional a => a -> a
recip n = 1/n
```
The examples above showcase the use of types which have class constraints on `Integral` and `Factional`. In this case, numbers can only be used to live up to the constrains put upon the functions.

## 4.2 Conditional expressions
```haskell
abs :: Int -> Int
abs n = if n >= 0 then n else -n
```

Nesting:
```haskell
signum :: Int -> Int
signum n = if n < 0 then -1 else
                if n == 0 then 0 else 1
```
It is important to note that in Haskell, conditional statements always need a **else** branch. This requirements avoids the well-known *dangling else* problem.

## 4.3 Guarded equations
As an alternative to conditional expressions.


```haskell
abs n | n >= 0 = n
      | otherwise = -n
```
If the first guard is `True`, then the first result is chosen; otherwise, if the second is `True`, then the second result is chosen, and so on.

**The symbol `|` is read as *such that*.**

```haskell
signum n | n < 0 = -1
         | n == 0 = 0
         | otherwise = 1
```

The guard *otherwise* is defined in the standard prelude simply by `otherwise = True`. This keyword provides a convenient way of handling all other cases, as well as avoiding the possibility that none of the guards in the sequence is `True`.

## 4.4 Pattern matching
A sequence of syntactic expressions called *patterns* is used to choose between a sequence of results of the same type. 

```haskell
not :: Bool -> Bool
not False = True
not True = False
```

Functions with more than one argument.
```haskell
(&&) :: Bool -> Bool -> Bool
True && True = True
True && False = False
False && True = False
False && False = False
```

Which can be simplefied by using the *wildcard pattern* ***_***.
```haskell
True && True = True
_ && _ = False
```
Since only `True && True` can equate to `True`, the rest can equate to `False`.

Additionally, under lazy evaluation, if the first argument is `False`, the nte result `False` is returned without the need to evaluate the second argument.

This can be further simplefied like:
```haskell
True && b = b
False && _ = False
```
Whereas if the first value is `True`, the evaluation is the value of the second argument. And if the first argument is `False`, then it directly equates to `False`.

Even more simplefied:
```haskell
b && b = b
_ && _ = False
```
Which can be put into guards.
```haskell
b && c | b == c = b
       | otherwise = False
```

### Tuple patterns
The following examples select the first and second component of a pair.
```haskell
fst :: (a,b) -> a
fst (x,_) = x

snd :: (a,b) -> b
snd (_,y) = y
```

### List patterns
Match a list that begins with the `Char` a.
```haskell
test :: [Char] -> Bool
test [’a’,_,_] = True
test _ = False
```

A list can be decomposed as follows.
```haskell
      [1,2,3] 
=     { list notation }
      1 : [2,3]
=     { list notation }
      1 : (2 : [3])
=     { list notation }
      1 : (2 : (3 : []))
=     { list notation }
```
Essentially a list is abbreviated as `1:(2:(3:[]))`, which comes down to `1:2:3:[]`. Note the use of the const operator (`:`) in a constructed list.
By using the const operator, we can define a more general definition of a non-empty list.

```haskell
test :: [Char] -> Bool
test (’a’:_) = True
test _ = False
```
The difference is that this list accepts that has **any numbers of characters** beginning with the letter 'a'.

Similarly, the library `head` and `tail` can use the same technique.
```haskell
head :: [a] -> a
head (x:_) = x

tail :: [a] -> [a]
tail (_:xs) = xs
```

**Note that cons patterns must be parenthesised**, because function application has higher priority than all other operators in the language. For example, the definition `head x:_ = x` without parentheses means `(head x):_ = x`, which is both the incorrect meaning and an invalid definition.

# 4.5 Lambda expressions
As an alternative to defining functions using equations. Lambda expressions are nameless functions.

```haskell
\x => x + x
```
The symbol `\` represents a Greek letter *Lambda*, written as **λ**. Despite the fact that these functions are nameless, they can be used in the same way as any other functions.

```haskell
> (\x -> x + x) 2
4
```

As well as being interesting in their own right, lambda expressions have a number of practical applications.

Formalise the meaning of a curried function definition:
```haskell
add :: Int -> Int -> Int
add x y = x + y
```
can be understood as meaning
```haskell
add :: Int -> (Int -> Int)
add = \x -> (\y -> x + y)
```
which makes precise that `add` is a function that takes an integer `x` and returns a function, which in turn takes another `y` and returns the result `x + y`.

Rewritting original definitions in this manner comes down to the following syntactical form.
```haskell
? -> (? -> ?)
```

### Secondly, lambda expressions are also useful when defining functions that return functions as results by their very nature, rather than as a consequence of currying.
```haskell
const :: a -> b -> a
const x _ = x
```
can be transformed to
```haskell
const :: a -> (b -> a)
const x = \_ -> x
```
which showcases that the function `const` returns a function.

### Finally, lambda expressions can be used to avoid having to name a function.
```haskell
odds :: Int -> [Int]
odds n = map f [0..n-1]
         where f x = x*2 + 1
```
which can be transformed as follows
```haskell
odds :: Int -> [Int]
odds n = map (\x -> x*2 + 1) [0..n-1]
```
as the function `f` is only referenced once, we can define this as a lambda function.

## 4.6 Operator sections
Functions such as `+` that are written between their two arguments are called *operators*.

As shown before, we can simply reference to these operators in the following way.
```haskell
7 `div` 2
```
On the other hand, the converse is also possible. Any operator can be converted into a curried function that is written before its arguments by enclosing the name of the operator in parentheses, as in `(+) 1 2`. Moreover, this convention also allows one of the arguments to be included in the parentheses if desired, as in `(1+) 2` and `(+2) 1`.

In general, if `#` is an operator, then expressions of the form `(#)`, `(x #)`, and `(# y)` for arguments x and y are called sections, whose meaning as functions can be formalised using lambda expressions as follows:
```haskell
(#) = \x -> (\y -> x # y)

(x #) = \y -> x # y

(# y) = \x -> x # y
```

### Sections have three primary applications
`(+)` is the addition function `\x -> (\y -> x+y)`

`(1+)` is the successor function `\y -> 1+y`

`(1/)` is the reciprocation function `\y -> 1/y`

`(*2)` is the doubling function `\x -> x*2`

`(/2)` is the halving function `\x -> x/2`

### Stating the type of operator
It is necessary to state the type of operator as the operator itself is not a valid expression. For example, the type of the addition operator `+` for integers is stated as follows:
```haskell
(+) :: Int -> Int -> Int
```
### Operators as arguments to other functions
```haskell
sum :: [Int] -> Int
sum = foldl (+) 0
```