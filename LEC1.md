# Prequel

## Prompt

**Prompt: Write a program that matches `[A-Za-z]+` words and returns a list of words to frequency sorted by frequency.**

`tr a b` translates all `a`'s into `b`'s.

Thus, `tr -cs 'A-Za-z' '[\n*]'` does this:

- `-c` is `c`omplement, `-s` is `s`queeze.
- Thus, we take every subsequence that's not a letter into a new line.

```bash
$ tr -cs 'A-Za-z' '[\n*]' 
Four score and seven (7) years ago
Four
score
and
seven
years
ago
```

`sort` sorts all lines.

`uniq -c` gives the `c`ount for duplicates of specific lines.

`sort -rn` sorts `n`umerically instead of alphabetically, and does it in `r`everse.

Take it all together, and we get:

```bash
$ echo "Four score and seven years ago and and" | tr -cs 'A-Za-z' '[\n*]' | sort | uniq -c | sort -rn
   3 and
   1 years
   1 seven
   1 score
   1 ago
   1 Four
```

## Sapir-Wharf Hypothesis

A linguistic hypothesis(more people disbelieve than believe), that states:

1. **The language we use determines how we view the world and how we think.**
2. **The structural diversity of languages is essentially limitless.** 

This hypothesis _somewhat applies to programming languages_.

# Course Info

Instructor name : **Paul Eggert**

Office : 4532J Boelter

Office Hours : Monday 2PM, Thursday 10AM.

Grading : _40/20/40_, _homework/midterm/exam_.

**Open book & notes for exams**, but questions are indirect, logical consequences of things in the book.

There's a **drop-dead date**: last due date is last Friday's discussion, 11:55 PM.

**Textbook** : Adam Brooks Webber

Week 1: Chapter 1, 2, any chapter with the name "ML"

## Topics - Theory & Practice

### Theory (Lecture)

- **Language Design** (How do you invent a new language?)
- **Syntax** (Form of the language)
- **Semantic** (Meaning of the language)
- **Functions**
- **Names**
- **Types** - `int`, `std::queue<int>`
- **Control** - `if`, `std::exception`, etc
- **Objects**
- **Concurrency** - How should we do this?
- **Scripting**

### Practice (Homework)

- **OCaml** 
    - Functional programming language.
    - Focus on syntax.
- **Prolog**
    - Focus on control and names.
- **Java**
    - Focus on concurrency, types.
- **Python**
    - Focus on concurrency, scripting.
- **Scheme** (Variant of lisp)
    - Focus on semantics and functions.

## TIOBE index (Popularity)

1. **Java** - 12%
2. **C** - 7%
3. **C++** - 5%
4. **C#** - 4%
5. **Python** - 3%
6. **PHP** - 2%
7. **Javascript** - 2%
8. **Visual Basic** - 2%
9. **Perl** - 2%
10. **Ruby** - 2%

# Language Design Issues

## Orthogonality

An orthogonal language is where its features are not related to each other.

An example of a **non-orthogonal** language: In C, a function can return any type except for array types, i.e.`int[]`.

```C
typedef ___ jmp_buf;
jmp_buf foo(void);
```

In this case, you **cannot** return a function that returns `jmp_buf` if it's an array type!

































