# Block and Stream Ciphers

## 1. Cipher Types — Block vs Stream

Before we discuss algorithms, we must first understand the two families of symmetric encryption — **block ciphers** and **stream ciphers**.

### 1.1. Block Ciphers — Encrypting in Chunks

Block ciphers work on **fixed-size groups** of bits (commonly 64 or 128).  
Each block is processed by the same mathematical function and key.

```
Plaintext → [Block1][Block2][Block3]
              ↓      ↓      ↓
           Encrypt Encrypt Encrypt
              ↓      ↓      ↓
Ciphertext → [C1]  [C2]  [C3]
```

If the plaintext is not an exact multiple of the block size, **padding** is added (we’ll see this later).

### 1.2. Stream Ciphers — Encrypting on the Fly

A stream cipher encrypts one bit or byte at a time by generating a **keystream** — a sequence of pseudo-random bits derived from a secret key and possibly an initialization vector (IV).

```
Keystream: 10101100...
Plaintext: 11001001...
Ciphertext = Plaintext XOR Keystream
```

The same process decrypts, since XOR is its own inverse:

```
Plaintext = Ciphertext XOR Keystream
```

### 1.3. Analogy — A Tale of Boxes and Radios

- **Block Cipher:** Imagine packing letters into identical boxes and sealing each box separately with the same lock.  
  - Easy to organize, but patterns can leak if all boxes look the same.
- **Stream Cipher:** Instead of boxes, think of a **continuous radio signal** — the encryption is constant, shifting with every millisecond of data.

| Aspect | Block Cipher | Stream Cipher |
|---------|---------------|----------------|
| Processing | Fixed-size blocks | Continuous stream |
| Security Basis | Key + Mode | Key + Keystream generator |
| Example | AES, DES, Blowfish | RC4, A5/1 |
| Vulnerability | Pattern leaks (ECB) | Keystream reuse |
| Use Case | File storage, databases | Video calls, Wi-Fi, real-time IoT |

---

## 2. Block Cipher Modes — How Blocks Interact

A **block cipher** by itself can only encrypt one block. To encrypt long messages, we use *modes of operation*. These modes define how each block depends on the previous one.

### 2.1. ECB — Electronic Codebook Mode

The simplest (and least secure) mode. Each block is encrypted **independently**.

```
C_i = E_K(P_i)
P_i = D_K(C_i)
```

#### Example

If you encrypt a bitmap image (like the Linux penguin Tux) using ECB, the resulting image still **shows the outline** of the penguin — because identical plaintext blocks produce identical ciphertext blocks.

**Analogy:** Encrypting pages of a book with identical locks. Anyone noticing that two locked boxes look the same will guess their content is identical.

#### Why It’s Insecure

- Identical input → identical output.
- Does not hide data structure.
- Rarely used today except for encrypting random or independent data (e.g., keys).

---

### 2.2. CBC — Cipher Block Chaining Mode

Each plaintext block is XORed with the **previous ciphertext block** before encryption.  
The first block uses an **Initialization Vector (IV)**.

```
C_0 = IV
C_i = E_K(P_i XOR C_{i-1})
```

Decryption reverses the process:

```
P_i = D_K(C_i) XOR C_{i-1}
```

**Visualization:**

```
Plaintext Block1 -----> XOR -----> Encrypt -----> Ciphertext1
       ↓                                  ↑
       IV -------------------------------/
```

**Analogy:**  
CBC is like sending a chain of locked boxes, where each box’s combination depends on the contents of the previous one. Change one box, and all following boxes are affected.

**Security Property:**  
CBC hides patterns — two identical plaintext blocks result in different ciphertexts, as long as the IV is random.

**Common Pitfall:**  
If the same IV is reused with the same key, attackers can detect similarities between messages.

---

### 2.3. Padding and Ciphertext Stealing (CTS)

Block ciphers require messages to be multiples of the block size. If not, **padding** is added.

#### Example: PKCS#7 Padding

```
HELLO (5 bytes) → add 03 03 03
Result: "HELLO"
```

When decrypting, the receiver checks the padding bytes and removes them.

#### Ciphertext Stealing (CTS)

Avoids adding padding at all. Instead, it “steals” bytes from the final ciphertext block to fill the last partial block — keeping ciphertext length identical to plaintext.

**Analogy:** Folding the last letter to perfectly fit the box, rather than adding blank pages.

---

## 3. Beyond CBC — Other Block Cipher Modes

| Mode | Concept | Advantage | Use Case |
|------|----------|------------|----------|
| **CFB (Cipher Feedback)** | Converts block cipher to stream cipher | Real-time encryption | Network comms |
| **OFB (Output Feedback)** | Uses Initialization Vector (IV) repeatedly through encryption function | Error resistance | Satellite links |
| **CTR (Counter Mode)** | Encrypts counters to generate keystream | Parallelizable | High-speed systems |

**CTR mode** is especially popular today — it combines the **security of chaining** with **the performance of streams**.

---

## 4. Stream Ciphers — The Continuous Approach

Stream ciphers encrypt data bit-by-bit using a pseudo-random keystream.

### 4.1. RC4 — The Classic Example

Developed by Ron Rivest in 1987, RC4 was used in SSL/TLS and WEP.

#### Process Summary

1. Initialize a 256-byte array (S) with values 0–255.  
2. Permute S based on the key.  
3. Generate a keystream by swapping elements in S and using their combination.

```
for i in range(256):
    S[i] = i
j = 0
for i in range(256):
    j = (j + S[i] + K[i % keylen]) % 256
    swap(S[i], S[j])
```

#### Weakness

Early bytes of the keystream leak information about the key → devastating in WEP.

---

### 4.2. A5/1 — GSM Encryption

Used in 2G mobile networks.  
Based on **three Linear Feedback Shift Registers (LFSRs)** combined with majority logic.

```
Output = LFSR1 ⊕ LFSR2 ⊕ LFSR3
```

#### Weakness

Because of predictable clocking, attackers could precompute keystreams → crack GSM calls in minutes.

---

## 5. WEP and WPA2 — A Case Study in Design Failure

### 5.1. WEP — Wired Equivalent Privacy

Goal: secure Wi-Fi traffic using RC4.  
Flaw: **RC4 + 24-bit IV reused frequently**.

Attackers could:

- Capture packets.
- Notice repeated IVs.
- Use statistical analysis to recover keys.

#### Real Incident

By 2007, tools like *aircrack-ng* could break WEP in **under 60 seconds**.

---

### 5.2. WPA2 — A Secure Successor

WPA2 fixed WEP’s weaknesses by replacing RC4 with **AES-CCMP**, a combination of AES in **Counter mode** + **CBC-MAC** for integrity.

**Improvements:**

- 48-bit IV (nonce).  
- Per-session keys.  
- Strong integrity checking.  

WPA2 remains the standard today, though WPA3 adds further protections against offline password guessing.

---

## 6. Complex Ciphers — The Evolution

Modern symmetric cryptography evolved through a beautiful lineage — from **Feistel-based designs** like DES to **Substitution-Permutation Networks (SPNs)** like AES.  
Both achieve the same goal: confusion, diffusion, and avalanche — but they take very different roads to get there.

---

### 6.1. DES — The Feistel Legacy

Developed by IBM in the early 1970s (originally as "Lucifer"), **Data Encryption Standard (DES)** became the U.S. federal encryption standard in 1977.  
Though considered obsolete today, DES remains a masterpiece of early cryptographic engineering.

#### Core Characteristics

| Property | Value |
|-----------|-------|
| **Block Size** | 64 bits |
| **Key Length** | 56 bits (plus 8 parity bits) |
| **Rounds** | 16 |
| **Structure** | Feistel Network |
| **Main Operations** | Permutation, Expansion, XOR, Substitution (S-boxes) |

#### Feistel Round Overview

Each round transforms the data through a carefully designed pattern of splitting, substitution, and recombination.

```
Input Block → [L0 | R0]

For round i = 1 to 16:
  L_i = R_{i-1}
  R_i = L_{i-1} XOR F(R_{i-1}, K_i)
End

Ciphertext = [R16 | L16]  (swap halves at the end)
```

The Feistel structure allows the same algorithm to be used for **encryption and decryption** — only the round keys are applied in reverse order.

#### Inside the F-function

Each `F(R, K)` round function is where most of the cryptographic "magic" happens:

1. **Expansion (E-box)**  
   The 32-bit right half `R` is expanded to 48 bits, duplicating some bits for overlap.  
   This ensures that after substitution, dependencies spread across bits.

   ```
   R = 32 bits → E(R) = 48 bits
   ```

2. **Key Mixing (XOR)**  
   The expanded data is XORed with the round key `K_i`.

   ```
   E(R) XOR K_i
   ```

3. **Substitution (S-boxes)**  
   The 48-bit result is divided into 8 groups of 6 bits.  
   Each group passes through an **S-box** (substitution box), producing 4 bits of output.  
   Together, these create 32 bits of highly non-linear data.

   ```
   48 bits → S1…S8 → 32 bits
   ```

4. **Permutation (P-box)**  
   The final step shuffles the bits again to ensure **diffusion**.

#### Visualization of a DES Round

```
     Left Half (32 bits)      Right Half (32 bits)
      L_{i-1}                   R_{i-1}
         |                         |
         |                         v
         |                   F(R_{i-1}, K_i)
         |                         |
         v                         v
      (copy)                    (XOR)
         |                         |
         v                         v
       L_i                       R_i
```

The Feistel structure ensures **invertibility** — decryption simply runs rounds in reverse using the same F-function.

#### Why DES Was Revolutionary

- Introduced **confusion and diffusion** principles operationalized in hardware.  
- Allowed efficient implementation in silicon (1970s design constraint).  
- First to use multiple **S-boxes** designed for resistance to differential attacks (even before differential cryptanalysis was public!).

#### Why DES Failed Eventually

- The **56-bit key** became trivially short.  
  In 1998, the EFF built a DES Cracker that found keys in **56 hours**.  
- Vulnerable to **brute-force and linear cryptanalysis**.  

**Mitigation:** Triple DES (3DES) used multiple encryptions with 3 keys — more secure but 3× slower.

---

### 6.2. AES — The Modern Standard

In 2001, the National Institute of Standards and Technology (NIST) announced **AES (Advanced Encryption Standard)** — designed by Belgian cryptographers Joan Daemen and Vincent Rijmen under the name **Rijndael**.

AES replaced DES and became the world's dominant block cipher — used in everything from HTTPS to disk encryption to WPA2.

#### Core Characteristics

| Property | AES-128 | AES-192 | AES-256 |
|-----------|----------|----------|----------|
| **Block Size** | 128 bits | 128 bits | 128 bits |
| **Key Size** | 128 bits | 192 bits | 256 bits |
| **Rounds** | 10 | 12 | 14 |
| **Structure** | Substitution–Permutation Network (SPN) |

#### AES High-Level Flow

AES treats data as a **4×4 matrix of bytes** called the **state**.

```
Initial State = Plaintext + Key
  |
  v
Round 1 to N-1:
  SubBytes
  ShiftRows
  MixColumns
  AddRoundKey
  |
  v
Final Round:
  SubBytes
  ShiftRows
  AddRoundKey
```

#### Step-by-Step Transformations

1. **SubBytes (Confusion)**  
   Each byte is replaced using a fixed non-linear lookup table called an **S-box**.  
   This introduces **non-linearity** — tiny input changes lead to major output changes.

   ```
   Example: 0x53 → 0xED
   ```

2. **ShiftRows (Transposition)**  
   Rows of the state matrix are cyclically shifted left by 0, 1, 2, or 3 bytes.

   ```
   [a0 a1 a2 a3]      [a0 a1 a2 a3]
   [b0 b1 b2 b3]  →   [b1 b2 b3 b0]
   [c0 c1 c2 c3]      [c2 c3 c0 c1]
   [d0 d1 d2 d3]      [d3 d0 d1 d2]
   ```

   This ensures bytes spread across columns — enhancing diffusion.

3. **MixColumns (Diffusion)**  
   Each column is multiplied by a fixed matrix in the finite field GF(2⁸).  
   This mixes bits within each column, ensuring that one changed bit affects the entire block.

   ```
   | b'0 |   | 2 3 1 1 | | b0 |
   | b'1 | = | 1 2 3 1 | | b1 |
   | b'2 |   | 1 1 2 3 | | b2 |
   | b'3 |   | 3 1 1 2 | | b3 |
   ```

4. **AddRoundKey (Key Mixing)**  
   Each byte of the state is XORed with the round key derived from the main key.

   ```
   State[i][j] = State[i][j] XOR RoundKey[i][j]
   ```

#### Key Expansion (Schedule)

AES expands the secret key into a set of **round keys** using rotation, substitution, and XOR operations.

```
Key[0..Nk-1] → RoundKey[0..Nb*(Nr+1)-1]
```

This ensures each round uses a unique transformation of the original key.

#### Visualization of AES Round

```
Input State (4x4 bytes)
    |
    v
  SubBytes      → Non-linear substitution
    |
    v
  ShiftRows     → Row-wise bit diffusion
    |
    v
  MixColumns    → Column-wise mixing
    |
    v
  AddRoundKey   → Key material injection
    |
    v
Output State (4x4 bytes)
```

**Decryption:**  
Performed by applying inverse transformations (InvSubBytes, InvShiftRows, InvMixColumns) in reverse order.

#### Design Philosophy

| Principle | Purpose |
|------------|----------|
| **Confusion** | Non-linearity from S-boxes hides relationship between key and ciphertext |
| **Diffusion** | Bit changes spread through matrix transforms |
| **Simplicity** | Entirely byte-oriented, efficient in both hardware and software |
| **Parallelism** | Independent column operations → easy GPU acceleration |

#### Example Walkthrough (Simplified AES-128)

```
Plaintext: 3243F6A8885A308D313198A2E0370734
Key:       2B7E151628AED2A6ABF7158809CF4F3C

After initial key addition and 10 rounds:
Ciphertext: 3925841D02DC09FBDC118597196A0B32
```

Each round transforms the 128-bit block beyond recognition — ensuring a full avalanche effect.

#### Comparison: DES vs AES

| Feature | DES | AES |
|----------|------|------|
| Structure | Feistel | Substitution–Permutation Network |
| Block Size | 64 bits | 128 bits |
| Key Size | 56 bits | 128–256 bits |
| Operations | Bit-level | Byte-level |
| Parallelizable | No | Yes |
| Security Today | Broken | Secure (post-quantum strong with 256-bit) |

**Analogy:**  
If DES is a mechanical lock with 16 tumblers, AES is a digital vault that randomizes its internal layout every time you close it.

### Why AES Dominates Modern Cryptography

- **Fast:** Efficient in both hardware and software.  
- **Scalable:** Supports 128, 192, 256-bit keys.  
- **Secure:** Resistant to all known practical cryptanalysis.  
- **Universal:** Used in TLS, VPNs, disk encryption, Wi-Fi (WPA2), and mobile devices.  

**Conclusion:**  
DES laid the foundation with Feistel's brilliance — a reversible structure that defined modern encryption.  
AES took those lessons, added mathematics, parallelism, and byte-level abstraction — and became the *universal language of data secrecy* in the digital era.

## 7. Brute Force and Security Estimation

At 1 million key attempts/second:

| Key Length | Time to Brute Force |
|-------------|---------------------|
| 56-bit (DES) | ~2,285 years |
| 64-bit | ~585,000 years |
| 128-bit | ~10^25 years |

At modern speeds (2^30/sec distributed cloud cracking):

| Key Length | Effective Time |
|-------------|----------------|
| 64-bit | 1 year (on ~640 GPU cores) |
| 128-bit | still infeasible |

---

## 8. Padding Schemes — Making Data Fit the Blocks

When plaintext doesn't perfectly fill block cipher requirements, we need **padding** — extra data added to complete the final block. Think of it like packing a suitcase: if your clothes don't fill it completely, you stuff towels in the gaps.

But here's the catch: **both sender and receiver must agree on the padding scheme**, or decryption becomes a guessing game.

---

### 8.1. Zero Padding — The Simple Approach

**Method:** Fill incomplete blocks with zero bytes (`0x00`).

```
Original: "HELLO" (5 bytes)
Block size: 8 bytes
Padded: "HELLO\x00\x00\x00"
```

**Analogy:** Like filling empty spaces in a box with bubble wrap — simple, but you can't tell where the real content ends.

#### Problem with Zero Padding

If your original message naturally ends with zeros, the receiver can't distinguish between real zeros and padding zeros.

```
Message: "DATA\x00\x00" (6 bytes, ends with real zeros)
Padded:  "DATA\x00\x00\x00\x00" (8 bytes)
```

When decrypting, did the original message have 2, 4, or 6 bytes? **Ambiguity = Security Risk.**

**Real-World Incident:** Early implementations of some protocols using zero padding led to message truncation bugs — imagine losing the final zeros of bank account numbers!

---

### 8.2. PKCS#7 Padding — The Standard Solution

**Method:** Fill with bytes whose value equals the number of padding bytes needed.

```
If you need 3 padding bytes: add 0x03 0x03 0x03
If you need 1 padding byte:  add 0x01
If you need 8 padding bytes: add 0x08 0x08 0x08 0x08 0x08 0x08 0x08 0x08
```

#### Examples

```
Original: "HELLO" (5 bytes, block size 8)
Padding needed: 3 bytes
Result: "HELLO\x03\x03\x03"

Original: "TESTDATA" (8 bytes, block size 8)
Padding needed: 8 bytes (full block!)
Result: "TESTDATA\x08\x08\x08\x08\x08\x08\x08\x08"
```

**Key Insight:** PKCS#7 **always** adds at least 1 byte of padding, even when the message perfectly fits the block. This eliminates ambiguity.

**Analogy:** Like writing the number of extra towels on each towel you stuff in the suitcase. When unpacking, you know exactly how many to remove.

### 8.3. ANSI X9.23 Padding — Random with Length

**Method:** Fill with random bytes, but make the **last byte** indicate the padding length.

```
Original: "HELLO" (5 bytes, block size 8)
Padding needed: 3 bytes
Result: "HELLO\x7A\x42\x03"
    (random)(random)(length)
```

**Advantage:** Random padding bytes make **traffic analysis harder** — identical plaintexts produce different ciphertexts even before encryption.

**Analogy:** Stuffing the suitcase with different random items each time, but always putting a note on top saying "I added 3 extra items."

#### Security Benefit

```
Message A: "ATTACK" → "ATTACK\x91\x03" (random padding)
Message B: "ATTACK" → "ATTACK\x2F\x03" (different random padding)
```

Even before block cipher encryption, the padded blocks look different — **reducing patterns** in ciphertext.

---

### 8.4. ISO 10126 Padding — Random Everywhere

**Method:** Identical to ANSI X9.23 — random bytes with length indicator at the end.

```
Original: "SECRET" (6 bytes, block size 8) 
Padding needed: 2 bytes
Result: "SECRET\xA7\x02"
```

**Note:** ISO 10126 and ANSI X9.23 are functionally identical in most implementations.

---

### 8.5. Bit Padding (1-0 Padding) — The Minimalist

**Method:** Add a single `1` bit, then fill the rest with `0` bits.

```
Original: "TEST" (32 bits)
Block size: 64 bits
Padded: "TEST" + 1 + 000...000 (31 zero bits)
```

**Advantage:** **Unambiguous and minimal** — you always know where the message ends (just before the first `1` bit when reading backwards).

**Analogy:** Like putting a single red marker at the end of your real items, then filling the rest with white space.

#### Binary Example

```
Message:  01001000 01100101 01101100 01101100  ("Hell")
Padded:   01001000 01100101 01101100 01101100 10000000 00000000 00000000 00000000
      |------------ original ------------|^mark    |------ zeros ------|
```

#### Use Cases

- **Hash functions** (like SHA-256) use bit padding internally.
- **Cryptographic protocols** where bit-level precision matters.
- **Low-level implementations** where byte-alignment isn't required.

---

### 8.6. Comparison of Padding Schemes

| Scheme | Last Byte Meaning | Randomness | Unambiguous? | Use Case |
|---------|-------------------|------------|---------------|----------|
| **Zero** | Could be data | No | **No** | Legacy systems only |
| **PKCS#7** | Padding length | No | **Yes** | Most common (TLS, AES) |
| **ANSI X9.23** | Padding length | Random fill | **Yes** | Traffic analysis resistance |
| **ISO 10126** | Padding length | Random fill | **Yes** | Same as ANSI X9.23 |
| **1-0 Bit** | Not applicable | No | **Yes** | Hash functions, minimal overhead |

### 8.6. Block Size Requirements — The 8-Byte Rule

**Important**: When working with block ciphers, the output must always be a multiple of the block size. For 64-bit block ciphers (like DES), this means the ciphertext must be a multiple of 8 bytes.

**Example**: A 25-character string (25 bytes) would produce a 32-byte ciphertext after padding.

```
Original: 25 bytes
Next multiple of 8: 32 bytes  
Padding added: 7 bytes (using PKCS#7: \x07\x07\x07\x07\x07\x07\x07)
```

**Why This Matters**: Block ciphers can only process complete blocks. Incomplete final blocks must be padded to the full block size, ensuring the encryption algorithm receives properly sized input chunks.

**Note**: Modern AES uses 128-bit (16-byte) blocks, so outputs must be multiples of 16 bytes.

---

### 8.7. Padding Oracle Attacks — When Padding Goes Wrong

**The Vulnerability:** If an application reveals whether padding is **valid or invalid** during decryption, attackers can use this as an oracle to decrypt messages **without knowing the key**.

#### How It Works

1. Attacker captures ciphertext.
2. Modifies the second-to-last block systematically.
3. Sends modified ciphertext to the application.
4. If the application says "padding error" vs "other error," the attacker learns information about the plaintext.

```
Valid response:   "Message processed"
Invalid padding:  "Padding error"
Other errors:     "Authentication failed"
```

By trying different modifications and observing responses, attackers can **decrypt the entire message** byte by byte.

#### Real-World Example

- **2002**: Serge Vaudenay discovered padding oracle attacks against CBC mode.
- **2010**: The "POODLE" attack against SSL 3.0 exploited padding oracles.
- **2016**: Several web applications were found vulnerable to padding oracle attacks in their encryption APIs.

#### Defense

**Always use authenticated encryption** (like AES-GCM) or **verify integrity before checking padding**.

```python
# WRONG: Check padding first
def decrypt_wrong(ciphertext, key):
  plaintext = decrypt_cbc(ciphertext, key)
  if not valid_padding(plaintext):
    raise PaddingError("Invalid padding")  # Reveals padding state!
  return remove_padding(plaintext)

# CORRECT: Check authentication first
def decrypt_correct(ciphertext, key, mac):
  if not verify_mac(ciphertext, mac, key):
    raise AuthenticationError("Invalid MAC")  # Same error for all failures
  plaintext = decrypt_cbc(ciphertext, key)
  return remove_padding(plaintext)
```

---

### 8.8. Real-World Padding Mistakes — A Cautionary Tale

#### Case Study: The Banking API Bug

In 2019, a financial institution's API used PKCS#7 padding but had inconsistent validation:

- **Valid padding**: Returned HTTP 200 with processed transaction
- **Invalid padding**: Returned HTTP 400 with "Padding error"  
- **Invalid authentication**: Returned HTTP 401 with "Authentication failed"

Attackers exploited this to:

1. Capture encrypted transaction requests
2. Modify padding systematically  
3. Extract account numbers and amounts from the padding oracle responses
4. Forge valid transactions

**The Fix:** All decryption errors were changed to return the same generic HTTP 500 response.

---

### 8.9. Choosing the Right Padding

| Scenario | Recommended Padding | Reason |
|----------|-------------------|---------|
| **Modern applications** | Use **authenticated encryption** (AES-GCM) | Avoids padding altogether |
| **Legacy CBC mode** | PKCS#7 + **constant-time validation** | Industry standard, well-tested |
| **Traffic analysis concerns** | ANSI X9.23 or ISO 10126 | Random padding reduces patterns |
| **Hash functions** | 1-0 bit padding | Minimal overhead, mathematically clean |
| **Never use** | Zero padding | Ambiguous, security risk |

---

**Key Takeaway:** Padding seems like a simple problem, but the devil is in the details. Modern cryptography has largely moved to **authenticated encryption modes** like GCM that avoid padding altogether — proving that sometimes the best solution to a hard problem is to **engineer the problem away**.

---

## 8. Summary: Lessons from History

| Concept | Mistake | Modern Lesson |
|----------|----------|----------------|
| ECB | Reused keys, visible patterns | Use CBC or CTR with random IV |
| CBC | Static IVs reused | Always randomize IV |
| RC4 | Weak early bytes | Never use biased keystreams |
| WEP | Small IV, key reuse | Use per-session keys |
| DES | Short key length | Move to AES 128/256 |
| AES | Still secure | Maintain strong key management |

---

## 9. Real-World Analogies Recap

| Concept | Analogy |
|----------|----------|
| Block Cipher | Packing letters into fixed boxes |
| ECB | Identical boxes → pattern leaks |
| CBC | Boxes chained by previous contents |
| Padding | Folding paper to fit envelope |
| Stream Cipher | Continuous radio signal |
| WEP | Reused lock key |
| AES | Modern vault with layers of locks |

---

**End of Lecture — From Blocks to Streams: Understanding Symmetric Encryption**
