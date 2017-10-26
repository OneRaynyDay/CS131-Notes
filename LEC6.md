# Polymorphism

OCaml says "ad hoc" polymorphism is bad. "Universal" is better.

Ad hoc polymorphism is when functions take in either overloaded arguments, or they coerce the inputs into the correct classes.

## Templates

Templates are like macros, like `List<T>`. The compiler substitutes `T` with whatever type we inputted, like perhaps a `String` in java. These templates are **instantiated at compile time.** When we run them, we will observe no `List<T>`, but rather a bunch of different classes like `List<String>`, `List<Thread>`, `List<Integer>`, etc.

## Generics

Generics are **not macros**. Instead they are **directives to the compiler.** It forces the compiler to check that the class works for **any** specified type. This only generates 1 copy of the code, and then we can inject the type during runtime. Thus, it is not individually optimized.

## Templates vs. Generics

**Templates**

- Long compile runtime
- Bloated # of classes
- Faster runtime because `sizeof` for example can be evaluated at compile time.

**Generics**

- Short compile runtime
- Not individually optimized, since checked for any type
- Slower at runtime because `sizeof` cannot be evaluated at compile time(Generics can have different sizes).

There's no `sizeof` operation in java, because all objects are of the same size! They are **all pointers(references)!**

## Java without generics

```java
List l = new LinkedList();
l.add(new Integer(0));
Integer n = l.iterator().next(); // won't compile! If this is completely generic then returns Object!
```

The above will not compile, and will complain.

```java
List l = new LinkedList();
l.add(new Integer(0));
Integer n = (Integer) l.iterator().next(); // better!
```

This could work, but is pretty ugly.

```java
List<Integer> l = new LinkedList<Integer>();
l.add(new Integer(0));
Integer n = l.iterator().next(); // it's an Iterator<Integer>, so must return Integer.
```

We will run faster here because **during the runtime we don't need to cast the value that comes out.**

## Subverting java's types

```java
List<String> ls = ...;
List<Object> lo = ls;
lo.add(new Thread());
String s = ls.get(); // uh oh!
```

So actually... `List<Object>` is not a base type of `List<String>`! Otherwise we can have nonhomogenous data structures.

The same argument goes to the previous lecture: `const char*` vs. `char*`! **You are only a subtype if you have a superset of operations.** `char*` has a superset of operations, and `List<Object>` has a superset of operations. 

## Java's wildcard

In java, if you don't care what the object is:

```java
void printAll(List<?> l){
    for(Object o : lo){
        System.out.println(o.toString());
    }
}
```

We don't care what list it is, because we know `toString()` exists for all objects.

Here's an example that **doesn't work**:

```java
// this doesn't work, because ? means anything.
// The ?[] type could be different from Collection<?> type.
void convert(?[] a, Collection<?> c){      
    for(int i = 0; i < a.length; i++){
        c.add(a[i]);
    }
}
```

Here's the fixed version:

```java
void <T> convert(T[] a, Collection<T> c){      
    for(int i = 0; i < a.length; i++){
        c.add(a[i]);
    }
}
```

using **generics** to bind the types together such that they're the same.

However, we can't do this:

```java
Integer[] al;
Collection<Object> co;
convert(al, co); // won't work! Integer != Object!
```

So we can relax the generics a little bit:

```java
// U is a superclass of T
void <T, U super T> convert(T[] a, Collection<U> c){      
    for(int i = 0; i < a.length; i++){
        c.add(a[i]);
    }
}
```

or...

```java
// U is a superclass of T
void <T> convert(T[] a, Collection<? super T> c){      
    for(int i = 0; i < a.length; i++){
        c.add(a[i]);
    }
}
```

## Another kind of generics: Duck Typing

_If it quacks like a duck, and waddles like a duck, then it is a duck!_

- Statically, there's no type checking. We just declare the variables, pass them around, etc.
- We only type check during runtime.
    - but sometimes, it might not even typecheck.
- Nobody cares about the behavior of the object until we need to inspect the behavior.

Thus, subtyping is a little hard in duck typed languages.

# How can we check whether 1 interface is a subtype of another?

This goes back to **multiple inheritance**:

```
Object -> Graphics -> Rectangle -> DrawableRectangle
       L-> Figures -> Drawable -^
```

Java does not allow something like this, because multiple inheritance is so complicated.

Java **does allow `extends` a base class AND `implements` of multiple interfaces at the same time**. You can have something like:

```java
class DrawableRectangle extends Rectangle implements Drawable, Serializable
```

# The fastest computer : Sunway Taihu Light

- 40960 nodes
- Each containing a SW26010 processor
- Each containing 64 compute CPU + 1 mgmt CPU

# Why was Java made?

Several issues with C++:

1. Crashes alot
2. Too complicated w/ memory instructions
3. Multiple builds(different one for each instruction set)
4. Executables are too large(upgrading your toaster takes too long)
5. Recompile-the-world problem

Took the features from Smalltalk but did not like:

1. Weird smalltalk syntax
2. Dynamically typed
3. Getting programs off the net is hard!



