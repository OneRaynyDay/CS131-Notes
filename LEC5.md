# More about OCaml

Let's consider the following reverse function:

```ocaml
let rec recerse = function 
    | [] -> []
    | hd::tl -> (reverse tl) @ [hd];;

reverse: 'a list -> 'a list = <fun>
```

This is not *tail recursive!* We should use something called an **accumulator**, which is something that caches all the work we've done before.

```ocaml
(* We call it revapp for reverse & append *)
let rec revapp a = function
    | [] -> a
    | hd :: tl -> revapp (hd::a) tl

let reverse = revapp []
```

Now it's tail recursive. It looks like a for loop here.

## What if we did the above in C?

```c
struct l{
    int c;
    struct l* next;
};

struct l* revapp(struct l* a, struct l* b){
    if(b==NULL) return a;
    struct s* p = malloc(sizeof(*p));
    p->c = b->c;
    p->next = a;
    return revapp(p, b->next);
}

struct l* reverse(struct l* b){
    return revapp(NULL, b);
}
```

Now it looks good and dandy, but what about:

```ocaml
let foo l = revapp (37::l)
```

We want foo to return a function.

In C++:

```c++
// int list -> int list
typedef struct l*(*ilf) (struct l*);

ilf saveb; // this becomes global! really bad!
ilf foo(struct l* b){
    saveb = b;
    return foohelp;
}

struct l* foohelp(struct l* p){
    return revapp(saveb, p);
}
```

This type of higher order function calls do NOT work well in C because if the function is recursive, then the state of `saveb` will be messed with in the middle of the call.

In order to support recursion, we need the `saveb` to be a heap, and that really sucks, sinc  state of `saveb` changes.

## Back to Types

In ocaml, what is this? `([],[])` is generic! The type is: `'a list * 'b list`.

Primitives: 
- `int` of various sizes(possibly unsigned).
- `char`, `unsigned char`, `signed char`. (`char` might be signed, might be unsigned, depends)
- `float`, `double`, etc.
- `void`. In OCaml, it's called a `unit`. It's just `()`.

```ocaml
utop # ();;
- : unit = ()
```

Can divide types into **exposed type** and **abstract type**. Exposed type means no encapsulation, white box. Abstract is black box, like java hiding its members using setters and getters.

Now is an `int` an exposed type? It's not completely!

An integer may have *junk bits*. They are ignored by the hardware. An integer could be represented using ones complement, two's complement, or signed magnitude. We have no idea which one is currently used for implementation!

```c++
int f(float x){
    int *p = (int*) &x;
    return *p & ((1<<23)-1); // very dangerous!
}
```

There's a lot of issues with floats, like deciding whether the program should crash when it runs into an infinity or a NaN. However, we suppress that, so we now have to do much more error checking.

# Static vs. Dynamic Type Checking

The advantage of static type checking is:

- reliability
- performance

The advantage of dynamic type checking is:

- flexibility
- less bureaucracy

In java, we have a hybrid. For inheritance, we will check during runtime whether a derived class is actually a derived class of a base class.

# Strongly typed vs Weakly typed

In C/C++, this is not strongly typed.

```c
T *p = (U*) p; // we just changed the type!
```

In OCaml, we can't do that! It's a strongly typed language.

# Type equivalence

**Name equivalence** : Have the same name

**Structural equivalence** : Same data structure, same operations.

C++ uses *name equivalence* for:

```c
struct s{int foo;}; // these are not the same!
struct t{int foo;}; // their names are not the same.
```

C++ uses *structural equivalence* for:

```c
typedef long pid_t; // 2 types with diff names
typedef long ptrdiff_t; // but they're same type!
```

## Subtyping issues

In `char *` vs. `const char *`, which ones are subsets? 

```c
char *p = ...;
char const *c = ...;

// THIS IS NOT ALLOWED IN C
p = c; // what could go wrong?
*p = 'a'; // uh oh!

// THIS IS ALLOWED IN C
c = p; // this is fine!
```

We would think that `char const *c` would be a subclass of `char *c`. However, we should think about it this way:

A subtype is one that can do **more stuff**. A `char *` can do more than a `const char *`.

# Polymorphism

Definition is : **an operation that can accept many different types.**

There are 2 types of polymorphism: **ad hoc**, and **systematic**.

## Ad Hoc
One kind is **overloading**, and the other is **coercion**.

A function could have several different patterns(parameter). This is overloading.

A function could coerce cast into its desired arguments. This is coercion.

A drawback to coercion is that **we can lose precision.** If we coerce long into an int, then it will silently strip the number, or:

```c
int i = -1;
unsigned int j = 2;
if(i < j) ...
else ... // i is actually greater! It's UINT_MAX.
```



