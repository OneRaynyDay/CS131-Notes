# Java - Synchronization

**Sequential Consistency**:
1. Ordering of statements must be maintained. The java compiler can optimize this. 
2. The scheduler can arbitrarily interleave two thread's operations on shared memory.

**Atomic Operations**:
1. Noninterruptible operation. Interleaving in the middle of the operation is not possible.

For example, `AtomicIntegerArray` allows us to `get()` and `set()` without interruption.

## Keyword `synchronized`

```java
class D{
    synchronized int a() { ... }
    synchronized int b() { ... }
    synchronized int c() { ... }
}
```

`synchronized` guarrantees a "happens before" relationship.

```java
x.a();
x.b(); // guarranteed to happen AFTER a
x.c(); // guarranteed to happen AFTER b
```

This allows us to have sequential consistency in `x`.

However, if `x != y`, and y is an instance of the same class, this can happen:

```java
x.a();
y.a();
y.b();
y.c();
x.b(); // guarranteed to happen AFTER x.a
x.c(); // guarranteed to happen AFTER x.b
```

## Keyword `volatile`

```java
volatile int x; // all reads come from main memory, and all writes fall through to main memory.
```

This allows us to prevent caching of incorrect values from different cpu's.














