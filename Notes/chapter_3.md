# Notes on chapter 3

## 3. Basic concepts

|Symbol| Can be read as | Example | Meaning |
|---|---|---|---|
|`::`| is of type | `False :: Bool`| `False` is of type `Bool` |
|`->`| maps to result | `Bool -> Bool` | `Bool` maps to result `Bool`

## 3.1.1 Type notation: values

We use the notation `v::T` to mean that `v` is a value of the type `T`, and say that `v` has *type* `T`.

```haskell
False :: Bool
```

```haskell
True :: Bool
```

```haskell
not :: Bool -> Bool
```

The logical negation function `not` depicts a negation of the mapping from boolean values to boolean values as a result.

***Practical note**: use `:type` to display the type of an expression*

``` haskell
> :type False
False :: Bool
```

## 3.1.2 Type notation: expressions

Symbol `::` can also be used with expressions that have not yet been evaluated. The notation in this case is described as ``e :: T``, whereas `e` represents an expression.

```haskell
not False :: Bool
```

```haskell
not True :: Bool
```

```haskell
not (not False) :: Bool
```

### Type requirement and assumption

In Haskell, every epression **must** have a type, **which is calculated prior to evaluating the expression by a process called *type inference***.

The key to this process is the following simple typing rule for function application, which states that if `f` is a  function that maps arguments of type `A` to results of type `B`, and `e` is an expression of type `A`, then the application `f` `e` has type `B`:

|``f :: A -> B e ::a``|
|--|
|`f e :: B`|

## 3.2 Basic types

|Type | | Description|
|---|---|---|
|Bool| logical values | **True** or **False** |
|Char| single characters | All characters in the Unicode system|
|String| strings of characters | All sequence of characters |
|Int| fixed-precision integers | Integers with a fixed amount of memory being allocated (range: -2^63 to 2^63 -1) |
|Integer| arbitrary-precision integers | All integers with as much memory as necessary |
|Float| single-precision floating-point numbers | Numbers with a decimal point, with a fixed amount of memory allocated.|
|Double| double-precision floating-point numbers | Similar to Float except that twice as much memory is used for storage of these numbers to increase their precision.|

The term *floating-point* comes from the fact that the number of digits permitted after the decimal point depends upon the size of the number.

## 3.3 List types

A list is a sequence of *elements* of the same type.

```haskell
[v,v,v] :: [T]
```

### Examples

```haskell
[False, True, False] :: [Bool]
```

```haskell
['a', 'b', 'c'] :: [Char]
```

```haskell
["One", "Two", "Three"] :: [String]
```

The number of elements is called its *length*.
| List | Length | Name |
|---|---|---|
|`[]`| 0 | Empty list |
|`[False]`, `['a']`, `[[]]`| 1 | Singleton |

We even can have nested lists:

```[['a', 'b'],['c', 'd', 'e']] :: [[Char]]```

Furthermore, there is no restriction that a list must have a finite length. In particular, due to the use of *lazy evaluation in Haskell*, lists with an infinite length are both natural and practical.

*Lazy evaluation in Haskell*: meaning that the expression is not evaluated when they are bound to variables, but their evaluation is **deferred** until their results are needed by other computations. In consequence, arguments are not evaluated before they are passed to a function, but only when their values are actually used.

## 3.4 Typle types

A *tuple* is a finite sequence of *components* of possibly different types.

```haskell
(T1, T1, ...,Tn)
```

### Examples

```haskell
(False,True) :: (Bool,Bool)
```

```haskell
(False,'a',True) :: (Bool,Char,Bool)
```

```haskell
("Yes",True,'a') :: (String,Bool,Char)
```

The number of components in a tuple is called its *arity*.
|Tuple|Arity||
|---|---|---|
|()|zero|empty tuple|
|(True, 'a')|two| pairs|
|(True, 'a', "B")|three|triples|

Note that a tuple with a arity of one, such as `(False)` is not described. This is simply because iti s not permitted because they would conflict with the use of parentheses to make the evaluation order explicit, such as in `(1+2)*3`.

There are no restirctions on the type of the components in a tuple. For example, we can have tuples of tuples, tuples of lists, and lists of tuples.

```haskell
('a', (False, 'b')) :: (Char,(Bool,Char))
```

```haskell
(['a','b'],[False,True]) :: ([Char],[Bool])
```

```haskell
[('a',False),('b',True)] :: [(Char,Bool)]
```

Note that tuples must have a finite arity, in order to ensure that tuple types can always be inferred prior to evaluation.

## 3.5 Function types

A *function* is a mapping from arguments of one type to results of another type. As written as follows:

```T1 -> T2```

### Examples

```haskell
not :: Bool -> Bool
```

```haskell
even :: Int -> Bool
```

The following code snippet depicts a description of the *add* function. In this example, two *Int* values are mapped to result in a *Int* as output.

``` haskell
add :: (Int, Int) -> Int
add (x,y) = x+y
```

The following function returns a list of integers from zero to a given limit.
``` haskell
zeroto :: Int -> [Int]
zeroto n = [0..n]
```

## 3.6 Curried functions

Functions with multiple arguments can also be handled in another, perhaps less obvious way, by exploiting the fact that functions are free to return functions as results. For example, consider the following definition:

``` haskell
add` :: Int -> (Int -> Int)
add` x y = x+y
```

The type states that ``` add` ``` is a function that takes an argument of type `Int`, and returns a result that is a function of type `Int -> Int`.

More precisely, ``` add` ``` takes an integer `x` and returns a function (`y = x+y`), which in turn takes an integer `y` and returns the result `x+y`.

Note that the function ``` add` ``` produces the same final result as the function `add`, but whereas 'add' takes its two arguments at the same time packaged as a pair (`(Int,Int)`), ``` add` ``` takes two arguments one at a time, as reflected in different types of the two functions.

```haskell
add: (Int, Int) -> Int
```

```haskell
add` :: Int - (Int -> Int)
```

Functions with more than two arguments can also be handled using the same technique, by returning functions that return functions, and so on.

```haskell
mult :: Int -> (Int -> (Int -> Int))
mult x y z = x*y*z
```

This definition states that `mult` takes an integer `x` and returns a function, which in turn takes an integer `y` and returns another function, which finally takes an integer `z` and returns the result `x*y*z`.

Functions such as ``` add` ``` and `mult` that take their arguments one at a time are called *curried functions*.

Useful functions can be made by applying curried functions with less than its full complements of arguments. For example, a function that increments an integer can be given by the partial aplication ``` add` 1 :: Int -> Int ``` of the curried function ``` add` ``` with only one of its two arguments.

### Avoid parentheses
To avoid excess parentheses when workng with curried functions, two simple conventions are adopted. First of all, the function `->` in types is assumed to associate to the right. For example:

```haskell
Int -> Int -> Int -> Int 
```

means

```haskell
Int -> (Int -> (Int -> Int)) 
```


Consequently, function application, which is denoted silently using spaces, is assumed to associate to the left. 


```haskell
mult x y z 
```

means


```haskell
((mult x) y) z 
```

Unless tupling is explicitly reuired, all functions in Haskell with multiple arguments are normally defined as curried functions, and two conventions above are used to reduce the number of parentheses that are required.

## 3.7 Polymorphic types