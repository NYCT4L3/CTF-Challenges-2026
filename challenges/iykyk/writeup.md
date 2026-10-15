# IYKYK: Writeup

> **Spoilers.** This file contains the full solution.

**Category:** Crypto / Misc · **Difficulty:** Medium–Hard (a strong player needs 30–60 minutes; others will need hints)  
**Tools:** the official [CyberChef](https://gchq.github.io/CyberChef) and [CrackStation](https://crackstation.net).

> **Use the official CyberChef site.** Some older copies (for example one hosted by a university) have a bug in *Derive EVP key* that produces a wrong key, with no error message.

The title **IYKYK** (If You Know, You Know) is an acronym. Each letter is one layer, from the outside in:

| Letter | Layer |
|---|---|
| **I** | International Morse Code |
| **Y** | AES-256-CBC, passphrase starts with Y (the word is found by cracking an MD5 hash) |
| **K** | Keyword cipher (keyword `KRYPTOS`) |
| **Y** | AES-256-CBC again, a different passphrase starting with Y (another MD5 hash) |
| **K** | Vigenère cipher (key `KNOWLEDGE`) |

**The twist:** nothing in the two Y layers is an algorithm starting with Y. The Y is in the *passphrase*.

Every layer's decoded text is a short note (the clue) followed by the next blob.

## Solution

### Layer 1: Morse code (I)

1. Open `challenge.txt` and copy everything.
2. In CyberChef, paste it into the Input and add **From Morse Code** (defaults: letter delimiter Space, word delimiter Forward slash).
3. The output is all uppercase (Morse has no case):

```
ONE LAYER DOWN. NEXT LOCK IS AES. MD5 OF PASSPHRASE:
D487DD0B55DFCACDD920CCBDAEAFA351 CIPHERTEXT: 53616C74...
```

**Checkpoint:** the hex after `CIPHERTEXT:` is exactly 608 characters.

### Layer 2: crack the hash, then AES (Y)

4. The 32-character string is an MD5 hash. Lowercase it (`d487dd0b55dfcacdd920ccbdaeafa351`) and paste it into CrackStation. The answer is `yellow`.
5. Split the 608-character hex blob from the left:
   - Characters 1–16: `53616C7465645F5F`. This is the text `Salted__` (OpenSSL's label). Ignore it.
   - Characters 17–32: the salt, `477914440350D1E3`
   - Characters 33–608: the ciphertext (576 characters, starts with `D32560A06A7F`)
6. Add **Derive EVP key** with:
   - Passphrase: `yellow` (UTF8)
   - Key size: `384`
   - Iterations: `1`
   - Hashing function: `MD5`
   - Salt: `477914440350D1E3` (Hex)

   The output is 96 hex characters. The first 64 are the key and the last 32 are the IV:

   ```
   Key: 6bbadb5bddfa102f00362443fd8f236f9716382f6aaf08799e4edac3e9e9ee4b
   IV:  d4fc186e5f2fcd27093c650bd5bf02a3
   ```

7. Make a new recipe. Paste only the 576-character ciphertext as the Input. Add **From Hex**, then **AES Decrypt** with Key (Hex), IV (Hex), Mode CBC, Input Raw, Output Raw.
8. The output is:

```
A KEYWORD CIPHER IS NEXT. KEYWORD: KRYPTOS
GTWN EHYD BM KTM. FP5 HO IKMMIALKMT: tky9t8pp8575o...
```

### Layer 3: keyword cipher (K)

A keyword cipher is a scrambled alphabet: the keyword goes first (no repeats), then the remaining letters in order. For `KRYPTOS` the alphabet is `KRYPTOSABCDEFGHIJLMNQUVWXZ`, so every A in the message was replaced by K, every B by R, and so on. To decrypt, swap back.

9. Copy the second line (the long blob, not the note).
10. In a new recipe, paste it as the Input and add **Substitute** with:
    - Plaintext: `KRYPTOSABCDEFGHIJLMNQUVWXZkryptosabcdefghijlmnquvwxz`
    - Ciphertext: `ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz`
11. The output is:

```
NEXT LOCK IS AES. MD5 OF PASSPHRASE: eac9e8dd8575f4c7831f1f6a72607126
CIPHERTEXT: 53616C74...
```

**Checkpoint:** the hex after `CIPHERTEXT:` is exactly 160 characters.

### Layer 4: crack the hash, then AES again (Y)

12. Paste `eac9e8dd8575f4c7831f1f6a72607126` into CrackStation. The answer is `yesterday`.
13. Split the 160-character hex blob the same way:
    - Characters 1–16: `53616C7465645F5F` (ignore)
    - Characters 17–32: the salt, `83CF9DAEAD46CB15`
    - Characters 33–160: the ciphertext (128 characters, starts with `DC257F876C8C`)
14. **Derive EVP key** with passphrase `yesterday` (UTF8), key size `384`, iterations `1`, MD5, salt `83CF9DAEAD46CB15` (Hex):

    ```
    Key: c2a14c8b0eef6286cf781b408159d2febea8cd1d357b8171c17f55f89712a8e4
    IV:  2cdca91f700ea8778896e102ccde705e
    ```

15. Paste only the 128-character ciphertext, then **From Hex** and **AES Decrypt** (same settings as layer 2). The output is:

```
LAST ONE. VIGENERE. KEY: KNOWLEDGE
MGT{EqCnEo_Fngppkxte_Rhwuje}
```

### Layer 5: Vigenère (K)

16. Copy the second line only (`MGT{EqCnEo_Fngppkxte_Rhwuje}`).
17. Add **Vigenere Decode** with the key `KNOWLEDGE`.
18. The flag appears.

**Flag:** `CTF{...}` (redacted; it is the output of step 17)

Only letters are shifted. Digits, underscores and braces stay the same, and the key advances only on letters.

## The same solution on the command line (optional)

For each AES layer, with the ciphertext hex in `HEX` and the cracked word as the passphrase:

```
printf '%s' "$HEX" | basenc --base16 -d | openssl enc -d -aes-256-cbc -md md5 -pass pass:yellow
```

The `HEX` here is the whole blob including `Salted__`; OpenSSL reads the salt itself. Use `pass:yesterday` for layer 4. OpenSSL prints a harmless warning about the old key derivation.

## Common problems

- **Derived key looks wrong.** Make sure it is the official CyberChef site. Check the salt is exactly 16 characters and set to Hex, and the passphrase is set to UTF8.
- **Salt has 17 characters.** The first ciphertext character was copied by accident.
- **Output is garbage that looks like the input.** A pause button (breakpoint) is turned on in the recipe. Click it to turn it off. Also make sure *From Hex* comes before *AES Decrypt*.
- **"Unable to decrypt input with these parameters."** *From Hex* is missing, or the input is missing or has an extra character. Check the character counts above.
- **The MD5 hash is not found.** The Morse output is uppercase, so lowercase it before cracking.
- **Later steps fail.** Paste only the blob line, not the first note line.

## Hints

| # | Hint |
|---|---|
| 1 | The title is an acronym. Five letters, five layers. |
| 2 | The outer layer is not encryption at all. It is one of the oldest ways of sending a message over a wire. |
| 3 | Not every letter names an algorithm. A hash cannot be reversed, but it can be looked up. |
| 4 | Hex that starts with `53616C7465645F5F` is OpenSSL's AES format. Use the cracked word as the passphrase with MD5 key derivation (CyberChef: *Derive EVP key*, then *AES Decrypt*). Use the official CyberChef site, not an old copy. |
| 5 | The two K layers are a keyword cipher (a scrambled alphabet built from a keyword) and a Vigenère. Each layer's note tells you the keyword. |

Suggested timing: hints 1 and 2 early, hint 3 at the midpoint, hints 4 and 5 in the last stretch.

## Design notes

- Every rebuild of `challenge.txt` creates new random salts and ciphertext, so this writeup only matches the file in this repo. Do not regenerate it.
- Before running this at an event, check on CrackStation that both hashes still crack to `yellow` and `yesterday`.
