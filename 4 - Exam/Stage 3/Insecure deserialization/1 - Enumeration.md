## Features to Enumerate

- Serialized cookies
- Session cookies
- User-controlled objects
- Deserialization sinks
- Gadget chains
- Exploit Server

---

## What to look for

- Base64 serialized objects
- PHP serialization
- Java serialization
- Ruby Marshal
- Modified object properties
- Magic methods
  - `__wakeup`
  - `__destruct`
  - `readObject`
- Signed objects
- Weak integrity checks
- ysoserial gadget chains
- Arbitrary file deletion
- Command execution

## Common endpoint

- /login
- /my-account
- /admin
- /logout

## Common cookies

- session
- stay-logged-in

## Useful tools

- ysoserial
- phpggc



