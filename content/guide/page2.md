# Bandit Guide — Levels 10–19

[Back to Guide Index](/content/ctfs/bandit.md)

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

[Previous: Levels 0–9](/content/guide/page1.md) | [Next: Levels 20–29](/content/guide/page3.md) | [Guide Index](/content/ctfs/bandit.md)
