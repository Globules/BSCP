## Labs tips — Server-Side Template Injection (SSTI)

Every PortSwigger SSTI lab follows the same core idea:

- user-controlled input is embedded into a server-side template
- you can inject template syntax depending on the engine
- initial goal is to confirm evaluation (math/string output change like `7*7 → 49`)
- error messages reveal the template engine (Django, Freemarker, Handlebars, ERB, Tornado, etc.)
- escalation leads to object access, then code execution
- final objective is often arbitrary command execution (e.g. deleting `/home/carlos/morale.txt`)

Common methodology:

- test with basic expressions depending on syntax:
  - `{{7*7}}`
  - `${7*7}`
  - `<%= 7*7 %>`
- trigger errors to identify the template engine
- read documentation for built-ins, debug tools, and unsafe features
- escape template context (`{{ }}`, `{% %}`, `${ }`, etc.)
- escalate using:
  - Python (`os.system`)
  - Node.js (`require('child_process').exec`)
  - Java (`Execute`, reflection, TemplateModel classes)
  - Freemarker (`?new()`, `Execute`)
  - Django (`settings`, `debug`)
  - Handlebars prototype / constructor abuse

## Link

### BSCP Academy

- [Server-side template injection](https://portswigger.net/web-security/server-side-template-injection)

### BSCP Labs (PortSwigger Web Security Academy)

- [Basic server-side template injection](https://portswigger.net/web-security/server-side-template-injection/exploiting/lab-server-side-template-injection-basic)
- [Basic server-side template injection (code context)](https://portswigger.net/web-security/server-side-template-injection/exploiting/lab-server-side-template-injection-basic-code-context)
- [Server-side template injection using documentation](https://portswigger.net/web-security/server-side-template-injection/exploiting/lab-server-side-template-injection-basic-documented-exploit)
- [Server-side template injection in an unknown language with a documented exploit](https://portswigger.net/web-security/server-side-template-injection/exploiting/lab-server-side-template-injection-basic-unknown-language)
- [Server-side template injection with information disclosure via user-supplied objects](https://portswigger.net/web-security/server-side-template-injection/exploiting/lab-server-side-template-injection-object-information-disclosure)

---

### Other resources

- [Server-Side Template Injection (SSTI) Explained](https://www.youtube.com/watch?v=2r3vZ8v0b5c)
- [PayloadsAllTheThings - SSTI](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Template%20Injection)
- [Django Template Injection Guide](https://www.youtube.com/watch?v=8C5f1G5c7Jk)