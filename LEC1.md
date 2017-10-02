Prompt: Write a program that matches `[A-Za-z]+` words and returns a list of words to frequency sorted by frequency.

`tr a b` translates all `a`'s into `b`'s.

Thus, `tr -cs 'A-Za-z' '[\n*]'` does this:

- `-c` is complement, `-s` is squeeze.
- Thus, we take every subsequence that's not a letter into a new line.

```
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

`uniq -c` gives the count for duplicates of specific lines.

`sort -rn` sorts `n`umerically instead of alphabetically, and does it in `r`everse.

Take it all together, and we get:

```
$ echo "Four score and seven years ago and and" | tr -cs 'A-Za-z' '[\n*]' | sort | uniq -c | sort -rn
   3 and
   1 years
   1 seven
   1 score
   1 ago
   1 Four
```
