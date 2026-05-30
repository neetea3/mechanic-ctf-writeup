# mechanic-ctf-writeup
Crypto CTF writeup involving ML-KEM key leakage and automated decryption.
# Mechanic - Crypto CTF Writeup

## Challenge Overview

Mechanic is a cryptography challenge involving a flawed implementation of a post-quantum encryption scheme using ML-KEM-1024.

The challenge provides:

* flag_22.enc
* output.raw
* mechanic.py

## Technical Summary

The challenge implemented a post-quantum encryption workflow using ML-KEM-1024. During source code review, I discovered that every generated secret key was written directly to a file named `output.raw`.

By determining the ML-KEM secret key size and splitting the file into fixed-size chunks, I was able to identify valid decryption keys and automate recursive decryption of every encryption layer until the original PNG image containing the flag was recovered.

## Initial Analysis

After extracting the archive, I inspected the provided files.

![Initial Files](Screenshot%20\(527\).png)

The source code revealed that the program generated multiple secret keys and stored them inside `output.raw`.

![Source Code](Screenshot%20\(528\).png)

## Vulnerability

The critical vulnerability was:

```python
f.write(skey)
```

Every secret key was written to `output.raw`, allowing recovery of the encryption chain.

## Determining Key Size

Using the QuantCrypt library, I determined the secret key size for ML-KEM-1024.

![Key Size](Screenshot%20\(539\).png)

Output:

```text
pk = 1568
sk = 3168
```

## Testing Key Chunks

After splitting `output.raw` into chunks of 3168 bytes, I tested each chunk against the encrypted file.

![Valid Key Discovery](Screenshot%20\(541\).png)

A valid secret key was identified, allowing decryption of the outer encryption layer.

## Automating Decryption

I created a Python script that recursively decrypted every layer using the recovered key chunks.

![Layer Decryption](Screenshot%20\(548\).png)

The script successfully decrypted all layers from layer 22 down to layer 0.

## Recovering the Flag

The final output was a PNG image.

![Recovered Image](Screenshot%20\(549\).png)

The recovered image contained the challenge flag.

## Skills Demonstrated

* Python scripting
* Cryptographic analysis
* Source code review
* Automation
* Reverse engineering
* Post-quantum cryptography concepts
