# Object-Oriented Programming

We want **encapsulation**. We don't want to see what's inside of each of these objects.

## Java

- Platform Agnostic (turns into java bytecode, runs in JVM)
- Object oriented programming

**Features**:
1. Object Oriented Programming
2. Mutability
3. No pointers
4. Pass by value semantics
5. Similar syntax to C++
6. Statically typed
7. Garbage collection

We use JDK 8 for CS131.

### Arrays

```java
int[] arr;
arr = new int[5];

int j = 11;
arr = new int[j]; // Can be dynamic in size!
```

### `==` operator

... compares pointer values for objects.

```java
String a = "a";
String b = "a";

if(a == b) { ... } // does not work! 2 different objects.
if(a.equals(b)) { ... } // does work! contents are the same.
```

### Class vs. Objects

1. An object is an instance of the class.
2. Classes defines data and methods that the objects will have. Objects will have the specific instantiations of that data.

Here's an example of a class, and it performs encapsulation:

```java
public class BankAccount{
    private int money;
    public BankAccount(int money){
        this.money = money;
    }
    public int getBalance(){
        return money;
    }
    public int withdraw(int sum){
        int temp = money;
        money = max(0, money-sum);
        return max(temp, sum); // returns how much money was taken out
    }
}
```


