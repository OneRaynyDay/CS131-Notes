# OCaml - Functional Language with Strong Types

1. Functions are 1st class objects.
    - you can pass them as arguments.
2. Type inference. "Duck typing"
3. Strongly typed.
    - Hard to cast unless explicitly done so.

Primitive types in ocaml:

- `int` : 0,1,-1...
- `bool` : true, false
- `float` : 3.14159, 0.1, -2.0

## Using `let` is a const qualifier

```ocaml
# let six = 6;;
val six : int = 6
# six = six + 5;;
- : bool = false
```

## Functions in OCAML

### Defining

```ocaml
# let increment x = x + 1;;
val increment : int -> int = <fun>
# let increment_f x = x +. 1.0;;
val increment_f : float -> float = <fun>
# let mul x y = x * y;;
val mul : int -> int -> int = <fun>
# let double (x:int) : int = 2*x;;
val double : int -> int = <fun>
```

#### Closures - Binding values to arguments in functions

```ocaml
# let mult = fun x y -> x * y;;
val mult : int -> int -> int = <fun>
# let mult_4 = mult 4;;
val mult_4 : int -> int = <fun>
# mult_4 5;;
- : int = 20
```

#### Lambdas

```ocaml
# let double = fun x -> 2 * x;;
val double : int -> int = <fun>
```

The `fun x -> 2 * x` is a lambda function. We capture it with the name `double`.

#### Functions are first order

```ocaml
# let doTwice f b = f (f b);;
val doTwice : ('a -> 'a) -> 'a -> 'a = <fun>
# doTwice square 2;;
- : int = 16
```

#### Recursive

```ocaml
# let rec pow x n =
  	if n == 0 then 1 else x * pow x (n - 1);;
val pow : int -> int -> int = <fun>
```

### Inputs cannot be implicitly casted

```ocaml
# increment(1)
  ;;
- : int = 2
# increment_f(1);;
Error: This expression has type int but an expression was expected of type
         float
#
```

## Operators

- `+`,`-`... for ints
- `+.`,`-.`... for floats
- `mod` is a modulo, i.e. `1 mod 3;;`
- `>`, `<`, `>=`, ... are assignment operators
- `^` is to concatenate a string.
- `int_of_char`, `char_of_int`, carry out the various conversions between numbers and character strings. Like `int_of_char 'a'`.

## Scope

```ocaml
# let hundred =
  	let ten = 10
  	and two = 2 in five * ten * 2;;
Warning 26: unused variable two.
val hundred : int = 100
# five * two;;
Error: Unbound value two
```

Everything that's used in an inner scope will no longer be defined as you leave it.

## Lists

```ocaml
# let ll = 1 :: 2 :: [];;
val ll : int list = [1; 2]
# let lr = ll @ [1;2];;
val lr : int list = [1; 2; 1; 2]
```

## Pattern Matching

```ocaml
// If we do [1;2;3;], then:

let rec sum l = match l with
    | hd :: tl -> hd + sum(tl)
    | [] -> 0;;

// hd matches 1, tl matches [2;3;]
```

If we wanted to add the occurence of the number 3 here:

```ocaml
let rec sum l = match l with
    | 3 :: tl -> 3 + sum(tl)
    | [] -> 0
    | _ :: tl -> sum(tl);;
```
