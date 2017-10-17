# Some more OCaml:

Implementing a dictionary using first-order functions:

```ocaml
let empty key = None;;

let put k v d dkey = if dkey = k then Some v else d dkey;;

let x = put 3 2 empty;; 

(* gives a dictionary where x 3 will give Some 2, and x 5 will give None. *)
```

## HW 2

Example of specs:

```
["3", "+", "4"]

Expr
Term Binop Expr
Num Binop Expr
"3" Binop Expr
"3" "+" Expr
"3" "+" Term
"3" "+" Num
"3" "+" "4"
```

`frag` is a list.

For example, we have fragment `[1;2;3]`. We want to match the prefix of the fragment, which could be: `[],[1],[1;2],[1;2;3]`. The suffix could be `[],[3],[2;3],[1;2;3]`.

If we match prefix `[1;2]`, then we will return suffix `[3]`.

## Acceptor Function

A function with 2 arguments: a `derivation` and a `suffix`. 

The derivation for the example above is:

```ocaml
[ [Expr -> Term Binop Expr], [Term -> Num], ... [Num -> "4"] ]
```

The suffix of this is `[]`, since `["3","+","4"]` was all taken as the prefix(consumed by `Expr`).

### An acceptor that accepts everything

```ocaml
let accept_all d s = Some(d,s);;
```

### An acceptor that doesn't accept the empty suffix

```ocaml
let accept_noempty d s = match s with
    | [] -> None
    | x -> Some(d, x)
```

## Matcher Function

A function with 2 arguments: a `acceptor` and a `fragment`.

It does 4 things:

1. Tries to find a matching prefix. (The immediate next one)
2. If no prefix, then return None.
3. Else, it calls the acceptor with the derivation & the suffix.
4. If the acceptor returns None, then back to step 1.

## HW 2's `parse_prefix`:

We want to take in argument of the grammar and returns the derivation.

```ocaml
let parse_prefix grammar acceptor fragment = ...
```

It builds a **matcher** based on the **grammar**.

For example:

```ocaml
parse_prefix awkish_grammar accept_all ["9"]
```

To build a derivation for `["9"]`, we have:

```
"9"

Returns (Derivation and Suffix):
Some([[Expr -> Term]    <-- Derivations
      [Term -> Num]
      [Num -> 9]],
        
      [])    <-- Suffix
```

## An Example

In this problem(Completely different):

`matcher` <- acceptor & frag

`accept` <- suffix

### Q1: Write the empty prefix matcher

```ocaml
empty_matcher accept frag = accept frag;;
```

### Q2: Write the matcher that doesn't match anything

```ocaml
no_matcher accept frag = None;;
```

### Q3: Write the matcher that matches if the first element is equal to V

```ocaml
let match_value v accept frag = match frag with
    | hd :: tl -> if v = hd then accept tl else None
    | [] -> None
```

### Q4: Takes 2 Matchers, m1 and m2, and appends them.

```ocaml
let append_matchers m1 m2 frag accept = 
m1 frag (fun s -> m2 s accept)
```

### Q5: How would you make a matcher that matches a sequence of values only using `match_value` and `append_matchers`?

```ocaml
let rec match_many vs accept frag = match vs with
    | v :: t -> match_value v accept ( fun s -> match_many t s accept )
    | [] -> frag
```


