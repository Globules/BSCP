## Labs tips

Every lab from Insecure Deserialization has:

- Serialized objects transmitted between client and server (cookies, parameters, hidden fields)
- Use of encoding formats like Base64, PHP serialization, Java serialization, or custom formats
- Client-side or server-side deserialization of untrusted data
- Possibility to modify serialized data to change application logic
- Type juggling issues (e.g. strings interpreted as integers or booleans)
- Weak integrity protections (missing or flawed signatures / HMAC validation)
- Gadget chains enabling remote code execution or privilege escalation
- Language-specific exploitation patterns (PHP, Java, Ruby)
- Session cookies often contain serialized objects
- Burp Suite (Proxy, Repeater, Decoder) heavily used
- Frequent need to decode → modify → re-encode payloads
- Application functionality can sometimes be reused to trigger dangerous object behavior

## Link

### BSCP Academy

- [Insecure deserialization](https://portswigger.net/web-security/deserialization)

### BSCP Labs

- [Modifying serialized objects](https://portswigger.net/web-security/deserialization/exploiting/lab-deserialization-modifying-serialized-objects)
- [Modifying serialized data types](https://portswigger.net/web-security/deserialization/exploiting/lab-deserialization-modifying-serialized-data-types)
- [Using application functionality to exploit insecure deserialization](https://portswigger.net/web-security/deserialization/exploiting/lab-deserialization-using-application-functionality-to-exploit-insecure-deserialization)
- [Arbitrary object injection in PHP](https://portswigger.net/web-security/deserialization/exploiting/lab-deserialization-arbitrary-object-injection-in-php)
- [Exploiting Java deserialization with Apache Commons](https://portswigger.net/web-security/deserialization/exploiting/lab-deserialization-exploiting-java-deserialization-with-apache-commons)
- [Exploiting PHP deserialization with a pre-built gadget chain](https://portswigger.net/web-security/deserialization/exploiting/lab-deserialization-exploiting-php-deserialization-with-a-pre-built-gadget-chain)
- [Exploiting Ruby deserialization using a documented gadget chain](https://portswigger.net/web-security/deserialization/exploiting/lab-deserialization-exploiting-ruby-deserialization-using-a-documented-gadget-chain)


### Other resources

- [Insecure deserialization overview](https://portswigger.net/web-security/deserialization)
- [OWASP Deserialization vulnerabilities](https://owasp.org/www-community/vulnerabilities/Deserialization_of_untrusted_data)
- [Java deserialization explained](https://www.youtube.com/watch?v=example1)
- [PHP object injection concepts](https://www.youtube.com/watch?v=example2)
- [Gadget chains explained](https://www.youtube.com/watch?v=example3)