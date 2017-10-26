# Java

## Types

Everything inherits from the class `Object`:

```java
public class Object{
    public Object();
    public boolean equals(Object obj);
    public int hashCode(); // For pointers, will discard the higher 32 bits of the pointer address.
    public String toString(); // By default, is an address : #0x0ffff for example.
    // THE FUNCTION BELOW IS SIMPLIFIED
    public final Class getClass();

    protected void finalize() throws Throwable; // By default, does nothing.
    // cloning is not meant for general use
    protected Object clone() throws CloneNotSupportedException; // Could throw if cloning is too expensive.
}
```

The `equals()` function is behavioral equivalence, and `==` is address equivalence.

We could get into a lot of trouble if we have the situation where `!o.equals(o1) && o == o1` evaluates to true.

This is similar to the `hashCode()` function, 
which assumes if `o.equals(o1)`, then `o.hashCode() == o1.hashcode()`.

Why is `toString()` so important? **It's for debugging**! When you call `System.out.println()`, it will call the `toString()` function on the input.

`getClass()` gives us the class, i.e. `Class c = o.getClass();`. We can't do something like:

```
Class c = o.getClass(); // This is an object that describes the class.
c v; // invalid!
```

We also need a `final` at the signature, which tells us that final classes cannot be subclasses. 
`final` methods cannot be overridden, and `final` classes cannot be subclassed.
This is to avoid **cheating by subclassing**. It also makes your classes run faster.

So why do we need it? **It's just for debugging**! 

Why are there so many debugging functions? **Because debugging java would be easy!** We can write a java
debugger that uses **reflection**. The debugger wants to introspect and look at the code, which means it's
very easy to inspect.

Why do we need `finalize()` in java? It's used before garbage collecting the object! This is because
we need to release resources if the object is going to die! I.e. close the file descriptor.

### The actual getClass()

It looks like: `public final Class<? extends X> getClass()` where X is the **erasure** of o's type. X could be `Object`, `List`, or whatever. Confusin'.

## Parallelism (SMP, Symmetric Multiprocessing, Shared Memory Parallelism)

Inside the CPU(s), we have primitive values, instructions, references to objects, but it **cannot hold objects itself**.
None of these CPU(s) are farther/closer from RAM, they're all same speed and same distance. Fetching any object will take roughly the same time. This is done to simplify scheduling.

The way it's represented in Java is with a `Thread` object. **It's a single instruction pointer for a program running on a single CPU**.

### Thread life cycle

1. Creation via `Thread t = new Thread(r)`, where r is some `Runnable`. 
2. By calling `t.start()`, the thread object will call `Runnable`'s `run()` function. It allocates resources.
3. The thread will either:
    - compute as normal - RUNNABLE
    - sleep - TIMEDWAITING
    - wait - WAITING (WAITING on an internal event, via signal)
    - do I/O - BLOCKED (WAITING on an external event, via an interrupt or something)
    - exit (return from `run()` for example) - TERMINATED
4. Gets GC'd

In java, there's a keyword `synchronized`, which allows us to declare blocks of code synchronized:

```java
public class Foo{
    int v;
    ...
    // This kind of code is common in old java(pre 1.4)
    synchronized int next(){
        // it grabs a lock on object of type Foo (spinlocks)
        return v++; 
        // releases
    }
}
```

Nowadays, standard classes typically are unsynchronized, for performance. Java found its niche on server-side applications, where the server has a ton of cores. The `synchronized` key word may be causing a large bottleneck.

We now need to sacrifice **simplicity** for **speed and safety**.

```java
o.wait(); // removes all locks held by this thread, waits until o available.
o.notify(); // tells OS o is available. OS finds 1 thread wait on o and marks it runnable.
o.notifyAll(); // same as notify, but all threads marks runnable.
```

### Higher level classes

**Exchanger**:
```java
// not actual syntax
Exchanger x = new Exchanger();
v2 = x.exchange(v1);
v1 = x.exchange(v2);
```

**Cyclic Barrier** : All threads run, and then wait until all threads finish, and then start again.

**Countdown latch** : All threads wait until waiting, and then latch opens and all threads can run again.

### Optimization by Java compilers (Uh oh!)

The below optimization only works if we have single threaded execution!

```java
public class X{
    int i;
    int j;
}

... {
    X o = ...
    int v = x.i;
    int w = x.j;
    return v - x.i; // just returns 0 because it knows v == x.i;
}
```

We don't want the compiler to do this if we are ever calling the function in a multithreaded environment!

Thus, we can fix it by adding `volatile`:

```java
public class X{
    volatile int i;
    volatile int j;
}
```

## Names & Bindings

In java, we have visibility modifiers, like `private`, `protected`, and `public`. This is the same thing as in C++.

## Storage Management
