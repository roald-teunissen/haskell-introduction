# Notes on chapter 3

## 3. Basic concepts

| Symbol | Can be read as | Example         | Meaning                      |
| ------ | -------------- | --------------- | ---------------------------- |
| `::`   | is of type     | `False :: Bool` | `False` is of type `Bool`    |
| `->`   | maps to result | `Bool -> Bool`  | `Bool` maps to result `Bool` |

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

| ``f :: A -> B e ::a`` |
| --------------------- |
| `f e :: B`            |

## 3.2 Basic types

| Type    |                                         | Description                                                                                                         |
| ------- | --------------------------------------- | ------------------------------------------------------------------------------------------------------------------- |
| Bool    | logical values                          | **True** or **False**                                                                                               |
| Char    | single characters                       | All characters in the Unicode system                                                                                |
| String  | strings of characters                   | All sequence of characters                                                                                          |
| Int     | fixed-precision integers                | Integers with a fixed amount of memory being allocated (range: -2^63 to 2^63 -1)                                    |
| Integer | arbitrary-precision integers            | All integers with as much memory as necessary                                                                       |
| Float   | single-precision floating-point numbers | Numbers with a decimal point, with a fixed amount of memory allocated.                                              |
| Double  | double-precision floating-point numbers | Similar to Float except that twice as much memory is used for storage of these numbers to increase their precision. |

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
| List                       | Length | Name       |
| -------------------------- | ------ | ---------- |
| `[]`                       | 0      | Empty list |
| `[False]`, `['a']`, `[[]]` | 1      | Singleton  |

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
| Tuple            | Arity |             |
| ---------------- | ----- | ----------- |
| ()               | zero  | empty tuple |
| (True, 'a')      | two   | pairs       |
| (True, 'a', "B") | three | triples     |

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

**A type that contains one or more type variables is called *polymorphic* ("of many forms").**

The idea that `length` can be applied to a lists whose elements have any type is amde precise in its type by the inclusion of a *type variable*.
Some examples include:
```haskell
> length [1,3,5,7]
4
> length ["Yes", "No"]
4
> length [sin,cos,tan]
```
`Length` is hereby a function that calculates the length of any list, irrespective of the type of the elements.

*Type variables* must be in lower-case letters and are usually named a,b,c and so on.
```haskell
length :: [a] -> Int
```
Other examples:
```haskell
fst :: (a,b) -> a

head :: [a] -> a

take :: Int -> [a] -> [a]

{-
from this example, we can conclude that zip 
pairs up elements from two lists, although
the type on its own doesn't capture the precise 
manner in which this is done.
-}
zip :: [a] -> [b] -> [(a,b)]

id :: a -> a
```

## 3.8 Overloaded types
An example of this is the fact that operator `+` is calculates the sum of any two numbers of the same numeric type

```haskell
> 1 + 2
3

> 1.0 + 2.0
3
```
*Class constraints* play a part in this. Written as `C a`, where `C` is the name of the class and `a` is a type variable. For example:
```haskell
(+) :: Num a => a -> a -> a
```
That is, for any type a that is an *instance* of the class *Num* of numeric types, the function (+) has type a -> a -> a. 

The following is the overloaded type and `(+)` the overloaded function.
```haskell
Num a => a -> a -> a
```

Other examples:
```haskell
(*) :: Num a => a -> a -> a

negate :: Num a => a -> a

abs :: Num a => a -> a
```

## 3.9 Basic classes
A *class* is a collection of types that support certain overloaded operations called *methods*.

### *Eq* class 
has the following methods to determine if two variables are equal or unequal 

```haskell
(==) :: a -> a -> Bool
(/=) :: a -> a -> Bool
```
All basics types ***Bool, Char, String, Int, Integer, Float,*** and ***Double***  are instances of the *Eq* class.

Some examples:
```haskell
> False == False
True

> ’a’ == ’b’
False

> "abc" == "abc"
True

> [1,2] == [1,2,3]
False

> (’a’,False) == (’a’,False)
True
```
*Note that function types are not in general instances of the Eq class, because it is not feasible in general to compare two functions for equality.*

### *Ord* class
This class contains types that are instances of the equality class *Eq*, but whose values are totally (linearly) ordered.
```haskell
(<) :: a -> a -> Bool

(<=) :: a -> a -> Bool

(>) :: a -> a -> Bool

(>=) :: a -> a -> Bool

min :: a -> a -> a

max :: a -> a -> a
```

Again, all basic types are instances of the *Ord* class, as are list types and tuple types provided that their element and component types are instances.

```haskell
> False < True
True

> min ’a’ ’b’
’a’

> "elegant" < "elephant"
True

> [1,2,3] < [1,2]
False

> (’a’,2) < (’b’,1)
True

> (’a’,2) < (’a’,1)
False
```
Note that strings, lists and tuples are ordered *lexicographically*; that is, in the
same way as words in a dictionary.

### *Show* class
Containing types whose values can be converted into strings of characters.
```haskell
show :: a -> String
```

Some examples
```haskell
> show False
"False"

> show ’a’
"’a’"

> show 123
"123"

> show [1,2,3]
"[1,2,3]"

> show (’a’,False)
"(’a’,False)"
```
### *Read* class
This class is dual to *Show*, but uses the following method:
```haskell
read :: String -> a
```

Some examples
```haskell
> read "False" :: Bool
False

> read "’a’" :: Char
’a’

> read "123" :: Int
123

> read "[1,2,3]" :: [Int]
[1,2,3]

> read "(’a’,False)" :: (Char,Bool)
(’a’,False)
```

The use of `::` in these examples resolves the type of the result.

With:
```haskell
> read "False" :: Bool
```
a boolean value is inferred. Converting "False" to a logical value is therefore valid, but:
```haskell
> read "abc" :: Bool
```
is obviously not, because *abc* is not a valid `Bool` value.

### *Num* class
This class contains types whose values are numeric, and as such can be processed using the following methods:
```haskell
(+) :: a -> a -> a

(-) :: a -> a -> a

(*) :: a -> a -> a

-- return the negation of a number
negate :: a -> a

-- return the absolute value
abs :: a -> a

-- return the sign
signum :: a -> a
```

The basic types ***Int, Integer, Float***, and ***Double*** are instances of the Num class.
```haskell
> 1 + 2
3

> 1.0 + 2.0
3.0

> negate 3.0
-3.0

{- 
parentheses are important here. 
without them in this case, it would 
lead to the following:
abs - 3 

which is incorrect and an ill-typed expression 
-}
> abs (-3)
3

> signum (-3)
-1
```
***Note that the Num class does not provide a division method, but as we shall now see, division is handled separately using two special classes, one for integral numbers and one for fractional numbers.***

### *Integral* class
This class contains types that are instances of the numeric class *Num*, but in addition whose values are integers, and as such support the methods of integer division and integer remainder:
```haskell
div :: a -> a -> a

mod :: a -> a -> a
```

In practice, these two methods are often written between their two arguments by enclosing their names in single back quotes. The basic types ***Int*** and ***Integer*** are instances of the Integral class.
```haskell
> 7 `div` 2
3

> 7 `mod` 2
1
```

### Fractional class
This class contains types that are instances of the numeric class Num, but in addition whose values are non-integral, and as such support the methods of fractional division and fractional reciprocation:
```haskell
(/) :: a -> a -> a

recip :: a -> a
```

The basic types ***Float*** and ***Double*** are instances.

```haskell
> 7.0 / 2.0
3.5

> recip 2.0
0.5
```

## 3.10 Chapter remarks
*"The term Bool for the type of logical values celebrates the pioneering work of George Boole on symbolic logic, while the term curried for functions that take their arguments one at a time celebrates the work of Haskell Curry (after whom the language Haskell itself is named) on such functions. The relationship between
the type of a polymorphic function and its behaviour is formalised in [3]. A more detailed account of the type system is given in the Haskell Report [4], and a formal description of the type system can be found in [5]."*