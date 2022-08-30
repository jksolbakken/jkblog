---
title: "Trusted types"
date: 2022-08-30T19:46:56+02:00
draft: true
---

The latest addition to the XSS protection landscape is [Trusted Types](https://w3c.github.io/webappsec-trusted-types/dist/spec/), "an API that allows applications to lock down powerful APIs to only accept non-spoofable, typed values in place of strings to prevent vulnerabilities caused by using these APIs with attacker-controlled inputs".

Despite having gained a lot of attention for years, [Cross Site Scripting (XSS)](https://owasp.org/www-community/attacks/xss/) continues to be one of the most common type of vulnerabilities on the web. [Content Security Policies](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP) and automatic sanitization of outputs in frameworks like React and Vue are all powerful mechanisms, but none of them solve the problem completely. If you are going commando without the big frameworks you need to manually ensure proper sanitization of output to all unsafe DOM sinks, forget one and you are vulnerable. If one the third party libraries you use have bugs and/or are compromised your CSP won't save you because you're still loading the code from trusted locations.

A trusted type is a function that all strings are run through before being passed to unsafe sinks. Their behavior is defined by directives in your Content Security Policies.

