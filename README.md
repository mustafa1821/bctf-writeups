# CTF Write-Ups Collection

A comprehensive collection of Capture The Flag (CTF) challenge write-ups from November 2025 competition.

## Overview

This repository contains detailed technical write-ups for six CTF challenges spanning multiple categories including Forensics, Cryptography, and Web Exploitation. Each write-up includes methodology, tools used, and AI-assisted problem-solving techniques.

## Challenges

### Forensics

1. **[Shredder](writeups/01-shredder.md)** (100 pts)
   - PNG reconstruction and zlib decompression
   - Signature carving and chunk analysis
   
2. **[Old Email / UUEncoded DOS Binary](writeups/03-old-email.md)**
   - Legacy uuencoding format recognition
   - DOS executable analysis

### Cryptography

3. **[Clandescriptorius](writeups/02-clandescriptorius.md)** (176 pts)
   - Custom base-N encoding
   - Alphabet mapping and pattern recognition

### Web Exploitation

4. **[Ramesses / Pharaoh Login](writeups/04-ramesses.md)**
   - Cookie manipulation
   - Base64-encoded JSON exploitation
   
5. **[Big Chungus](writeups/05-big-chungus.md)**
   - JavaScript type confusion
   - Express.js query parameter injection
   
6. **[Awklet](writeups/06-awklet.md)**
   - Null-byte injection
   - CGI/AWK arbitrary file read

## Repository Structure

```
ctf-writeups/
├── README.md                          # This file
├── writeups/                          # Individual markdown write-ups
│   ├── 01-shredder.md
│   ├── 02-clandescriptorius.md
│   ├── 03-old-email.md
│   ├── 04-ramesses.md
│   ├── 05-big-chungus.md
│   └── 06-awklet.md
├── documents/                         # Original Word documents
│   ├── shredder_ctf_writeup.docx
│   ├── Clandescriptorius_ctf_writeup.docx
│   ├── Oldemail_ctf_writeup.docx
│   ├── Ramsesse_ctf_writeup.docx
│   ├── bigChungus_ctf_writeup.docx
│   └── Awklet_ctf_writeup.docx
└── assets/                            # Challenge files and screenshots
```

## Skills Demonstrated

- **Binary Analysis**: PNG structure parsing, file carving, zlib decompression
- **Legacy Formats**: UUencoding, DOS executables, classic encoding schemes
- **Cryptanalysis**: Pattern recognition, custom base conversion, alphabet mapping
- **Web Security**: Cookie manipulation, type confusion, query injection, null-byte attacks
- **Programming**: Python scripting, PowerShell automation, AWK scripting

## AI-Assisted CTF Solving

These write-ups document the role of AI assistance in:
- Format and vulnerability identification
- Script generation and debugging
- Error interpretation and recovery strategies
- Security concept explanation and education

## Tools Used

- **Binary Analysis**: hex editors, PNG parsers, zlib tools
- **Decoding**: Python (uudecode, base64), online decoders
- **Web Testing**: Browser DevTools, PowerShell, curl
- **Emulation**: DOSBox
- **AI Assistance**: ChatGPT, Claude

## Competition Details

- **Event**: [Competition Name]
- **Date**: November 2025
- **Team/Individual**: Individual
- **Total Points**: 276+ points

## Author

Mustafa
- Engineering Student at Ohio State University
- Focus: Cybersecurity and Competitive Programming

## License

These write-ups are for educational purposes. Challenge content and flags are property of the CTF organizers.

---

*Note: Flags have been partially redacted in public versions of these write-ups.*
