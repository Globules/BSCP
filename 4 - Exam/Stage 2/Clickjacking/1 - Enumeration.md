## Features to Enumerate

- Login functionality
- Account page
- Account deletion
- Email update
- Feedback feature
- Exploit Server

---

## What to look for

- No `X-Frame-Options`
- No `Content-Security-Policy: frame-ancestors`
- Frame buster protection
- URL parameter prefill
- `feedbackResult.innerHTML`
- `name` parameter
- Confirmation dialog
- Multi-step UI interaction

## Common endpoint

- /login
- /my-account
- /feedback
- /resources/js/submitFeedback.js

## Common Headers

- X-Frame-Options
- Content-Security-Policy: frame-ancestors
