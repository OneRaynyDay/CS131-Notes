# Java Memory Model - Causality

```java
o1.x = 3;
print(o2.y); // this will not be affected by o1.x... right?
```

The Java compiler loves to cache. When we cache, it's reasoning based on causality:

What we get in the beginning:
```java
o.x = 3;
o.y = 4;
o.z = o.x; // we know this has to be 3!
```

turns into(more-or-less):
```java
// movl %rax 4(%rbx)
o.x = 3;
o.y = 4;
// movl 8(%rbx) %rax
o.z = 3; // pretty much looks at the register that holds the value of o.x
```

Thus, **caching and synchronization are enemies of each other**. 

Some fixes:

1. `volatile` bypass caches.
2. `synchronized` methods holds and releases locks.

## JMM standardizes optimizations allowed to compilers

1. **Category A**: Normal loads & stores
2. **Category B**: `volatile` load & enter monitor
3. **Category C**: `volatile` store & exit monitor

Compilers are allowed to make a ton of optimizations for **category A**, 
but not for **category C**, and much less for **category B**.

```java
// Can be interchanged
o1.x = 3;
o2.y = 4;
```

```java
// Cannot be interchanged. o1 might be o2
o1.x = 3;
o2.x = 4;
```

```java
class C{
    int x;
    volatile int v;
    synchronized int foo(){ ... }
}
...
o.x = 3; // can interchange where its position is!
o.foo(); // cannot interchange!
```

The 3 types of interchangeable instructions (second op interchanges with first op):

1. A interchanges with A
2. A interchanges with B
3. C interchanges with A

```java
String s1 = "/usr/tmp";
// We can create an s2...
String s2 = s1.substring(4);
// but before we're done, we assign some global g to s2.
g = s2;
```

# Java names and bindings

Binding can mean many things, and they have different binding times:

1. Bind variable to its value (At runtime)
2. Bind variable to an address in memory (At runtime, when function is executed)
3. Bind variable to an alignment requirement (At compile time, checks machine)
4. Bind variable to a type (At compile time)

## Primitive namespaces

Here's an example of namespaces in c++ that has no collisions.

```c++
int f(void){ // a function f
    struct f{ int f; } f; // an instance of struct f, called f, with field f
    enum f{ z }; // an enum of type f, containing z
    struct g{ int f; } g; // another struct with a field f
    #include <f> // includes some f file
    #define f g // replaces all f's with g's
    f: goto f; // gets turned into g's and loops forever.
}
```

# Information hiding

Parts of your program doesn't know details of other parts of your program.

There are 2 ways to do information hiding:

1. Keywords for each name
2. Interfaces/signatures

Why information hiding?

1. Easy to change interfaces
2. Separate compilation

# What lives in memory?

0. Libraries(Which can contain from 1-end)
1. Static variables
2. Stack(Variables local to a function, return addresses)
3. Heap(Dynamically allocated variables)
4. Code
5. Constants and literals
6. Temps(intermediate values)
7. Metadata(tells the memory manager where things are)




