# Language Design Principles

- **Orthogonality**
- **Good Library Support**
- **Efficiency** : speed, power consumption, network access(RTT's).
- **Direct Control of Hardware**
- **Robustness** : Segfaults, coredumps are bad.
- **Readability/Writability**
- **Modularity**
- **Community Support** : StackOverflow
- **Backwards Compatibility**
- **Debuggability**
- **Simplicity** : easier to learn, explain, and implement.
- **Mutability** : easy to extend the language, and to port (The reason C won) to different platforms.

## Ex: Mutability in C - Syntax

We have the syntax:
```c
...
Obj args[7];
args[0] = a;
args[6] = g;
Foo(7, args);
...
```

And we want this:
```c
CALLN(Foo, a,b,c,d,e,f,g);
```

How do we extend the language?
```c
#define CALLN(f, ...) CALLMANY(f, ((Obj[]), {__VA_ARGS__}))
#define CALLMANY(f, args) (f)(ELTS(args), args)
#define ELTS(args) (sizeof(args) / sizeof(*(args)))
```

Macros are just placeholders that are injected into the actual code during compile time.

It becomes:

```c
(Foo)(sizeof((Obj[])({a,b,c,d,e,f,g})) / 
sizeof(*(Obj[])({a,b,c,d,e,f,g})), ((Obj[])({a,b,c,d,e,f,g})))
```

It's ugly, but it's good!

In the Bourne Shell(Bash)

```bash
IF a == b
THEN c=d; e=f;
ELSE g=i;
FI
```

If we wanted C to have the same ability:

```c
#define IF if(
#define THEN ){
#define ELSE }else{
#define FI }
```

# Syntax

_"What the program looks like" rather than what it "actually does"._

**Form independent of function** - Syntax is form, and semantics is function. Something can be completely syntactically valid, but the semantics do not, or the reverse.

In programming languages, syntax **cannot be ambiguous**.

```c
#include <stdin.h>
```

## Level 0 - Bytes

As a string in our file, we just have some bytes. We tell the C compiler to read these bytes and give us the program defined by it.

```
00110010
11010010
00100101
00010100
```

## Level 1 - Characters

If we use UTF-8 encoding then we have variable bytes that represent characters. (We can add different nationality languages not supported in ASCII-256)

```c
00110010 11010010 - #
00100101 00010100 - i
```

## Level 2 - Words

This process is called **tokenization**. It simplifies the program so that the compiler can understand the bigger idea.

```c
// <INCLUDE TOKEN, #include>
#include 
// <PACKAGE TOKEN, <stdin.h> >
<stdin.h> 
// <RETURN SIGNATURE TOKEN, int>
int 
main // ...
(
void
)
```

The expression `<TOKEN, text>` is called a **lexeme**. If we were building a debugger, the lexeme will also have the line number.

For example, an identifier has the regex: `[_a-zA-Z][_a-zA-Z0-9]*`.

### Custom keywords

If `if` is reserved, then we can't do something like:

```c++
int if = 0;
if++;
```

This can work in c, but not in c++. To preserve backwards compatibility, c11 had a lot of issues trying to come up with new key words.

### Parsing numbers

When we get the number `-39627`, what does it mean? Is it a negative integer, or a negative sign applied to a positive integer? 
In this case, it is just a negative integer.

When we get the number `-217473848`, it actually recognizes `217473848` as an unsigned int(cause it can't fit as an int), 
and then applies the `operator-` on the number and casts it back to an int.

# Context-free Grammars

## 5 Parts of Backus-Naur Form:

- **Token (terminal symbol):** - Member of a finite set $\Sigma$.
- **String:** finite sequence of tokens
- **Language:** Set of strings
- **Nonterminal:** member of a finite set $\mathcal{N}$. Represents the internal structures of strings.
- **Start symbol:** A specific nonterminal of which all strings start from.

## An issue with Context-free Grammar

Refer to [Internet RFC 5322](https://www.ietf.org/rfc/rfc5322.txt):

```
Message-ID: <bob@ucla.edu>
```

Every message has a unique ID. The syntax of a message id is of the following:

```
msg-id="<" word *("." word) "@" atom *("." atom) ">"
```

What's this `*`? It's a **kleene star**, making this not BNF. It's now called **Extended BNF(EBNF)**, which is just shorthand of a BNF:

```
msg-id="<" word dotword "@" atom dotatom) ">"

word ::= [a-zA-Z]*
dotword ::= "."[a-zA-Z]* | epsilon
```

We added the nonterminals `dotword` and `dotatom`.








