## Features to Enumerate

- OAuth login workflow
- OAuth provider
- Redirect URI
- Authorization endpoint
- Token exchange
- Client ID
- State parameter
- Redirect handling
- Account linking
- Exploit Server

---

## What to look for

- Missing `state` validation
- Open redirect
- Leaked OAuth token
- Dynamic client registration
- Weak redirect URI validation
- Access token exposure
- CSRF in OAuth flow

## Common endpoint

- /auth
- /oauth-callback
- /login
- /authenticate
- /callback
