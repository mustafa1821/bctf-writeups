# Shredder (100 pts)

**Category:** Forensics

## Overview

Shredder presented a corrupted file that appeared to represent a partially destroyed or fragmented data source. At first glance, nothing about the file was usable in its raw form, opening it with typical tools yielding errors, partial output, or unreadable garbage. This signaled immediately that the core of the challenge involved reconstructing or carving embedded data from an inconsistent byte stream. Uploading the image files into ChatGPT confirmed this suspicion and allowed for a more in-depth understanding of how we should approach this challenge.

The primary suspicion was that the file contained a valid format, most likely PNG, but with damaged or removed chunks. This would require low-level inspection, signature carving, and controlled decompression attempts.

## Initial Analysis

The first step was to examine the file for known magic bytes. Using binary inspection tools and simple signature greps (e.g., searching for the PNG header `89 50 4E 47`), I confirmed the presence of a valid PNG signature inside the file rather than at its beginning. This strongly implied that the original file had been shredded or wrapped inside another format, and the solver was expected to carve it out manually.

Running a PNG structure parser using ChatGPT showed:

- An intact IHDR chunk
- A massively truncated IDAT stream
- No IEND chunk
- No successive PNG signatures

This ruled out the possibility of recovering the original file through traditional repair since the binary simply lacked the required amount of data.

## Reconstruction Attempts

I moved forward with extracting whatever IDAT data existed. Given the information I had found up until this point I was able to prompt AI to generate a custom Python script that aggregated all IDAT chunks (only one in this case), concatenated them, and attempted to decompress the resulting zlib data.

Initially, the decompression failed with an error typical of truncated DEFLATE streams:

```
Error -5: incomplete or truncated stream
```

This was expected. So instead of a strict decode, I attempted partial zlib decompression, allowing the algorithm to output whatever blocks were recoverable before encountering invalid bytes. This produced:

```
Recovered 357 rows out of expected 329.
```

This was a strange but revealing result: although the advertised height was 329 pixels, decompression yielded more lines than expected. Such behavior occurs when garbage data at the end of the compressed stream gets interpreted as deflated blocks.

Despite this, the partially recovered image included enough meaningful visual content to read the challenge's flag and inspire my new wallpaper.

## Data Interpretation

Once the image had been "unshredded" the Python script sent the corrected image to a folder where it could be accessed. Once accessed it displayed an anime femboy with the flag printed across the center of the image.

## Role of AI in Solving the Challenge

AI played three important roles in this solution:

### 1. Structural Reasoning Assistance

AI helped validate the hypothesis that the file likely contained a buried PNG and that the missing IDAT chunks were the core issue, preventing full reconstruction. This prevented wasted time exploring unrelated file formats.

### 2. Python Script Generation & Debugging

AI generated clean Python scripts to:

- Locate PNG signatures
- Extract chunk data
- Concatenate all IDAT fragments
- Attempt safe zlib decompression with exception handling

This accelerated testing of multiple reconstruction paths.

### 3. Error Interpretation & Recovery Strategy

AI clarified the meaning of zlib error codes and why the decompressed row count exceeded the IHDR height, guiding the approach toward "recover whatever is usable" rather than attempting strict recovery.

This support saved significant time by eliminating blind guesswork and ensuring reconstruction efforts stayed technically grounded.

## Flag

The partially reconstructed PNG revealed the correct flag embedded in the readable region of the image.

---

**Tools Used:** Python, zlib, PNG parsers, ChatGPT  
**Skills:** Binary analysis, file carving, zlib decompression, AI-assisted debugging
