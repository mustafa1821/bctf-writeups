# Clandescriptorius (176 pts)

**Category:** Cryptography

## Overview

Clandescriptorius was a cryptography challenge centered around an intentionally misleading ciphertext. At first glance, the task resembled a standard substitution or classical cipher problem, the sort where solvers instinctively attempt frequency analysis, Caesar shifts, or simple XOR patterns. But the puzzle behaved inconsistently with any of those typical structures. The ciphertext resisted straightforward statistical attacks, and early manipulations produced no meaningful plaintext.

The key realization was that the challenge title, Clandescriptorius, hinted at a layer of "hidden writing", something concealed not in the ciphertext's characters themselves but in how the text was encoded. The difficulty stemmed not from breaking strong cryptography but from identifying the format in which the message was clandestinely represented.

## Initial Analysis

I began by examining the ciphertext's structure:

- Length was unusual: too long for a simple Caesar, too short for a block cipher sample
- The character distribution did not resemble printable ASCII
- Repeating patterns existed, but not in a way consistent with common classical ciphers
- Some bytes fell outside typical alphabetic or alphanumeric sets

This suggested that the text wasn't encrypted using a traditional cipher, it was probably encoded, not encrypted. Whenever encoded data is intentionally disguised, the challenge often involves:

- Base conversions
- Non-standard alphabets
- Novel or historical ciphers
- Format obfuscation

After ruling out the common encodings (Base32, Base64, Base85, Hex, ROT variants), the next step was to investigate whether the characters belonged to a particular numeric system.

## Breakthrough Insight

The key observation came from noticing that the symbol set had a limited alphabet but not one that matched base64 or base32. Instead, its distribution aligned more closely with an arbitrary base-n encoding, the sort used in custom substitution schemes to hide the fact that the content is actually just numbers.

By mapping each character to an index value and analyzing mod patterns, it became clear that the ciphertext encoded data in a base far beyond the typical classical sets. The distribution matched a base-62 / base-N style alphabet, where the actual challenge was to correctly interpret the chosen ordering.

Once the proper alphabet was identified (the challenge intentionally altered character ordering to mislead naïve decoders), converting the ciphertext into raw bytes produced a meaningful binary blob.

That binary decoded cleanly into a readable plaintext containing the flag.

## Detailed Walkthrough

### 1. Character Set Extraction

The first step was isolating every unique character in the ciphertext. The size of the character set gave a strong hint about the encoding base. This narrowed the puzzle to a non-standard base-encoding scheme using a custom alphabet.

### 2. Alphabet Mapping

Once the character set was extracted in its natural order (sorted by appearance), it became possible to test hypotheses quickly. The ciphertext's structure suggested that each character represented a single digit in some base. A brute-force alphabet alignment revealed the correct mapping.

### 3. Base Conversion

After discovering the correct ordering, the ciphertext was interpretable as a giant integer represented in base-N. Converting that integer into bytes produced:

- Proper UTF-8 representation
- Well-formed ASCII
- A readable plaintext containing the flag

Everything clicked at this point, the cipher was purely an encoding puzzle disguised as a cryptographic one.

## Role of AI in Solving the Challenge

AI played a critical role in three places:

### 1. Pattern Identification

AI helped verify early observations about the unusual character frequency and guided the reasoning toward base-encoding rather than classical cipher structures. This prevented wasting time on dead-end Caesar/ROT/XOR attempts.

### 2. Alphabet Testing Automation

AI quickly generated Python scripts to:

- Extract unique alphabet characters
- Test multiple alphabet orderings
- Convert ciphertext from arbitrary base-N to integers
- Convert integers to raw bytes

This allowed rapid iteration without hand-coding boilerplate conversions.

### 3. Interpreting Binary Output

When the decoded bytes initially appeared partially corrupted, AI suggested:

- Trying alternate byte-grouping interpretations
- Testing endianness variants
- Ensuring no padding adjustments were needed

This helped stabilize the final output and confirm the correct plaintext.

AI's support accelerated experimentation, allowing me to focus on the underlying cipher logic instead of implementation details.

## Flag

Successfully decoding the custom base-N encoding revealed the flag.

---

**Tools Used:** Python, custom base conversion scripts, Claude AI  
**Skills:** Pattern recognition, alphabet mapping, base conversion, encoding analysis
