# Bandit Guide — Levels 0–9

[Back to Guide Index](/content/guide.md)

---

## Level 0

Password: `bandit0`

---

## Level 1

Password: `ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If`

**Solution:** To open a file named `-`, use `./-`

---

## Level 2

Password: `263JGJPfgU6LtdEvgfWU1XP5yac29mFx`

**Solution:** To open a file with spaces, use the same approach or escape char `\`

---

## Level 3

Password: `MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx`

**Solution:** In a hidden file (starting with `.`)

---

## Level 4

Password: `2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ`

The only readable file out of a lot of other files.

**Solutions:**

```bash
# List all file types
for i in ./*; do file $i; done

# Find only text files from the above list
for i in ./*; do file $i | grep "text"; done

# Using find's exec
find . -type f -exec file {} \; | grep "text"
```

> Note: `exec` does NOT run the command in a shell. It ends at `\;` and literally function-calls `file`, passing it `{}` (current file). It CANNOT interpret `|`. That is handled by the shell like a regular piping expression, sending all output of `find` to `grep`.

```bash
# More efficient: file is only called once with all outputs passed
find . -type f -exec file {} + | grep "text"
```

---

## Level 5

Password: `4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw`

Non-executable, with certain size.

**Solution:**

```bash
find . -type f -not -executable -size 1033c | xargs cat
```

---

## Level 6

Password: `HWasnPhtq9AVKe0dmk45nxy20cvUa6EG`

Fixed size, read permission, with certain owner and group name.

**Solution:**

```bash
find / -type f -readable -size 33c -user bandit7 -group bandit6 2> /dev/null | xargs cat
```

> Note: `find` tries to find readable files, but still throws a "permission denied" error if it cannot enter a directory. So redirect errors to `/dev/null`.

---

## Level 7

Password: `morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj`

In a long text file, next to word "millionth".

**Solution:**

```bash
cat data.txt | grep "millionth"
```

---

## Level 8

Password: `dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc`

Find the only unique line in the file.

**Solution:**

```bash
cat data.txt | sort | uniq -u
```

> `uniq` only works with adjacent lines, so sort all input first to make duplicates adjacent.

---

## Level 9

Password: `4CKMh1JI91bUIZZPXDqGanal4xvAg0JM`

One of few readable strings preceded by many `=`.

**Solution:**

```bash
strings data.txt | grep "====="
```

---

[Next: Levels 10–19](/content/guide/page2.md) | [Guide Index](/content/guide.md)
