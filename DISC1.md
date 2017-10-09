# Types in OCaml

## "Optionals"

```ocaml
type optionalInt = 
    | Nothing
    | Something of int;;

let addOpt x y = match (x,y) with
    | Something x , Something y -> Something (x + y)
    | _,_ -> Nothing
```

## Optionals

```ocaml
type ('opt) myoption =
    | Nothing
    | Something of 'opt;;

let x = Something 3;;
let y = Something "s";;

let addOpt add x y = match (x,y) with
    | Something x , Something y -> Something (add x y)
    | _,_ -> Nothing
```

## Writing our own list type

```ocaml
type ('a) list = 
    | Empty
    | Cons of 'a * 'a list;;

let x = Cons (1,Empty);;

let rec addList l = match l with
    | Empty -> 0
    | Cons (a,b) -> a + (addList b)
;;
```

## Writing our own binary tree

```ocaml
type ('a) btree =
    | Leaf
    | Node of 'a * 'a btree * 'a btree

let rec inorder t = match t with
    | Leaf -> []
    | Node (v, left, right) -> (inorder left) @ [v] @ (inorder right)
;;

let l = inorder (Node(2, Node(1, Leaf, Leaf), Node(4, Node(3, Leaf, Leaf), Leaf)));;
```

## Writing our own dictionary

```ocaml
type ('k, 'v) dict = ('k * 'v) list

let put key value d = (key, value) :: dict

let rec get key d = match d with
| (k, v)::t -> if k = key then Some(v) else get key t
| [] -> None;;
```
