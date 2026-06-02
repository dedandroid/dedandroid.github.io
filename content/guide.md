# Bandit Wargame Guide

`level : password`

`----` = indicates different techniques

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

## Level 10

Password: `FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey`

Base64 encoded file.

**Solution:**

```bash
base64 -d data.txt
```

---

## Level 11

Password: `dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr`

Simple ROT13 on a string.

**Solution:**

```bash
cat translate.txt | tr [A-Za-z] [N-ZA-Mn-za-m]
```

> Maps all `A-Z` to `N-ZA-M`. `N-ZA-M` concatenates `N O P Q R ... Z` with `A B C D E ... M`, representing the full shift as `N O P Q R ... Z A B C ... M`, and simply maps `A B C ... Z` to it.

---

## Level 12

Password: `7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4`

Lots of compressions.

**Solution:** Just keep decompressing with either `gzip -d` (filename must be `.gz`), `bzip2 -d`, or `tar -xf`.

---

## Level 13

Password: `FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn`

Initially, no password. Instead a private SSH key is used to login, belonging to bandit14 (given from bandit13).

**Solution:**

```bash
# Grab the private key using scp
scp -P 2220 bandit13@bandit.labs.overthewire.org:~/sshkey.private ~/.ssh

# You MUST use 700 permissions to use the private key
chmod 700 ~/.ssh/sshkey.private

# Login using the key
ssh bandit14@bandit.labs.overthewire.org -p 2220 -i ~/.ssh/sshkey.private
```

> The actual password was obtained by checking `/etc/bandit_pass/bandit14` after logging in as bandit14 using their key.

---

## Level 14

Password: `MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS`

**Solution:**

```bash
nc 127.0.0.1 30000
```

Run on the bandit14 level and send the password of the last level.

---

## Level 15

Password: `8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo`

**Solution:**

```bash
openssl s_client -connect 127.0.0.1:30001
```

> You use `openssl` since the service is running on SSL. `nc` doesn't know the TLS protocol to establish the encrypted session the other side expects.

---

## Level 16

Password: `kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx`

**Solution:**

```bash
nmap -sV -p31000-32000 127.0.0.1
```

Finds ports with SSL:

```
PORT      STATE SERVICE     VERSION
31046/tcp open  echo
31518/tcp open  ssl/echo
31691/tcp open  echo
31790/tcp open  ssl/unknown
31960/tcp open  echo
```

Then connect using openssl:

```bash
echo kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx | openssl s_client -connect 127.0.0.1:31790 -ign_eof
```

> You will be given a private RSA file, not a password. Log in as bandit17 and check `/etc/bandit_pass/bandit17` for the actual password.

---

## Level 17

Password: `EReVavePLFHtFlFsjn3hyzMlvSuSAcRD`

**Solution:**

```bash
diff passwords.new passwords.old
```

Password is in `passwords.new`.

---

## Level 18

Password: `x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO`

Someone edited the `.bashrc` file to log you out immediately. So we used `scp` to grab the file:

```bash
scp -P 2220 bandit18@bandit.labs.overthewire.org:~/readme goh.txt
```

---

## Level 19

Password: `cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8`

**Solution:**

```bash
./bandit20-do cat /etc/bandit_pass/bandit20
```

> The binary in bandit19's home directory had a setuid bit and was owned by bandit20, allowing us to run it with bandit20's privileges and cat the file from the folder where bandit19 is not allowed.

---

## Level 20

Password: `0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO`

The binary `./suconnect` reaches out to a localhost port specified in its arguments and expects a password match. Upon a match, it sends the password to the port.

**Solution:** Begin a `nc` listener on a port (like 65002) and tell the application to connect to it.

---

## Level 21

Password: `EeoULMCra2q0dSkYj561DX7s1CpBuOBt`

Check the `/etc/cron.d` folder for the cron job associated with bandit22. The cronjob copies bandit22's password to a readable `/tmp/` file. Simply read that file to get the flag.

---

## Level 22

Password: `tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q`

The user bandit23 also has a cronjob running where their password is copied into a temporary file, whose name is derived using an md5sum. Copy their program, run it inside `/tmp/`, change the target to `"bandit23"`, and execute it to see the filename it writes to. Then cat that file.

Cronjob running:

```bash
cat /etc/cron.d/cronjob_bandit23
# @reboot bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
# * * * * * bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
```

Contents of the program:

```bash
#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget
```

---

## Level 23

Password: `0Zf11ioIjMVN551jX3CmStKLYqjk54Ga`

Hardest by far.

Bandit24 owns a cronjob that runs `bandit24.sh`:

```bash
#!/bin/bash

shopt -s nullglob

myname=$(whoami)

cd /var/spool/"$myname"/foo || exit
echo "Executing and deleting all scripts in /var/spool/$myname/foo:"
for i in * .*;
do
    if [ "$i" != "." ] && [ "$i" != ".." ];
    then
        echo "Handling $i"
        owner="$(stat --format "%U" "./$i")"
        if [ "${owner}" = "bandit23" ] && [ -f "$i" ]; then
            timeout -s 9 60 "./$i"
        fi
        rm -rf "./$i"
    fi
done
```

Go into `/var/spool/bandit24/foo` and create a custom file with the following contents:

```bash
cp /etc/bandit_pass/bandit24 /tmp/fileofded.txt
```

Make sure the file has 777 permissions. Wait until a new minute begins — the cronjob (running with bandit24's permissions) will execute the file. Then read `fileofded.txt` for the password.

---

## Level 24

Password: `gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8`

Requires bruteforcing a 4-digit code + bandit24 password at port 30002. Following script works:

```bash
coproc NC { nc 127.0.0.1 30002; }

password="gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8"

for i in {0..9999}; do
    code=$(printf "%04d" "$i")

    echo "sending $password $code"

    # send newline-terminated input
    printf "%s %s\n" "$password" "$code" >&"${NC[1]}"

    # read one line of response
    IFS= read -r response <&"${NC[0]}"

    if [[ "$response" != *Wrong* ]]; then
        echo "Code: $code"
        echo "$response"
    fi
done
```

---

## Level 25

Password: `iCi86ttT4KSNe1armKiwbQNmB3YJP3q4`

A cheeky level. The private key for bandit26 is already provided in bandit25's home directory. However, bandit26's login shell is not `/bin/bash` — it's a strange file called `showtext`:

```
bandit26:x:11026:11026:bandit level 26:/home/bandit26:/usr/bin/showtext
```

Contents of `showtext`:

```bash
#!/bin/sh

export TERM=linux

exec more ~/text.txt
exit 0
```

The file uses `exec` to launch `more` on a `text.txt` file. If you log in as bandit26, a giant "BANDIT26" is displayed before kicking you out — because `more`'s pager only appears if the file can't be printed in one go.

**Solution:** Shrink your terminal to force pager mode:

```bash
stty rows 6 cols 50
```

Then:
1. Press `v` to enter vim from the pager
2. `:set shell=/bin/bash` — change vim's shell variable to bash (overrides the restricted shell)
3. `:shell` — pop open a full bash shell as bandit26

Chain of shells:
```
sshd
 └── login shell
      └── more
           └── vim
                └── bash
```

---

## Level 26

Password: `s0773xxkk0MXfdqOfPRVr9L3jJBUOgCZ`

From the bandit26 shell (previous level), there is a binary `bandit27-do` with a setuid bit owned by bandit27. Use it to cat bandit27's password:

```bash
./bandit27-do cat /etc/bandit_pass/bandit27
```

---

## Level 27

Password: `upsNCc7vzaRDx6oZC6GiR6ERwe1MowGB`

```bash
git clone ssh://bandit27-git@bandit.labs.overthewire.org:2220/home/bandit27-git/repo
```

Get the only file inside the cloned repo.

---

## Level 28

Password: `Yz9IpL0sBcCeuG7m9uQFt8ZNpS4HZRcN`

The password was accidentally pasted in a previous git commit.

```bash
git log          # see history
git show <commit> # check the second commit — it leaked the password
```

---

## Level 29

Password: `4pT1t5DENaYuqnqvadYs1oE4QLCdjmJ7`

Focus on git branches.

```bash
git branch -a      # see all branches (including remote)
git checkout dev   # password is on the dev branch
```

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

**Solution:** Use bash special variables — unlike environment variables, these are unique to the bash process itself:

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

## The End

```
Congratulations on solving the last level of this game!

At this moment, there are no more levels to play in this game. However, we are
constantly working on new levels and will most likely expand this game with more
levels soon. Keep an eye out for an announcement on our usual communication channels!
In the meantime, you could play some of our other wargames.

If you have an idea for an awesome new level, please let us know!
```
