# Conditional Constellation

**Skills:** Bruteforcing, threading, python scripting, tokens

When visiting the IP address, you are greeted with a screen "purchase a new starship" with a button (at /)
Clicking on the button makes a GET request to the /api/v1/session endpoint.

If you intercept this request, you see that in response (in Burpsuite), you are given a session token, which your browser now stored.

After this you are immediately dropped to a keypad where you must enter a 4 digit code within 90 seconds. The JS loaded into the browser loads this
in the same location as "/" (SPA-stuff)

When you type a code, the code itself is attached in the body of the request as a simple 4-digit number, separate from the headers. This is sent to the
/api/v1/pin/attempt endpoint via a POST request.

Bruteforcing is hinted.

Of course, bruteforcing requires a script, and to reach the "code-entering" page, you require a session token that is only valid for a certain time.
Therefore, I logged in using my browser, extracted the token that was given to me, and quickly pasted it into my python bruteforce script.
As time was limited and there were 10000 possibilities to consider, I used 100 threads to help me out.

This drastically decreased the time taken to try out each code and gave the flag for code 8596:

```
NexusCTF{Swipe2unl0ckPls}
```

> NOTE: it appears that every time the session token changes, so does the number which gives the flag. The same number is therefore unlikely to work twice.

**Rating: 9/10**

---

[Next: Interstellar Ingress](/content/ctfs/quantum/interstellar-ingress.md) | [Back to Quantum](/content/ctfs/quantum.md)
