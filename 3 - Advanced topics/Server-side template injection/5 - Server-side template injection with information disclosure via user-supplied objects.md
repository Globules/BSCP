## Server-side template injection with information disclosure via user-supplied objects

This lab is vulnerable to server-side template injection due to the way an object is being passed into the template. This vulnerability can be exploited to access sensitive data.

To solve the lab, steal and submit the framework's secret key.

You can log in to your own account using the following credentials:

content-manager:C0nt3ntM4n4g3r

## Notes

- /login
- Edit content feature

## Resume

1. Identify SSTI
2. Trigger template parsing errors
3. Fingerprint the template engine
4. Use Django debug template tag
5. Enumerate available objects
6. Access `settings`
7. Extract `SECRET_KEY`

## Solve

Go to any product page and use the **edit template** feature.

To test for SSTI, inject a simple expression:

```django
{{7*7}}
```

After saving the template, the application returns an error:

```text
Internal Server Error
Traceback (most recent call last): File "<string>", line 11, in <module> File "/usr/local/lib/python2.7/dist-packages/django/template/base.py", line 191, in __init__ self.nodelist = self.compile_nodelist() File "/usr/local/lib/python2.7/dist-packages/django/template/base.py", line 230, in compile_nodelist return parser.parse() File "/usr/local/lib/python2.7/dist-packages/django/template/base.py", line 486, in parse raise self.error(token, e) django.template.exceptions.TemplateSyntaxError: Could not parse the remainder: '*7' from '7*7'
```

The most important part is:

```text
django.template.exceptions.TemplateSyntaxError
```

which confirms that the application uses the **Django template engine**.

The error also reveals that Django attempted to parse:

```django
{{7*7}}
```

as a template expression.

Next, research Django template functionality.

The Django documentation describes the built-in template tag:

```django
{% debug %}
```

which dumps all available variables and objects accessible from the template context.

Replace the previous payload with:

```django
{% debug %}
```

After saving the template, the page outputs a large amount of debugging information containing accessible objects and variables.

Inside the debug output, notice the presence of:

```python
settings
```

The Django `settings` object contains sensitive framework configuration values, including:

```python
SECRET_KEY
```

The Django secret key is security-sensitive because it is used for:

- Session signing
- CSRF token generation
- Password reset tokens
- Cryptographic signing

To retrieve the secret key, replace the debug payload with:

```django
{{settings.SECRET_KEY}}
```

The template engine evaluates the expression and renders the application's secret key directly inside the page.

The application effectively executes:

```python
settings.SECRET_KEY
```

which discloses the framework secret key and solves the lab.

Final payload:

```django
{{settings.SECRET_KEY}}
```