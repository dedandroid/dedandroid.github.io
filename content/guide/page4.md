# Bandit Guide — Levels 30–33

[Back to Guide Index](/content/guide.md)

---

## Level 30

Password: `qp30ex3VLz5MDG1n91YowTv4Q8l7CDZL`

Focus on git tags.

```bash
git tag
git show secret
```

> `secret` is the name of the git tag. Note the tag points to a blob object, not a commit.

---

## Level 31

Password: `fb5S2xb7bRyFmAvQYQGEqsbhVyJqhnDy`

Push a file to the remote repository per the README instructions:

```
File name: key.txt
Content: 'May I come in?'
Branch: master
```

> Note: The `.gitignore` file blocks `*.txt` files, so delete it first. Although the push will be rejected, the password is returned.

---

## Level 32

Password: `3O9RfhqyAlVBEZpVb6LYStshZoqoSx5K`

The login shell automatically uppercases every command and attempts to execute it:

```
bandit32:x:11032:11032:bandit level 32:/home/bandit32:/home/bandit32/uppershell
```

**Solution:** Use bash special variables — unlike environment variables, these are unique to the bash process itself and are not inherited by child processes:

```
$0   # script/shell name
$1   # first argument
$2   # second argument
$#   # number of arguments
$?   # exit status of last command
$$   # PID of current shell
$!   # PID of last background process
$_   # last argument of previous command
```

In the caps shell, typing `$0` expands to `/bin/sh`, which gets executed as `/bin/sh` and spawns a proper shell.

> `uppershell` is owned by bandit33 with a setuid bit for group bandit32, so the spawned shell runs with bandit33's privileges.

---

## Level 33

Password: `tQdtbs5D5i2vJwkO8mEyYEyTL8izoeJ0`

```
Congratulations on solving the last level of this game!

At this moment, there are no more levels to play in this game. However, we are
constantly working on new levels and will most likely expand this game with more
levels soon. Keep an eye out for an announcement on our usual communication channels!
In the meantime, you could play some of our other wargames.

If you have an idea for an awesome new level, please let us know!
```

---

[Previous: Levels 20–29](/content/guide/page3.md) | [Guide Index](/content/guide.md)
