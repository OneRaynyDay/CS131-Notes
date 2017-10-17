# Functional Programming

It's a good tool for parsing!

## The problem of parsing (a context free grammar, BNF)

Given the grammar of a language, and a string of tokens, how do you parse the tokens with respect to the grammar of the language?

1. **We must deal with recursion.** We must have nested-ness in our language(it could be arbitrarily long sequence of tokens!)
2. **Concatenation.** We need to concatenate symbols together.
3. **Disjunction(OR).** Here's an example: `S -> aSb | aT`. Which one of the disjunctions should we use?

Our job here is to take a grammar, token sequence and get a parser. I.e.: `f : (grammar, token sequence) -> parser`.

### Conjunction & Disjunction Issues

`A -> BC` seems easy right?

```python
def A(tokens):
    return C(B(tokens))
```

But what if `B(tokens)` consumes different # of tokens?
That's an issue, because `C` might or might not work depending on which branch `B` decided to take.

1. Return sets of leftover tokens rather than a single set. Then `C` can choose from any, and there will be a valid one.

### Design

We need a `acceptor = token_list -> bool` which tells you whether it accepts or not, and a `matcher = acceptor -> (token_list -> bool)`. This is like generating an acceptor from a previously existing acceptor.

When writing the parser for `A`, we can do something like:

```ocaml
pA = fun acceptor -> pB( pC acceptor )
```

For an example,

```
B -> aX | bY | aZ
```

grammar gives us:

```ocaml
let pB acceptor ts = pX( pa( ts )) || pY( pb( ts )) || pZ( pa( ts ))
```

or:

```ocaml
let pB acceptor ts = match ts with
    | T 'a' :: t -> pX (t) || pZ (t)
    | T 'b' :: t -> pY (t)
```

_Small aside: redundancy_:

```ocaml
fun acceptor -> (fun ts -> acceptor ts) 
```

is redundant!

```ocaml
fun acceptor -> acceptor
```

is equivalent!

## Closures

**Currying is a technique for closures.** Actually, in our `matcher` function, we perform currying:

```
acceptor = tokenlist -> bool
matcher = acceptor -> (tokenlist -> bool)

match acc tokens = (match acc) tokens

match acc <-- gives currying
```

# Functional Language - Motivations

## Clarity

We want to use mathematical notations (100s of years of experience). For example, in C: `c = c + 1` is false. 

In a nutshell, we want to escape from the "von Neumann bottleneck". We started off with a single ALU, and we have to load/store values to multiply. If we are multiplying many numbers at once, then we are facing a huge bottleneck!

### The `;` Syntax is Bottleneck!

If we have `p++ = 3; q++ = --p;`, we are **forcing serialization on these two operations!**

### Functional Form (Higher order function)

It's a function that takes a function as an argument or returns a function as a result.

For example, $\sum_i^N f(i)$ is a function that takes a function `f`, and a number `N` and returns a number.

Another example is $\int f(x)dx$. This is a function that takes a function and returns another function. 

These functional forms are used all the time!

### Referential Transparency

```ocaml
let f = (fun x ...
    ....
    ....
    ..x.
    x...)
```

The 3 `x`'s will have the same value! This makes programs **easier to understand!** If we're writing C++ code, we don't guarrantee this because x could've been **edited in the middle!**

### Case Study: OCaml

Why **O**Caml? It's **O**bject oriented of ML.

1. **Compile time type checking**. It's like g++/gcc. It's unlike python.
2. **You don't need to write down the types**. It's like python, unlike C++/java.
3. **Automatic storage management (a GC)**. We don't have new/delete. It's like python/java, unlike C++.
4. **Good support for higher order functions**.









