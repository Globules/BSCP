## Labs tips

Every lab from SQL injection has:

- parameters in GET or POST requests (e.g. `productId`, `category`, `username`)
- database-backed functionality (login, search, filtering)
- visible or blind interaction with the database
- error messages, boolean responses, or timing differences
- payloads injected using `'`, `"`, `--`, `#`, `/* */`

Common exploitation paths:

- authentication bypass (`' OR 1=1--`)
- UNION-based extraction
- error-based data leakage
- blind SQL injection (boolean or time-based)
- out-of-band (OAST) exfiltration

## Link

### BSCP Academy

- [SQL injection](https://portswigger.net/web-security/sql-injection)

### BSCP Labs

- [SQL injection vulnerability in WHERE clause allowing retrieval of hidden data](https://portswigger.net/web-security/sql-injection/lab-retrieve-hidden-data)
- [SQL injection vulnerability allowing login bypass](https://portswigger.net/web-security/sql-injection/lab-login-bypass)

#### UNION attacks

- [SQL injection UNION attack, determining the number of columns returned](https://portswigger.net/web-security/sql-injection/union-attacks/lab-determine-number-of-columns)
- [SQL injection UNION attack, finding a column containing text](https://portswigger.net/web-security/sql-injection/union-attacks/lab-find-column-containing-text)
- [SQL injection UNION attack, retrieving data from other tables](https://portswigger.net/web-security/sql-injection/union-attacks/lab-retrieve-data-from-other-tables)
- [SQL injection UNION attack, retrieving multiple values in a single column](https://portswigger.net/web-security/sql-injection/union-attacks/lab-retrieve-multiple-values-in-single-column)

#### Blind SQL injection

- [Blind SQL injection with conditional responses](https://portswigger.net/web-security/sql-injection/blind/lab-conditional-responses)
- [Blind SQL injection with conditional errors](https://portswigger.net/web-security/sql-injection/blind/lab-conditional-errors)
- [Visible error-based SQL injection](https://portswigger.net/web-security/sql-injection/blind/lab-visible-error-based)
- [Blind SQL injection with time delays](https://portswigger.net/web-security/sql-injection/blind/lab-time-delays)
- [Blind SQL injection with time delays and information retrieval](https://portswigger.net/web-security/sql-injection/blind/lab-time-delays-info-retrieval)

#### Out-of-band (OAST)

- [Blind SQL injection with out-of-band interaction](https://portswigger.net/web-security/sql-injection/blind/lab-out-of-band)
- [Blind SQL injection with out-of-band data exfiltration](https://portswigger.net/web-security/sql-injection/blind/lab-out-of-band-data-exfiltration)

#### Filter bypass / advanced

- [SQL injection with filter bypass via XML encoding](https://portswigger.net/web-security/sql-injection/lab-sql-injection-with-filter-bypass-via-xml-encoding)
- [SQL injection attack, querying the database type and version](https://portswigger.net/web-security/sql-injection/examining-the-database/lab-querying-database-version)
- [SQL injection attack, listing the database contents on non-Oracle databases](https://portswigger.net/web-security/sql-injection/examining-the-database/lab-listing-database-contents)
- [SQL injection attack, listing the database contents on Oracle](https://portswigger.net/web-security/sql-injection/examining-the-database/lab-listing-database-contents-oracle)

### Other resources

- [SQL Injection Explained](https://www.youtube.com/watch?v=ciNHn38EyRc)
- [SQL Injection Tutorial](https://www.youtube.com/watch?v=2OPVViV-GQk)
- [SQL injection cheat sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)