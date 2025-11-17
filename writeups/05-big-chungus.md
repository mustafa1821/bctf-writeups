# Big Chungus / Type Confusion in Query Parsing

**Category:** Web Exploitation

## Overview

The Big Chungus challenge initially appeared to be a humorous, lightweight web puzzle, but beneath the meme aesthetic was a subtle and instructive bug involving JavaScript type confusion, Express.js query parsing, and unsafe assumptions about user input types.

The server contained a conditional check that attempted to validate a username's length. However, it made one critical mistake: it assumed `username` was always a string. By exploiting how Express parses nested query parameters, it was possible to turn `username` into a user-controlled object with a fake `length` property. This bypassed the length check entirely and triggered the flag-printing branch.

This was a perfect introduction to why type-checking matters in JavaScript and why query parameters must never be trusted without validation.

## Initial Attempts & Misleading Intuition

At first glance, the challenge description and UI suggested a simple input-based puzzle, maybe something hidden behind a username field or a toggled page state. The initial guess was that adding any `username` parameter (e.g., `?username=chungus`) might reveal the flag.

But the page didn't behave as expected, and inspecting the live site did not show anything in the client code responsible for flag printing. This hinted the vulnerability was deeper in the server-side JavaScript, not visible from browser source alone.

Claude mentioned a vulnerability at line 53 in `index.js`, which prompted us to examine the challenge files directly. Once we reviewed the provided source in the write-up material, the real issue became clear.

## Locating the Bug in the Source

Inside `index.js`, the core logic looked roughly like this:

```javascript
if (req.query.username.length > 0xB16_C4A6A5) {
    // show flag
    res.send(`FLAG: ${process.env.FLAG}`);
}
```

The key security flaw:

- `req.query.username` is not guaranteed to be a string
- Express automatically parses query parameters like:

```
?username[length]=999
```

into:

```javascript
username = { length: "999" }
```

The code never checks:

- type of `username`
- whether `length` belongs to the actual username string
- whether `length` is numeric
- whether username is even a primitive

So by controlling `username` as an object, we controlled `username.length`.

The comparison constant, `0xB16_C4A6A5`, equals:

```
47626626725
```

We simply needed a `length` value larger than that.

## Triggering the Vulnerability

The exploit is elegantly simple:

### Exploit URL

```
https://big-chungus.challs.pwnoh.io/?username[length]=999999999999999
```

Express interprets this as:

```javascript
req.query = {
    username: {
        length: "999999999999999"
    }
}
```

JavaScript then evaluates:

```javascript
"999999999999999" > 47626626725
```

Which is true after coercing the string into a number.

This caused the server to enter the flag-rendering branch and return a response containing:

```
FLAG: bctf{...}
```

## How AI Assisted

AI materially accelerated the solution through:

### 1. Identifying the Type Confusion Vector

When searching for the behavioral flaw inside the server logic, AI correctly pointed out that Express parses nested parameters into objects, enabling `username[length]` attacks, a nuance that beginners rarely know.

### 2. Verifying the Dangerous Line (53)

AI highlighted exactly how line 53 of `index.js` was vulnerable, confirming the root cause: trusting `.length` without validating type or structure.

### 3. Constructing the Correct Exploit Payload

AI provided the exact URL syntax needed to craft an object-shaped query parameter that would survive Express parsing and override the expected primitive.

### 4. Explaining Why the Exploit Works

The explanation clarified the chain of logic:

- Query string → object
- Object.length → attacker-controlled
- Numeric coercion → bypass
- Flag-rendering branch → accessible

This not only solved the challenge but also built a deeper understanding of query parsing risks in JavaScript.

## Flag

Visiting the exploit URL produced the flag.

---

**Tools Used:** Browser, Express.js knowledge, JavaScript console  
**Skills:** Type confusion, query parameter injection, Express.js security, object manipulation
