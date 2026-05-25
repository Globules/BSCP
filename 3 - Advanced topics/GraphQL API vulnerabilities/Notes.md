## Labs tips

Every lab from GraphQL API vulnerabilities has:

- A GraphQL endpoint exposed over HTTP (often `/api`, `/graphql`, or variant paths)
- Queries and mutations sent via GET or POST requests
- A schema that can often be partially or fully enumerated
- Possibility of introspection being enabled, blocked, or weakly filtered
- Input variables passed either inline or via JSON `variables`
- Common operations like `getUser`, `getBlogPost`, or `delete*` mutations
- Weak access control between queries and mutations
- Client-side or server-side validation inconsistencies
- Burp Suite (Proxy, Repeater, GraphQL tab) heavily used
- Site map GraphQL extraction useful for schema discovery

## Link

### BSCP Academy

- [GraphQL Security](https://portswigger.net/web-security/graphql)

### BSCP Labs

- [Accessing private GraphQL posts](https://portswigger.net/web-security/graphql/lab-apprentice-accessing-private-graphql-posts)
- [Accidental exposure of private GraphQL fields](https://portswigger.net/web-security/graphql/lab-accidental-exposure-of-private-graphql-fields)
- [Finding a hidden GraphQL endpoint](https://portswigger.net/web-security/graphql/lab-find-hidden-graphql-endpoint)
- [Bypassing GraphQL brute force protections](https://portswigger.net/web-security/graphql/lab-bypassing-graphql-brute-force-protections)
- [Performing CSRF exploits over GraphQL](https://portswigger.net/web-security/graphql/lab-csrf-exploits-over-graphql)

### Other resources

- [GraphQL cheat sheet](https://portswigger.net/web-security/graphql/cheat-sheet)
- [OWASP GraphQL Security](https://owasp.org/www-project-top-ten/)
- [GraphQL explained (video)](https://www.youtube.com/watch?v=meH7i4g9h2Q)
- [GraphQL security overview (video)](https://www.youtube.com/watch?v=YCIctC-4zw0)
- [GraphQL attack surface (video)](https://www.youtube.com/watch?v=Q8Tz5V9G3pE)
