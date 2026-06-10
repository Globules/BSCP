## Features to Enumerate

- Product category filter
- Login functionality
- Tracking cookie
- User lookup
- Stock check feature
- Oracle / Microsoft / PostgreSQL / MySQL behavior

---

## What to look for

- SQL error messages
- Boolean response differences
- Time delays
- `UNION SELECT`
- Number of columns
- String-compatible columns
- Database version
- Table and column names
- Hidden products
- Administrator password
- Blind SQL injection in cookies
- Out-of-band DNS interaction

## Common endpoint

- /filter?category=
- /login
- /product
- /product/stock
- /my-account

## Common parameters / cookies

- category
- username
- password
- TrackingId
- session
- productId
- storeId
