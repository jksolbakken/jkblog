---
title: "Trusted types"
date: 2022-08-30T19:46:56+02:00
tags: ["security", "web"]
draft: false
---

The latest addition to the XSS protection landscape is [Trusted Types](https://w3c.github.io/webappsec-trusted-types/dist/spec/), "an API that allows applications to lock down powerful APIs to only accept non-spoofable, typed values in place of strings to prevent vulnerabilities caused by using these APIs with attacker-controlled inputs".

Despite having gained a lot of attention for years, [Cross Site Scripting (XSS)](https://owasp.org/www-community/attacks/xss/) remains one of the most common types of vulnerabilities on the web. [Content Security Policies](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP) and automatic sanitization of outputs in frameworks like React and Vue are all powerful mechanisms, but none of them solve the problem completely. If you are going commando without the big frameworks you need to manually ensure proper sanitization of output to all unsafe DOM sinks, forget one and you are vulnerable. If one of the third party libraries you use have bugs and/or are compromised your CSP won't save you because you're still loading the code from trusted locations.

A trusted type is a function that all strings are run through before being passed to unsafe DOM sinks. Their behavior is defined by directives in your Content Security Policies:

```bash
Content-Security-Policy: require-trusted-types-for 'script'
```

With this policy in place, every time a script wants to write to an unsafe sink it passed through a trusted type named `default`. This type is responsible for sanitizing the input so that it is safe to use. The example below uses [DOMPurify](https://github.com/cure53/DOMPurify).

```javascript
// load/import DOMPurify 

if (window.trustedTypes && trustedTypes.createPolicy) {
   trustedTypes.createPolicy('default', {
      createHTML: DOMPurify.sanitize(string, {RETURN_TRUSTED_TYPE: true})
   });
}
```

If you want to use multiple trusted types for different parts of your pages you can specify those in the CSP directive:

```bash
Content-Security-Policy: require-trusted-types-for 'script'
Content-Security-Policy: trusted-types foo bar 'allow-duplicates'
```

Policy names may consist of alphanumeric characters and/or the special characters `-#=_/@.%`. 

The `allow duplicate` directive allows multiple policies with the same name.

Creating and using named policies is done like this:

```javascript
if (window.trustedTypes && trustedTypes.createPolicy) {
   const policyFoo = trustedTypes.createPolicy('foo', { /* sanitizing here */ })
   const policyBar = trustedTypes.createPolicy('bar', { /* sanitizing here */ })

   myFirstDiv.innerHTML = policyFoo.createHTML(data)
   myOtherDiv.innerHTML = policyBar.createHTML(data)
}
```

Now, if an attacker attempts to write strings directly to an unsafe sink the browser will throw an error like the one shown below.

![TypeError](/img/typeerror.png)

At the time of writing Trusted Types are [supported](https://caniuse.com/?search=trusted%20types) in Chrome, Edge and Opera.
