# Conditional constellation:

Skills: Bruteforcing, threading, python scripting, tokens

When visiting the IP address, you are greeted with a screen "purchase a new starship" with a button (at /)
Clicking on the button makes a GET request to the /api/v1/session endpoint.

If you interecept this request, you see that in response (in Burpsuite), you are given a session token, which your browser now stored.

After this you are immediately dropped to a keypad where you must enter a 4 digit code within 90 seconds. The JS loaded into the browser loads this
in the same location as "/" (SPA-stuff)

When you type a code, the code itself is attached in the body of the request as a simple 4-digit number, separate from the headers. This is sent to the
/api/v1/pin/attempt endpoint via a POST request.

Bruteforcing is hinted.

Ofcourse, bruteforcing requires a script, and to reach the "code-entering" page, you require a session token that is only valid for a certain time.
Therefore, I logged in using my browser, extracted the token that was given to me, and quickly pasted it into my python bruteforce script.
As time was limited and there were 10000 possibilities to consider, I used 100 threads to help me out.

This drastically decreased the time taken to try out each code and gave the flag for code 8596:
NexusCTF{Swipe2unl0ckPls}

NOTE: it appears that everytime the session token changes, so does the number which gives the flag. The same number is therefore unlikely to work twice.

9/10

# Interstellar Ingress:

Skills: JWT knowledge and header attacks

When you visit the website, there is a "login as guest" button. Clicking it calls the /guest-login POST endpoint.
This request sends our old cookie for the website, and in return, gets the new guest token. This is a JWT. The web request is:


GET / HTTP/1.1<br>
Host: 34.129.100.231:5059 <br>
Cache-Control: max-age=0<br>
Accept-Language: en-US,en;q=0.9<br>
Upgrade-Insecure-Requests: 1<br>
User-Agent: Mozilla/5.0 (X11; Linux x86_64) 
AppleWebKit/537.36 (KHTML, like Gecko) Chrome/133.0.0.0 Safari/537.36<br>
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7<br>
Referer: http://34.129.100.231:5059/login
Accept-Encoding: gzip, deflate, br<br>
Cookie: interstellar_ingress_session_token=eyJhbGciOiJIUzI1NiJ9.eyJpc19hZG1pbiI6ZmFsc2UsImlhdCI6MTc1OTIyMzI5Nn0.aOmEBfobHH4aSaqNRZwvXxZruokDP3QRVacsZVlPINo<br>
If-None-Match: "grrli6"<br>
Connection: keep-alive<br>

the JWT given is eyJhbGciOiJIUzI1NiJ9.eyJpc19hZG1pbiI6ZmFsc2UsImlhdCI6MTc1OTIyMzI5Nn0.aOmEBfobHH4aSaqNRZwvXxZruokDP3QRVacsZVlPINo
This token has 3 parts separated by the .'s. First 2 are encoded in base64 and are: the "header" (containing metadata such as the algo to use for signing), the "payload" (containing data making claims about the user), and lastly the signature (to confirm if the JWT is valid, created through a key).

The JWT above translates to:
{"alg":"HS256"}{"is_admin":false,"iat":1759223296}aOmEBfobHH4aSaqNRZwvXxZruokDP3QRVacsZVlPINo

As we don't have the secret key, we cannot forge the signature, but the basic attack pattern here would be to change the "is_admin" field to "true", and getting the server to accept it.

The attack we use to circumvent this is to change the "alg" field in the header to "none". This can sometimes lead to the server taking in "insecure" JWT's with no signatures at all. And this is exactly the exploit.

Encode the following:
{"alg":"none"}{"is_admin":true,"iat":1759223296} -> eyJhbGciOiJub25lIn0.eyJpc19hZG1pbiI6dHJ1ZSwiaWF0IjoxNzU5MjIzMjk2fQ. (remove the equals and add dots where they need to be)
and next time you login, in Burp, change the token field to this. The flag is:
![alt text](image.png)


# Star Maps:

Skills: Dangerously set inner HTML maps

In this challenge, looking at the files already given to you are key. When your accesses a webpage, all the necessary .js files are downloaded.
(Found in Inspect -> sources with a tree of files thereafter)
The files downloaded (especially the js ones) are minified where variable names are shortened to 1 char, and newlines are removed; practically unreadable.
The files are also bundled so that multiple source files are brought together into one.

Looking at one of the Routes/ files, it can be seen how the "//# sourceMappingURL=1.C2QAL_4o.js.map" is written. This indicates that mappings are in place, and a minimised function can be mapped. These mappings are found in the /src files:

Within the src/ folder, I simply found a .svelte file that looked to be displayed to the browser:
![alt text](image-1.png)

I saw that variables were being imported from "encryption.ts". This file was the gold mine.
![alt text](image-2.png)
It showed how it concatanated a "secret code", "magic word", and "flag seed" into a base64 string. Finding the missing 2 was fairly easy.
It was in the HTML code of the webpage:
![alt text](image-3.png)
"hubble" and "sunflower"
Combine them separated by a ":", then base64 encode, slice the 2 chars (paste the code in encyption.js into browser console), and reverse it. When sent via the input field, the output will be the flag:

NexusCTF{dangerouslySetInnerHTML_sourcemaps_are_amazing}

Note: the source maps themselves allow the minimised js code to be mapped back to its orignal source code. However I didn't get to use them.

[Back to CTF List](/content/ctfs.md)