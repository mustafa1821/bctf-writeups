# Old Email / UUEncoded DOS Binary

**Category:** Forensics

## Overview

This challenge presented an email "from 1985" containing a block of strange characters beginning with `begin 755 FLGPRNTR.COM` and ending with `end`. At first glance it was unclear whether this was encrypted, hashed, or encoded. The wording of the problem hinted that the solution lay in understanding older formats rather than cracking modern crypto.

The task was ultimately a matter of recognizing a classic uudecode payload embedded in the message, a method used historically for emailing binary attachments in plain text. Decoding the attachment produced a DOS `.COM` executable that printed the flag when run.

This was a pure format-recognition forensics challenge, disguised as a cryptography puzzle.

## Initial Recognition

The first major clue was the structure:

```
begin 755 FLGPRNTR.COM
M.....
...
end
```

This exact pattern is characteristic of uuencoding, a legacy encoding scheme used heavily in the 1980s and early 1990s for sending binary files through text-only email gateways. Seeing that, the goal crystallized: decode the block, extract the binary, and inspect or run it.

This recognition step was key. Without it, the text resembles ciphertext, but knowledge of historical file formats makes the solution immediate.

## Recovering the File

Using the uuencoded block from the challenge, the file could be recovered by:

### Linux / macOS

```bash
uudecode mail.uue
```

### Python (any OS)

```python
import io, uu

data = b"""begin 755 FLGPRNTR.COM
MOAP!@#PD:-"I&Z_6Z'&T"<TAP[1,,,#-(4A)7DQ1;AM.=5,:7W5_61EU
;:T1U&4=?1AY>&EAU95AU3AE)&D=:&T9O6%<D
end
"""

uu.decode(io.BytesIO(data), open("FLGPRNTR.COM","wb"), quiet=True)
```

This produced `FLGPRNTR.COM`, a ~114-byte DOS executable.

## Understanding the Program

Two approaches were possible:

### 1. Dynamic Execution (Fastest)

Running the program in DOSBox printed the flag immediately. DOS `.COM` binaries are flat memory images and load easily in emulation.

### 2. Static Analysis (More Educational)

Opening the file in a hex editor or disassembler revealed:

- A short sequence of instructions pushing an offset into the `DX` register
- A call to INT 21h AH=09, the DOS "print string until `$`" service
- A small block of obfuscated ASCII text ending with `$`

This confirmed that the file was a purpose-built "flag printer."

## How AI Assisted

AI contributed to this challenge in three significant ways:

### 1. Format Identification

When the text block looked like noise, the AI recognized the `begin 755 ...` structure and confirmed it matched uuencoding, steering the investigation away from hash cracking or cipher analysis.

### 2. Script Generation

AI produced clean, runnable Python code for decoding the uuencoded attachment without requiring installation of command-line tools.

### 3. Binary Behavior Explanation

AI described how DOS interrupts worked, why the INT 21h AH=09 service prints until `$`, and how the obfuscated string could be extracted manually, helping to understand the challenge beyond simply running the program.

## Flag

Extracting or running the binary revealed:

```
ctf{D1d_y0u_Us3_***An_3mul4t0r_Or_d3c0mp1lEr}
```

---

**Tools Used:** Python uudecode, DOSBox, hex editor  
**Skills:** Legacy format recognition, UUencoding, DOS executable analysis, AI-assisted learning
