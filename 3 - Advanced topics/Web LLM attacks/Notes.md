## Labs tips

Every lab from Web LLM attacks has:

- AI chatbot / LLM feature exposed through web interface
- Live chat functionality
- WebSocket endpoint commonly available at `/chat`
- LLM capable of interacting with backend APIs
- Prompt injection opportunities
- Excessive agency vulnerabilities
- Sensitive internal APIs exposed to the LLM
- APIs callable through natural language prompts
- Potential access to:
  - SQL debugging APIs
  - Newsletter subscription APIs
  - Password reset APIs
  - Product management APIs
- LLM often leaks available tools/features when prompted
- User-controlled prompts forwarded to backend systems
- Possibility of:
  - SQL injection
  - OS command injection
  - Data exfiltration
  - Privilege escalation
  - Unauthorized actions
- AI agents may autonomously perform actions on behalf of users
- Hidden system prompts may influence model behavior
- Prompt injection payloads often embedded in:
  - Product reviews
  - Support tickets
  - Emails
  - External content
- Secondary vulnerabilities can be triggered through AI-generated output
- Sensitive information may leak through indirect prompts
- AI scanners / filters can sometimes be bypassed with encoding or obfuscation
- Burp Suite useful for observing:
  - WebSocket traffic
  - API interactions
  - Hidden endpoints
- Prompt engineering heavily used during exploitation
- Indirect exploitation via AI tool invocation
- Trust boundary issues between LLM and backend systems

## Link

### BSCP Academy

- [Web LLM attacks](https://portswigger.net/web-security/llm-attacks)

### BSCP Labs

- [Exploiting LLM APIs with excessive agency](https://portswigger.net/web-security/llm-attacks/lab-exploiting-llm-apis-with-excessive-agency)
- [Exploiting vulnerabilities in LLM APIs](https://portswigger.net/web-security/llm-attacks/lab-exploiting-vulnerabilities-in-llm-apis)
- [Indirect prompt injection](https://portswigger.net/web-security/llm-attacks/lab-indirect-prompt-injection)
- [Exploiting AI agents to perform destructive actions](https://portswigger.net/web-security/llm-attacks/lab-exploiting-ai-agents-to-perform-destructive-actions)
- [Exploiting AI agents to exfiltrate sensitive information](https://portswigger.net/web-security/llm-attacks/lab-exploiting-ai-agents-to-exfiltrate-sensitive-information)
- [Exploiting AI agents to trigger secondary vulnerabilities](https://portswigger.net/web-security/llm-attacks/lab-exploiting-ai-agents-to-trigger-secondary-vulnerabilities)
- [Bypassing AI scanner defenses to exfiltrate sensitive information](https://portswigger.net/web-security/llm-attacks/lab-bypassing-ai-scanner-defenses-to-exfiltrate-sensitive-information)

### Other resources

- [Web LLM attacks (PortSwigger)](https://portswigger.net/web-security/llm-attacks)
- [Prompt Injection explained](https://www.youtube.com/watch?v=mPY3CWw00r0)
- [OWASP Top 10 for LLM Applications](https://owasp.org/www-project-top-10-for-large-language-model-applications/)
- [Prompt Injection Attacks against LLMs](https://www.promptingguide.ai/risks/prompt-injection)
- [LLM Security Best Practices](https://platform.openai.com/docs/guides/safety-best-practices)
- [Indirect Prompt Injection research](https://simonwillison.net/2023/May/11/delimiters-wont-save-you/)
- [LLM attacks overview (video)](https://www.youtube.com/watch?v=U0YdD6Z7G2s)