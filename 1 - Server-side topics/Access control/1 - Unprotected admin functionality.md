## Unprotected admin functionality

This lab has an unprotected admin panel.

Solve the lab by deleting the user carlos.

## Notes

- robots.txt

## Resume

1. Find the `robots.txt`
2. Access the admin panel
3. Delete Carlos's account


## Solve 

First step is to retrieve the `robots.txt` to find the admin panel : 

robots.txt
```
User-agent: *
Disallow: /administrator-panel
```

Access the `/administrator-panel` and delete Carlos's account