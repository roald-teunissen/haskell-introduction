# Notes on chapter 5

In this chapter we introduce list comprehensions.

## 5.1 Basic concepts
In mathematics, the *comprehension* notation can be used to construct new sets from existing sets.

### In mathematics
The comprehension {$x^2$ | X ∈ {1..5}} produces the set {1,4,9,16,25} of all numbers $x^2$.

### In Haskell
A comprehension notation in Haskell is very similar.
```haskell
> [x^2 | x <- [1..5]]
[1,4,9,16,25]
```

| symbol        | description                |
| ------------- | -------------------------- |
| `\|`          | is read as *such that*     |
| `<-`          | is read as *is drawn from* |
| `x <- [1..5]` | is called a *generator*    |

### Combining lists
```haskell
> [(x,y) | x <- [1,2,3], y <- [4,5]]
[(1,4),(1,5),(2,4),(2,5),(3,4),(3,5)]
```

### Changing the order of two generators creates a different result
```haskell
> [(x,y) | y <- [4,5], x <- [1,2,3]]
[(1,4),(2,4),(3,4),(1,5),(2,5),(3,5)]
```

### All possible ordered pairings
```haskell
> [(x,y) | x <- [1..3], y <- [x..3]]
[(1,1),(1,2),(1,3),(2,2),(2,3),(3,3)]
```

### Practical example: library *concat*
