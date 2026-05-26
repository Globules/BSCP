## How to exploit the HTTP Host header

Once you have identified that you can pass arbitrary hostnames to the target application, you can start looking for ways to exploit it.

In this section, we’ll cover some common HTTP Host header attacks that you may be able to construct. We’ll also look at how these attacks work in practice and how they can be adapted to different application behaviors.

We’ll cover the following examples:
- Password reset poisoning.
- Web cache poisoning.
- Exploiting classic server-side vulnerabilities.
- Bypassing authentication.
- Virtual host brute-forcing.
- Routing-based SSRF.

---

## Password reset poisoning

Attackers can sometimes use the Host header in password reset poisoning attacks.

Password reset poisoning is a technique where an attacker manipulates a vulnerable website into generating a password reset link that points to a domain under their control. This can be used to steal the secret token required to reset a user’s password and ultimately take over the account.

### How a password reset works

Many websites that require a login also provide password reset functionality. A typical flow looks like this:

1. The user enters their username or email address and submits a password reset request.
2. The website checks that the user exists and generates a temporary, unique, high-entropy token associated with that account on the back end.
3. The website sends an email containing a password reset link with the token as a query parameter.
4. The user visits the link, the site validates the token, and then allows the user to choose a new password.
5. The token is destroyed after use.

This approach is relatively secure, but its security depends on the assumption that only the intended user can access the email inbox containing the token.

### How to construct the attack

If the URL sent to the user is built dynamically from user-controlled input such as the Host header, it may be possible to poison the reset link:

1. The attacker submits a password reset request for the victim.
2. The attacker intercepts the request and changes the Host header to a domain they control.
3. The victim receives a legitimate reset email, but the reset link points to the attacker’s domain.
4. If the victim clicks the link, the reset token is delivered to the attacker’s server.
5. The attacker uses the stolen token on the real site to reset the victim’s password.

In some cases, even if the reset link itself cannot be controlled, the Host header may still be used to inject HTML into sensitive emails. Email clients usually do not execute JavaScript, but other techniques such as dangling markup attacks may still apply.

---

## Web cache poisoning

When probing Host header behavior, you may find reflected input that is not immediately exploitable. For example, the Host header might be reflected into the HTML response without encoding, or used in script imports.

Reflected client-side issues are often not directly exploitable through the Host header alone, because an attacker cannot usually force a victim’s browser to send a useful custom host value.

However, if the application uses a web cache, a reflected issue can sometimes be transformed into a stored one by poisoning the cached response so that it is served to other users.

### How the attack works

To build a web cache poisoning attack, you need to:

1. Trigger a response that reflects attacker-controlled input.
2. Preserve a cache key that will be shared by other users.
3. Cause the malicious response to be cached.
4. Wait for other users to receive the poisoned response.

Standalone caches often include the Host header in the cache key, which makes this harder. This tends to work better on application-level caches, though there are exceptions depending on the architecture.

---

## Exploiting classic server-side vulnerabilities

Any HTTP header can sometimes be used to trigger classic server-side vulnerabilities, and the Host header is no exception.

For example, you should try standard SQL injection techniques through the Host header. If the value is passed into a SQL query, the result may be exploitable.

The same idea applies to other server-side bug classes where user input is concatenated into backend logic without proper validation.

---

## Bypassing authentication

It is common for websites to restrict certain functionality to internal users only.

However, some access-control mechanisms make unsafe assumptions that can be bypassed by modifying the Host header. In these cases, simply changing the host value may expose restricted functionality and significantly expand the attack surface.

This can also expose hidden functionality that is normally unavailable to regular users.

---

## Virtual host brute-forcing

Companies sometimes host public websites and private internal sites on the same server.

In that situation, the internal hostname may resolve to a private IP address and may not even be visible in public DNS. Even so, an attacker can often still reach virtual hosts on the same server if they can guess the hostname.

If the hidden domain is not obvious, it may be possible to brute-force likely subdomains using a wordlist of candidate hostnames.

---

## Routing-based SSRF

In some environments, the Host header can be used to trigger high-impact routing-based SSRF attacks.

These attacks occur when intermediary components such as load balancers or reverse proxies route requests based on an unvalidated Host header. If this is misconfigured, an attacker may be able to route traffic to an arbitrary system.

These systems are especially valuable targets because they sit in a privileged network position. They can receive public requests while also having access to internal-only systems, which makes them a potential gateway into the internal network.

A useful way to detect this behavior is to supply a domain you control in the Host header and observe whether the target makes a DNS lookup or HTTP request to it. If it does, this suggests the Host header is influencing routing.

Once that is confirmed, the next step is to see whether internal systems can be reached in the same way. This may involve identifying private IP addresses in use on the internal network or brute-forcing common private ranges such as `192.168.0.0/16`.

### CIDR notation

IP ranges are often expressed using CIDR notation, such as `192.168.0.0/16`.

IPv4 addresses consist of four 8-bit decimal values called octets, each separated by a dot. Each octet can range from `0` to `255`, so the full IPv4 space ranges from `0.0.0.0` to `255.255.255.255`.

In CIDR notation, the first address is written explicitly, followed by a slash and a number indicating how many leading bits are fixed across the range.

For example:
- `10.0.0.0/8` means the first 8 bits are fixed.
- This covers addresses from `10.0.0.0` to `10.255.255.255`.

---

## Connection state attacks

For performance reasons, many websites reuse connections for multiple request/response cycles with the same client.

Poorly implemented servers sometimes assume that properties such as the Host header remain identical for all HTTP/1.1 requests sent over the same connection. That may be true for normal browser traffic, but it is not necessarily true for requests sent manually from an interception tool.

This can create opportunities where the server only validates the first request on a connection. In that case, you may be able to send a harmless initial request and then follow it with a malicious one on the same connection.

Many reverse proxies also use the Host header to route requests to the correct back end. If they assume every request on a connection is meant for the same host as the first one, that can become a useful primitive for attacks such as routing-based SSRF, password reset poisoning, and cache poisoning.

---

## SSRF via a malformed request line

Some custom proxies fail to validate the request line properly, which can produce unexpected routing behavior.

For example, a reverse proxy may take the path from the request line, prepend it with a backend server URL, and forward the result upstream. This works normally if the path begins with `/`, but unusual characters at the start of the path can change how the backend interprets the request.

For instance, a path beginning with `@` can cause the upstream URL to be parsed in a way that targets a different host than intended.

This kind of flaw can be dangerous when the proxy assumes the request line is always well formed.