## Features to Enumerate

- JSON Web Token authentication
- Session token
- Admin panel
- Search page
- Comment feature
- JWT Editor keys

---

## What to look for

- JWT stored in `session` cookie
- JWT structure: `header.payload.signature`
- Base64URL-encoded header and payload
- `sub` claim containing username
- Missing or flawed signature verification
- `alg` parameter set to `none`
- Weak HMAC secret
- `jwk` header parameter
- `jku` header parameter
- `kid` header parameter
- `kid` path traversal to `/dev/null`

## Common endpoint

- /my-account
- /admin
- /admin/delete?username=carlos

## Cookies

- session

## Useful tools

- hashcat
