# Grammars

## BNF & E-BNF (RFC 5322)

- "terminal" `( )*`
- 'terminal' { }
- `[optional]` 0 or 1
- `{repetition}` 0 or more
- `(grouping)` 1
- `(* comment *)`
- `x*` 0 or more
- `x-y` x except y (trouble). Infinite sets diff is hard. Only allow simple symbols.
- `x,y` concatenate
- `x|y` x or y

## RSO-EBNF

- syntax =` syntax rule, {syntax rule};`
- syntax rule =` meta id, '=', defns list, ';';`
- defns list =` defn, {'|', defn};`
- defn =` term, {',', term};`
- term =` factor, ['-', exception];`

What's the interpretation of X - Y | Z?

It's (X - Y) | Z, due to the rules:

1. `defn, {'|', defn}`
2. `(term, {',', term}) {'|', defn}`
3. `(factor, '-', term), '|', term`

## Syntax diagrams / charts/ racetracks

```
S -> aSb | c

Becomes a syntax diagram:

S |-> [a] -> S -> [b] -|> ->
  -------> [c] -------->
```

### Example: Scheme grammar

```
<cond> -> ( cond <cond clause>+ )
        | ( cond <cond clause>* (else <sequence> ) )

Becomes a syntax diagram:

                        <-----------
cond -> [c] -> [cond] -> cond clause --|-----------------------------|-->[)]-->
                      ------------------>[c]->[else]->sequence->[)]->
```

### Regular Expression

Regex is EBNF for a non-recursive grammar.

1. Use macro expansion
2. Combine all charts into 1
3. Obtain a finite state machine(not recursive)

Regex has these drawbacks (useless rules):

1. Cannot parenthesize. Need recursion, since parentheses could be infinitely deep.
2. Unwanted recursion. A grammar that's written recursively, but could be done non-recursively. E.x.(`S -> aSb | c` is equivalent to `a*cb*`, i.e. recursive but could be DP)
3. A nonterminal that's used but not defined. (This is allowed in EBNF)
4. A nonterminal that's defined but not used. (A waste)
5. Extra syntactic constraints that can't be easily captured in grammar:

```
S -> NP VP
NP -> ADJ* N
VP -> V ADV*
N -> the dog | dogs
N -> barks | barks
```

These are all valid sentences:

```
the dog bark <-- wrong!
the dog barks
dogs bark
dogs barks <-- wrong!
```

Would have to be fixed with:

```
S -> SNP SVP | PNP PVP
SNP -> ADJ* SN
PNP -> ADJ* PN
SVP -> SV ADV*
PVP -> PV ADV*
SN -> the dog
PN -> dogs
SV -> barks
PV -> bark
```

This works, but this is exponentially growing grammar rule! This makes the grammar hard to understand!

6. Ambiguous grammars. 2 different parses for the same sentence. i.e. `x+y*z` is `(x+y)*z` or `x+(y*z)`. To fix this, we need to complicate the grammar.

## An example grammar:

```
stmt:
    | ;
    | expr ;
    | return expr ;
    | goto ID ;
    | break ;
    | continue ;
    | { stmt-list }
    | while ( expr ) stmt
    | do stmt while ( expr ) ;
    | if ( expr ) stmt
    | if ( expr ) stmt else stmt
    | switch ( expr ) stmt
```

For `while ( expr ) stmt`, we need the parentheses to prevent something like : `while x * p;` to be ambiguous: `while (x) *p;` or `while (x*p);`?

For `do stmt while ( expr ) ;`, this `while ( expr );` doesn't need to have parentheses but we have it there to be consistent with the while loop.

With the rules `if ( expr ) stmt` and `if ( expr ) stmt else stmt`, we can ambiguate this:

```
if (...) if (...) ... else ...
```

So we need to fix it slightly. We can theoretically do something like:

```
if ( expr ) [stmt-"if ( expr )"]
if ( expr ) stmt else stmt
```

But we can't just subtract rules from a nonterminal like that, so we have to do something like:

```
nice-stmt: (removed if-else's)
    | ;
    | expr ;
    | return expr ;
    | goto ID ;
    | break ;
    | continue ;
    | { stmt-list }
    | while ( expr ) stmt
    | do stmt while ( expr ) ;
    | switch ( expr ) stmt

if ( expr ) nice_stmt else stmt
if ( expr) stmt else stmt
```

How would we come up with the match statement rule for ocaml?

The equivalent problem is:

```ocaml
match E with
    | x :: y -> ...
    | [] -> match F with
        | [] -> g
        | x::y -> b
```

is actually parsed as:

```ocaml
match E with
    | x :: y -> ...
    | [] -> match F with
        | [] -> g
    | x::y -> b
```

Another issue with unary operators:

```c++
// all of these are allowed!
// According to C++, if there are competing side-effects,
// then it's undefined behavior.
int a = b++ - b++;
       (10)  (11)
int a = b++ - b++;
       (11)  (10)
int a = b++ - b++;
       (10)  (10)
```


