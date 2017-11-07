# Prolog

It's a **logical programming language** (declarative).

We declare rules and facts, and we can query if a statement is true, or there are bindings that makes a statement true.

```prolog
person(alice)
person(bob)

person(alice) -> yes
person(X)
X = alice ?; -> yes
X = bob ?; -> yes
```

## Components:

**lowercase names** : atoms, like alice, bob.

**predicates** : person

**variables** : starts w/ an upper case, like X

**arity** : # of arguments of a predicate.

Here's another example:

We will try to define father-child relationships:

```prolog
father(orville, abe)
father(abe, homer)
father(homer, bart)
father(homer, lisa)
father(homer, maggie)

grandfather(X,Y) :-
    father(X,Z),
    father(Z,Y)

conclusion :- hypothesis
conclusion :- h1, h2. ; and
           :- h1; h2. ; or
```

## Quick rules

1. All statements ends with .
    - e.g. likes(john, susie).
2. Only 1 conclusion per statement.
3. Can't compose predicates on the left side of the `:-`.
    - `conclusion :- h1, h2.` means conclusion reached IF h1 AND h2.
4. `(h1, h2, h3); (h4, h5)` is a disjunction of conjunctions and is allowed.

## Practice: `birthparents`

```prolog
birthparents(Father, Mother, Child) :- father(Father, Child), mother(Mother, Child).
```
 
## Complex terms

- `(john, 7)` are tuples.
- `cons(9, cons(3, empty))`, and we can define `empty` to be a data type.
    - `ourlength(empty, 0)` defines empty of size 0.
    - `ourlength(cons(_, RL), L) :- 1 + ourlength(RL, RLL)`

## Arrays

```prolog
[1,2,3,a,b,c]
[X|R] // is analogous to X::R
[X, Y|R] // X::Y::R
```

For some example functions:

```prolog
firstisa([a|_]).
```

the length of the list:

```prolog
length([], 0).

length([_|R], L) :- length(R, RL), L is RLL + 1.
```

```prolog
append([], L2, L2).
append(L1, [], L1).
append([X|Y], L2, [X|Z]) :- append(Y, L2, Z).
```

```prolog
reverse([], []).
reverse([X|Y], R) :- reverse(Y, RY), append(RY, [X], R).
```

We could also define an accumulator:

```prolog
revacc([], A, A).
revacc([X|Y], Acc, R) :- revacc(Y, [X|Acc], R).
reverse(L, RL) :- revacc(L, [], RL)
```

## Equalities

2 types:

1. `(=)` -> `x = y.`, and then `y = x` gives "yes". It's **unifying**.
2. `(==)` -> **not unifying, but rather checks whether already equal.**

Similarly, inequalities are:

1. `(\=)` is the opposite of `(=)`.
2. `(\==)` is the opposite of `(==)`.

## More examples

```prolog
member(E,L) <-- E is some element, L is a list.

member(E, [E|_]).
member(E, [L|R]) :- member(E, R), L \== E.

compress(L1,L2) <-- 2 lists
compress([a,a,a,b,b,c,d,d,d,a,a],[a,b,c,d,a])

compressh([], A, A).
compressh([H1|T1], [], A) :- compressh(T1, [H1], A).
compressh([H1|T1], [H2|T2], A) :- 
(compressh([T1], [H2|T2], A), H1 == H2);
(compressh([T1], [H1,H2|T2], A), H1 \== H2).

compress(L1, L2) :- compressh(L1, [], L2).
```

```prolog
// Better implementation

compress([], [])
compress([X,X|R], Ans) :- compress([X|R], Ans).
compress([X,Y|R], [X|Ans]) :- X \== Y, compress([Y|R], Ans).
```
















