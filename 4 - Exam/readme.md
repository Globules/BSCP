# BSCP Exam Overview

## What the Exam Involves

To take the exam, you must first log in to your PortSwigger user account.

You will find a button labeled **"Take exam"**. Clicking this button starts the official exam process. After successfully completing the automated proctoring session, the exam environment will become available.

You will have **4 hours** to complete the **Burp Suite Certified Practitioner (BSCP)** exam.

---

## Exam Structure

The exam contains **two vulnerable web applications**.

Each application can be completed in **three stages**:

### Stage 1 — Access Any User Account

Gain access to any valid user account within the application.

---

### Stage 2 — Access the Admin Interface

Use your user account to gain access to the administrator interface.

This may involve:

- Privilege escalation
- Account compromise
- Authentication bypass
- Session attacks
- Business logic flaws

---

### Stage 3 — Read the Secret File

Using the admin interface, retrieve the contents of:

```txt
/home/carlos/secret
```

Then submit the secret using the application's:

```txt
submit solution
```

functionality.

---

## Important Notes

### Dangerous Functionality

While exploiting the applications, you may gain access to powerful functionality.

Be careful not to:

- Delete your own account
- Delete critical application data
- Break core system functionality

Doing so may make the exam impossible to complete.

---

### Default Accounts

Every application contains:

- An administrator account:

```txt
administrator
```

- A lower-privileged account, usually:

```txt
carlos
```

If you discover a username enumeration vulnerability, you may be able to brute-force credentials using the provided username and password lists.

---

## Active Victim Behavior

Each application contains **up to one active user**.

This user may be logged in as either:

- A normal user
- An administrator

You can assume that the victim:

- Visits the homepage every 15 seconds
- Clicks links in emails received from the application

This behavior allows exploitation of:

- Reflected XSS
- CSRF
- Stored XSS
- Open redirects
- Password reset poisoning
- Email-based attacks

You can use the **Exploit Server** and its:

```txt
Send to victim
```

feature to target them.

---

## SSRF Behavior

If you discover an SSRF vulnerability, you can access an internal-only service running on:

```txt
localhost:6566
```

This can be used to read local files and interact with internal functionality.

---

## Host Header Attacks

Host header attacks are allowed during the exam.

However, do **not** tamper with the following cookies:

```txt
_lab
_lab_analytics
```

These cookies are part of the core exam infrastructure and are not intended targets.

---

## Skills Required

To understand the expected skill level and covered vulnerability classes, refer to the official:

- Prepare for the exam page
- Web Security Academy labs
- Burp Suite Academy methodology

The exam heavily focuses on:

- Authentication vulnerabilities
- Access control
- XSS
- CSRF
- SSRF
- SQL injection
- Request smuggling
- Business logic flaws
- Deserialization
- File upload
- Host header attacks
- Clickjacking
- Web cache poisoning
- Server-side template injection
- XXE
- DOM vulnerabilities

---

## Goal

Your objective is to fully compromise both applications by reaching Stage 3 and successfully submitting the secrets within the allotted time.