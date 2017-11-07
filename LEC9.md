# Styles of programming

- **Sequential Programming**: C, Java, etc. We compose instructions.
    - `s1; s2;`.
- **Functional Programming**: OCaml, Lisp, etc. We compose functions.
    - `f1(f2(x))`
    - We omit assignments(or anything that has side effects).
- **Logic Programming**: Prolog, etc. We create predicates. 
    - `p1 & p2`
    - `p1 | p2`
    - We omit functions.

Logic programming, and functional programming are both **subsets of sequential programming**. 
They take away unnecessary things/problematic things so the code is easier to understand/debug.

Now we focus on **logic programming.**

# Logic Programming

What is it exactly? It's **declarative**. We write our program using **logical assertions**.

An algorithm can be composed of 2 things : **logic + control**. The logic is correctness, and control is optimization.

**Logic programming separates these 2 concerns. We want to modify control without changing the correctness!**

Logic programming always handles logic better than control.

# Prolog Grammar(Important stuff)

This specific type's name is **Edinburgh**.

```
term:
    NUM (3, 4, 12, 59, etc)
    ATOM (aB, xyz, er, '0.3', 'AB', 'o''clock', etc. '' is escaping ')
    VARIABLE (X, Ya, Z1, _1 <-- but you usually don't want _{somethings}, _, etc)
    STRUCTURE ( f(X1,X2,X3,...,XN), it's like a data structure, NOT a function)
```

Everything else is syntactic sugar!

Examples:
- `[]` is actually `'[]'`
- `[X|L]` is actually `'.'(X,L)`
- `[X,Y|L]` is actually `'.'(X,'.'(Y,L))`.
- `p(X) :- q(Y), r(Z)` is actually `':-'(p(X), ','(q(Y), r(Z)))`. 
- `N + 1` is actually equivalent to ` '+'(N,1) `.

This data structure is actually:

```
[ + , N , 1]
```

With something like `N + -3 * 4`:

```
[ + , N , ]
         |
       [ * ,  , 4 ]
            |
          [ - , 3]
```

To evaluate it, we can use a built in function `is`:

```
?- is(X, 3+5*4).
X = 23

?- N = 10, P is N+1.
N = 10, P = 11

?- N = 10, N is N+1.
no
```

`N is N+1.` is ALWAYS false. We can't just increment variables like this! 
It will evaluate the expression like `is(10, 10+1)`.

However, beware, because `is` is written in machine code.

You can't run it backwards:

```
?- is(1369, N*N).
ERROR
```

As in, the RHS cannot contain variables!

## Sorting in Prolog

```prolog
sort(L,S) :- permute(L,S), sorted(S).
```

- The above is **not a function call.** This is a **relation** on the list and its sorted version.
- `:-` is an if statement.
- `permute(L,S)` means we need to have the same elements in `L` and `S`.
- `sorted(S)` requires `S` to be sorted.

But now we have 2 other relations!

```prolog
sorted([]).
sorted([_]).
sorted([X,Y|L]) :- X =< Y, sorted([Y|L]).
```

- The empty list is always sorted.
- `_` is a dont-care. We don't care, as long as the list has 1 element.
    - `_` is an anonymous wildcard that doesn't have binding on other `_`'s.
- `=<` is a strange less-than-or-equals operator. This is to appeal to the linguistics people.
    - `>=` is still the same syntax.
- `[X,Y|L]` is a pattern match. In OCaml it's like `x::y::l`.

```prolog
permute([], []).
permute([X|L], PXS) :- permute(L, PS), append(P, S, PS), append(P, [X|S], PXS)
```

- The logic behind the permute function is that our structure looks something like:
    - `[x, ... L ...]`
    - `[ ... P ... , x , ... S ... ]`
        - Take `x` out to get: `[x, ... P ... S ... ]`.
- `permute(L, PS)` means the rest of the list must be true.
- `append(P, S, PS)` means `P` appending `S` must equal `PS`.
- `append(P, [X|S], PXS)` means `P` appending `X|S` must equal `PXS`.

... But now we need `append`!

```prolog
append([], A, A).
append([X|L], M, [X|LM]) :- append(L, M, LM)
```

## What's the efficiency?

Terrible! It's `O(|L|!)`. We're generating all permutations...
It's correct, but it's extremely slow.

## How do we optimize this?

We can use a rule `member` instead:

```prolog
member(X, [X|_]).
member(X, [_|L]) :- member (X,L).
```

Upon running it:

```prolog
?- member(Q, [3,1,3,7]

Q = 3; 
Q = 1;
Q = 3;
Q = 7;
no
```

It looks like: `Q1 = Q, X1 = Q1, X1 = 3`, thus `Q1 = 3`.

We can then type a `;` to tell it to give another answer! It will look past that rule:
`Q = Q2, X2 = Q2, L2 = [1,3,7], member(Q, [1,3,7]), X2 = 1`.

It will then match the next member, which is 1.

We can keep doing this until no more matches are found, in that case it will say "no".

What if we did this:

```prolog
?- member(42, L)

L = [42|_19];
L = [_21, 42| _97]
```

The variable `_19` is generated from the wildcard. It knows you don't care about the postfix after 42. 
If we ask for it again, it will check the rules, choosing the second rule, and getting `member[_21, 42| _97]`.

## Optimizations

If we have an expensive clause and a cheap clause: `e(X), c(X)`, then it's MUCH faster if we do:

```prolog
p(X) :- c(X), e(X).
```

Rather than:

```prolog
p(X) :- e(X), c(X).
```

However, if `c(X)` succeeds very often, and `e(X)` succeeds once, then we might have tons of candidates on `e(X)`, which
will be more effort than before.

## Another Example : `reverse`

This is a naive reverse:

```prolog
reverse([], [])
reverse([X|L], RX) :- reverse(L, R), append(R, [X], RX).
```

The above is O(N^2). How do we do this optimized? We should have an accumulator!

L stands for list, A means accumulator, R is the result.

```prolog
reva([], A, A).
reva([X|L], A, RA) :- reva(L, [X|A], RA).
reverse(L, R) :- reva(L, [], R).
```

The above is O(N).

## To debug

```prolog
?- append(A,B,[2,7,8]), write(foo(A,B)), nl, fail

foo([], [2,7,8])
foo([2], [7,8])
...
no
```

`fail` always fails, `nl` always prints new lines and fails, `write(foo(A,B))` 
just writes and fails, and then we go back to another choice of `append`.

We can try another:

```prolog
?- append(A,[2,7],B), write(foo(A,B)), nl, fail

foo([], [2,7])
foo([_28], [_28, 2, 7])
...
foo([_28,_19,_3,_5,_10...], [_28,_19,_3,_5,_10...,2,7])
...
```

The above is an infinite loop because we have infinite answers! This is like a `while(true)`.
`write` will look into the data structure `[ foo , A , B ]`, and sees that `foo` is not a
special name, so it will print the function name, with its arguments `A,B`.

NOTE: We can have redundant rules, like if we added an extra `permute([X], [X]).`.
However, it will slow down our program and usually it's really bad for prolog especially.






















