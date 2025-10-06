# Hash Functions

- See last section for references of the next affirmations.

> **Hash functions** are magical functions that take an input (or 'message') and return a fixed-size string of bytes. The output is typically a 'digest' that is unique to each unique input.

**Purpose of Hash Functions:** data integrity to messages/files. For a number you get the same output every time. If you change even one bit of the input, the output will change completely.

> Hash Functions do not encrypt data. They are one-way functions, meaning you cannot reverse the process to retrieve the original input from the hash output.

## Characteristics of Hash Functions

A **hash function** is a mathematical function that takes an input message `M` of variable length and produces a fixed-length output called the **hash value** or **message digest**, denoted as `h = H(M)`.

---

### Key Characteristics

1. **Easy to Compute (Forward Direction)**
   - For any input `M`, it is computationally *easy* to calculate the hash value `h = H(M)`.

2. **One-Way Property**
   - Given only `h`, it is *impossible* (computationally infeasible) to determine the original input `M`.  
     → This is known as **pre-image resistance**.
     →  Pre-Image Resistance: The difficulty of reversing the hash function to find the original input.

3. **Collision-Free (Resistance) Property**
   - It is *very difficult* to find two distinct messages `M` and `M′` such that:

     ```txt
     H(M) = H(M′)
     ```

   - This property ensures **collision resistance** — two different inputs should never produce the same hash value.

4. **Avalanche Effect**
   - Changing even a **single bit** in `M` will completely change the output `h`.  
     → The new hash appears totally unrelated to the previous one.

5. **Fixed-Length Output**
   - Regardless of the input size (1 byte or 1 GB), the hash function always outputs a fixed-length value (e.g., 128 bits for MD5, 256 bits for SHA-256).

6. **Puzzle Friendly**
   - For any hash result h, even if someone knows a random value r (chosen from a very large set of possibilities), it should be practically impossible for them to figure out what original message M would produce that hash result:

     ```txt
     H(r || M) = h
     ```

   - This property is useful in **proof-of-work** systems, where finding such an `M` requires significant computational effort.

---

## MD5 Algorithm

**MD5 (Message-Digest Algorithm 5)** is a widely known cryptographic hash function that produces a 128-bit (16-byte) hash value, typically expressed as a 32-character hexadecimal number.

### MD5 Usage

MD5 was commonly used for:

- **File integrity verification** - ensuring files haven't been corrupted during transfer
- **Digital fingerprinting** - creating unique identifiers for files
- **Password storage** - storing hashed passwords in databases
- **Checksums** - verifying data integrity in various applications

### Security Status: **DEPRECATED** ⚠️

**MD5 is cryptographically broken and unsuitable for further use:**

- **Collision vulnerabilities**: Researchers have demonstrated practical collision attacks
- **Speed weakness**: MD5 is too fast, making brute-force attacks feasible
- **Rainbow table attacks**: Pre-computed hash tables make password cracking easier
- **Not recommended** for any security-critical applications

### Modern Alternatives

Instead of MD5, use:

- **SHA-256** or **SHA-3** for general cryptographic purposes
- **bcrypt**, **scrypt**, or **Argon2** for password hashing
- **BLAKE2** for high-performance applications

### Examples (MD5)

| Input | Output (MD5 hash) |
|--------|-------------------|
| `a` | `0C C1 75 B9 C0 F1 B6 A8 31 C3 99 E2 69 77 26 61` |
| `b` | `92 EB 5F FE E6 AE 2F EC 3A D7 1C 77 75 31 57 8F` |
| `1 GB file` | `1D B9 DF A8 F2 7E 36 37 7C 07 5A C0 B8 4C 08 F3` |

Notice that even for extremely large files, the output has the same fixed size.

> **Note**: These examples demonstrate MD5's properties but remember that MD5 should not be used in production systems due to security vulnerabilities.

![Hash Functions.png](img/hashFunctions.png)

---

### Fundamental Properties

| Property | Description |
|-----------|--------------|
| **Deterministic** | The same input always produces the same output. |
| **Fast Computation** | Hashing is designed to be efficient and quick. |
| **Pre-Image Resistance** | Hard to find `M` given `H(M)`. |
| **Collision Resistance** | Hard to find `M` and `M′` such that `H(M) = H(M′)`. |
| **Puzzle Friendly** | Slightly changing the input leads to unpredictable outputs, useful for proof-of-work systems. |

---

### Visual Summary

```txt
    ┌───────────────────────┐
    │       Message (M)     │
    └───────────────────────┘
                │
                ▼
         h = H(M)   →  Hash Function
                │
                ▼
    ┌────────────────────────────┐
    │   Message Digest (hash h)  │
    └────────────────────────────┘
```

- It’s **easy** to go from message → hash.  
- It’s **impossible** to go back from hash → message.

---

**Summary:**
Hash functions are the backbone of data integrity, authentication, and digital signatures.  
Their strength lies in being **one-way, collision-resistant, and deterministic**, ensuring that even the smallest change in data is immediately detectable.

## When to use Hash Functions?

- **Data Integrity**: Verify that data has not been altered during transmission or storage.
- **Digital Signatures**: Create a unique fingerprint of a message to sign it securely.
- **Password Storage**: Store hashed passwords instead of plaintext to enhance security.
- **Proof-of-Work Systems**: Use in blockchain and cryptocurrency mining to ensure computational effort.
- **File Verification**: Check file integrity after downloads or transfers.
- **Extend/Reduce secret values** to a predefined size
  - Password: 123abc456 → Adding a padding to 20 bytes will not be secure (the attacker can see the padding and brute-force the password)
  - Key derivation functions (KDFs) are used to derive keys from passwords
  - For this usecase, salt is useless but we can hash it because will result in a fixed size

### Birthday Paradox and Collisions

- Hash functions can produce collisions, where two different inputs yield the same hash output.
- The **birthday paradox** illustrates that in a group of people, the probability of two sharing a birthday is surprisingly high. Similarly, in hash functions, as more inputs are hashed, the chance of collisions (two different inputs producing the same hash) increases.
- For a hash function with an `n`-bit output, the probability of a collision becomes significant after hashing about `2^(n/2)` different inputs.
- Hash functions can produce collisions, where two different inputs yield the same hash output. The **Birthday Paradox** provides a mathematical foundation for understanding collision probability in hash functions.

### Understanding the Birthday Paradox

#### Step 1: The Classic Birthday Problem

The Birthday Paradox states that in a room of just 23 people, there's a **50% probability** that two people share the same birthday. This seems counterintuitive because there are 365 possible birthdays.

#### Step 2: Mathematical Foundation

For **N different possibilities** of an event to happen, you need approximately **√N random items** for them to have a 50% chance of collision.

**Formula:** √N ≈ 2^(n/2) where n is the number of bits

#### Step 3: Birthday Examples

- **365 different birthdays**: √365 ≈ 19.1 → **~23 people** needed for 50% collision chance
- **Different scenario**: To have 50% probability that someone shares **your specific birthday**, you need **253 people** in the room

#### Step 4: Application to Hash Functions

The Birthday Paradox directly applies to hash function vulnerabilities:

1. **For an n-bit hash function**: A collision can be found in approximately **2^(n/2)** tries
2. **MD5 example**: 128-bit output → collision in **2^64** attempts (not 2^128)
3. **SHA-256 example**: 256-bit output → collision in **2^128** attempts (not 2^256)

#### Step 5: Birthday Attack Mechanism

The **birthday attack** exploits this paradox:

```txt
1. Generate many random messages M₁, M₂, M₃, ...
2. Calculate their hashes: H(M₁), H(M₂), H(M₃), ...
3. Continue until H(Mᵢ) = H(Mⱼ) for i ≠ j
4. Success! Found collision: H(Mᵢ) = H(Mⱼ) where Mᵢ ≠ Mⱼ
```

#### Step 6: Security Implications

- **Hash strength is halved**: An n-bit hash has only **n/2 bits** of collision resistance
- **Attack efficiency**: Birthday attacks find collisions with ~50% probability much faster than brute force
- **Real-world impact**: This is why MD5 (128-bit) is considered broken - only **64 bits** of effective security

### Visual Representation

```txt
Hash Function Security:
    Theoretical: 2ⁿ operations needed
    Reality:     2^(n/2) operations needed (Birthday Attack)

Examples:
    MD5 (128-bit):    2^128 → 2^64 operations
    SHA-1 (160-bit):  2^160 → 2^80 operations  
    SHA-256 (256-bit): 2^256 → 2^128 operations
```

### Key Takeaways

- Hash functions have **half their advertised security** due to birthday attacks
- **Collision resistance** is the limiting factor, not pre-image resistance
- Modern hash functions must account for birthday attacks in their design
- This is why cryptographers recommend hash functions with **double the desired security level**

## Hash Use Cases

![Usage 1](img/Usage1.png)
![Usage 2](img/Usage2.png)
![Usage 3](img/Usage3.png)

### Hash Functions in Password Storage

- Storing passwords in plaintext is a major security risk.

```txt
Database Table: users

1. 0 Security
┌──────────┬──────────────────┐
│ username │ password         │
├──────────┼──────────────────┤
│ john     │ 123456           │
│ alice    │ qwerty           │       
└──────────┴──────────────────┘

Problem: Passwords stored in plaintext
   - Anyone with database access sees all passwords
   - Data breaches expose user credentials directly
   - No protection against insider threats

2. Hashed Passwords
┌──────────┬──────────────────┐
│ username │ password         │
├──────────┼──────────────────┤
│ john     │ hash1            │
│ alice    │ hash2            │
│ bob      │ hash1            │
└──────────┴──────────────────┘

Problem: Conflict of the passords
   - Two users (john and bob) have the same password (123456)
   - Both passwords hash to the same value (hash1)
   - An attacker who knows one password can compromise both accounts
   - If Alice has a good password, she is safe because cannot reverse the hash

3. Salted Passwords
   - Adding "ICM" as salt to each password before hashing
┌──────────┬──────────────────┐
│ username │ password         │
├──────────┼──────────────────┤
│ john     │ hash1            │
│ alice    │ hash2            │
│ bob      │ hash1            │
└──────────┴──────────────────┘

- There is still a conflict between john and bob
- Now we can make sure that it is not in the list of the week passwords -> admin1234 becomes admin1234ICM

4. Random Salted Passwords
   - Each user gets a unique random salt
┌──────────┬──────────────────┬───────────────┐
│ username │ password         │     salt      │
├──────────┼──────────────────┼───────────────┤
│ john     │ hash1            │ random_salt1  │
│ alice    │ hash2            │ random_salt2  │
│ bob      │ hash3            │ random_salt3  │
└──────────┴──────────────────┴───────────────┘

Status: No conflicts, even if passwords are the same
   - Each password is combined with a unique random salt before hashing
   - Even if two users have the same password, their hashes will differ due to different salts
   - An attacker cannot use precomputed hash tables (rainbow tables) effectively
   - Significantly increases security against brute-force and dictionary attacks
   - Uses puzzle-friendly property of hash functions to make guessing passwords computationally expensive
```

- **Salting** adds a unique random value to each password before hashing, ensuring that even identical passwords produce different hashes.

![Password Storage](img/PasswordStorage.png)

## Message Authentication Codes (MACs)

**Scenario**: Alice wants to send a message `M` to Bob and ensure its **integrity** and **authenticity**. However, an attacker (Vader) can intercept and modify messages in the communication channel.

### The Problem: Simple Hash Functions Aren't Enough

#### **Attempt 1: No Protection**

- Alice sends: "Meet me at 10 AM"
- **Problem**: Vader intercepts and changes it to "Meet me at 10 PM"
- Bob receives the modified message with no way to detect tampering

#### **Attempt 2: Adding Hash Values**

- Alice sends: Message + `H(M)` (hash of the message)
- If Vader modifies the message, the hash won't match the content
- **Problem**: Vader can simply:
   1. Change the message to "Meet me at 10 PM"
   2. Compute a new hash for the modified message
   3. Send the modified message with the new hash
- Bob still cannot detect the tampering

### The Solution: Message Authentication Codes (MACs)

**MACs** solve this problem by protecting the hash itself using a **secret key** known only to Alice and Bob.

#### **Secure Approach: Using MACs**

**MAC Formula**: `MAC = E(K, H(M))`

- `H(M)` = Hash of the message
- `K` = Secret key shared between Alice and Bob
- `E(K, H(M))` = Encryption of the hash using the secret key

#### How MAC Protection Works

1. **Alice's Process**:

   ```txt
   Original Message: "See you at 10 AM"
   Step 1: Compute hash → H(M)
   Step 2: Encrypt hash with secret key → MAC = E(K, H(M))
   Step 3: Send message + MAC
   ```

2. **Vader's Attack (Fails)**:

   ```txt
   Intercepted: "See you at 10 AM" + MAC
   Step 1: Change message → "See you at 10 PM"
   Step 2: Compute new hash → H(M')
   Step 3: Cannot create valid MAC (doesn't know secret key K)
   Problem: Invalid MAC will be detected by Bob
   ```

3. **Bob's Verification**:

   ```txt
   Received: Message + MAC
   Step 1: Compute hash of received message → H(M_received)
   Step 2: Decrypt MAC using secret key → E⁻¹(K, MAC)
   Step 3: Compare decrypted hash with computed hash
   Result: If they match → message is authentic and unaltered
   ```

#### Visual Flow Diagram

```txt
ALICE (Sender)                    COMMUNICATION CHANNEL                    BOB (Receiver)
┌─────────────────┐                                                    ┌─────────────────┐
│ Original Message│                                                    │ Received Message│
│ "Meet at 10 AM" │                                                    │ "Meet at 10 AM" │
└─────────────────┘                                                    └─────────────────┘
       │                                                                       │
       ▼                                                                       ▼
┌─────────────────┐                                                    ┌─────────────────┐
│   H(Message)    │                                                    │   H(Received)   │
│     [hash]      │                                                    │     [hash]      │
└─────────────────┘                                                    └─────────────────┘
       │                                                                       │
       ▼                                                                       ▼
┌─────────────────┐               ┌─────────────────────┐              ┌─────────────────┐
│ E(K, H(Message))│ ──────────▶   │   Message + MAC     │ ──────────▶  │ E⁻¹(K, MAC)     │
│      [MAC]      │               │  sent over channel  │              │   [decrypt]     │
└─────────────────┘               └─────────────────────┘              └─────────────────┘
                                 │                                    │
                                 ▼                                    ▼
                          ┌─────────────────┐                 ┌─────────────────┐
                          │  VADER ATTACK   │                 │    COMPARE:     │
                          │ ❌ Cannot forge  │                 │ H(Received) ==  │
                          │ valid MAC       │                 │ Decrypted MAC?  │
                          │ (no secret key) │                 │                 │
                          └─────────────────┘                 └─────────────────┘
                                                            │
                                                            ▼
                                                   ┌─────────────────┐
                                                   │ ✅ MATCH =      │
                                                   │ Message Valid   │
                                                   │ ❌ NO MATCH =   │
                                                   │ Tampering Found │
                                                   └─────────────────┘
```

#### Attack vs. Defense Comparison

| Scenario | Attacker Capability | MAC Protection | Result |
|----------|-------------------|----------------|---------|
| **No Protection** | Modify message freely | None | ❌ Attack succeeds |
| **Hash Only** | Modify message + recompute hash | None | ❌ Attack succeeds |
| **MAC Protection** | Modify message | Cannot forge MAC without key K | ✅ Attack detected |

### Key Security Properties

| Property | Description |
|----------|-------------|
| **Integrity** | Any modification to the message will be detected |
| **Authenticity** | Only someone with the secret key can create valid MACs |
| **Non-repudiation** | Proves the message came from the claimed sender |

### Why MACs Are Secure

- **Secret Key Protection**: Attackers cannot forge valid MACs without the secret key `K`
- **Cryptographic Strength**: Based on the security of the underlying hash function and encryption
- **Tamper Detection**: Any message modification results in MAC verification failure

![MAC](MAC.png)

**Summary**: MACs provide both **data integrity** and **message authentication** by combining hash functions with secret key cryptography, making it impossible for attackers to modify messages without detection.

#### MAC-Based Intrusion Detection System

**Message Authentication Codes** can be effectively used to detect unauthorized file modifications in computer systems, providing a robust intrusion detection mechanism.

##### How It Works

1. **Initial Setup**
   - Alice (system administrator) has a secret key `K`
   - She computes MAC values for all critical system files: `MAC(M,K)` where `M` is each file
   - These MAC values are stored securely (not on the monitored system)

2. **Normal Operation**
   - Files remain unchanged
   - MAC verification confirms file integrity
   - System operates normally

3. **Intrusion Detection**
   - An attacker gains access and modifies files
   - Alice periodically recomputes MAC values for all files
   - If any MAC value differs from the stored baseline, it indicates file tampering
   - **Critical advantage**: The attacker cannot forge valid MAC values without the secret key `K`

##### Security Benefits

- **Tamper Detection**: Any file modification is immediately detectable
- **Cryptographic Protection**: Unlike simple checksums, MACs cannot be easily forged
- **Selective Monitoring**: Can focus on critical system files, configuration files, and executables
- **Non-Repudiation**: Provides proof that unauthorized changes occurred

##### Implementation Considerations

- MAC values must be stored in a secure location (separate from monitored system)
- Regular verification cycles to detect intrusions promptly
- Key management is critical - the secret key must remain confidential
- Suitable for monitoring static files that shouldn't change frequently

This use case demonstrates how cryptographic hash functions with authentication extend beyond data transmission to provide comprehensive system security monitoring.

### HMAC - Hash-based Message Authentication Code

**HMAC (Hash-based Message Authentication Code)** is a specific construction for creating message authentication codes using cryptographic hash functions. Unlike the general MAC approach we discussed earlier, HMAC provides a standardized, provably secure method that's widely adopted in practice.

#### HMAC Construction - RFC 2104

HMAC cleverly combines a secret key with a hash function to create a secure authentication mechanism. The construction uses two rounds of hashing with specially prepared key material.

**HMAC Formula:**

```txt
HMAC(K, M) = H((K ⊕ opad) || H((K ⊕ ipad) || M))
```

**Where:**

- **H** is a cryptographic hash function (e.g., SHA-256)
- **K** is the secret key, properly sized for the hash function
- **M** is the message to be authenticated
- **||** denotes concatenation
- **⊕** denotes exclusive OR (XOR)
- **opad** is the outer padding (0x5C5C...5C5C, repeated to block size)
  - Used in the second hash operation to create cryptographic separation
  - The constant 0x5C provides a fixed, known pattern that ensures the outer hash operates on different data than the inner hash
- **ipad** is the inner padding (0x3636...3636, repeated to block size)
  - Used in the first hash operation with the original message
  - The constant 0x36 creates a distinct environment for the inner hash computation
- **Purpose of different padding**: The two different constants (0x36 and 0x5C) ensure that the inner and outer hash operations process fundamentally different inputs, preventing cryptographic attacks that could exploit similarities in the hashing process

#### HMAC Algorithm Steps

1. **Key Preparation**
   - If key length > hash block size: `K = H(original_key)`
   - If key length < hash block size: pad `K` with zeros to reach block size

2. **Inner Hash Computation**
   - Compute `i_key_pad = K ⊕ ipad`
   - Compute `inner_hash = H(i_key_pad || message)`

3. **Outer Hash Computation**
   - Compute `o_key_pad = K ⊕ opad`
   - Compute `HMAC = H(o_key_pad || inner_hash)`

#### Visual HMAC Process

```txt
┌─────────────────────────────────────────────────────────────────────────┐
│                           HMAC Construction                             │
└─────────────────────────────────────────────────────────────────────────┘

Step 1: Key Preparation
┌─────────┐    ┌──────────────┐    ┌─────────────────┐
│   Key   │ ──▶│ Pad/Hash to  │ ──▶│  Prepared Key   │
│         │    │  Block Size  │    │   (64 bytes)    │
└─────────┘    └──────────────┘    └─────────────────┘

Step 2: Inner Hash (First Pass)
┌─────────────────┐    ┌─────┐    ┌─────────────────┐    ┌──────────────┐
│  Prepared Key   │    │ XOR │    │      ipad       │    │              │
│   (64 bytes)    │ ──▶│     │◀── │ (0x36 repeated) │ ──▶│ SHA1 - 1st   │
└─────────────────┘    └─────┘    └─────────────────┘    │    pass      │
                                           │              │
┌─────────────────┐                                      │              │
│    Message      │ ────────────────────────────────────▶│              │
└─────────────────┘                                      └──────────────┘
                                                │
                                                ▼
                                           ┌──────────────┐
                                           │ Hash Sum 1   │
                                           │  (20 bytes)  │
                                           └──────────────┘

Step 3: Outer Hash (Second Pass)
┌─────────────────┐    ┌─────┐    ┌─────────────────┐    ┌──────────────┐
│  Prepared Key   │    │ XOR │    │      opad       │    │              │
│   (64 bytes)    │ ──▶│     │◀── │ (0x5C repeated) │ ──▶│ SHA1 - 2nd   │
└─────────────────┘    └─────┘    └─────────────────┘    │    pass      │
                                           │              │
┌──────────────┐                                         │              │
│ Hash Sum 1   │ ────────────────────────────────────────▶│              │
│  (20 bytes)  │                                         └──────────────┘
└──────────────┘                                                │
                                                ▼
                                           ┌──────────────┐
                                           │ Hash Sum 2   │
                                           │   (HMAC)     │
                                           │  (20 bytes)  │
                                           └──────────────┘
```

#### Understanding HMAC in Simple Terms

**HMAC (Hash-based Message Authentication Code)** is a method to make sure that:

1. A message really comes from the person you expect (authenticity), and  
2. The message hasn’t been changed (integrity).

Think of HMAC as a **message locked with two layers of security**.

---

#### The Analogy – A Box with Two Locks

Imagine you want to send a message to your friend Bob.  
You don’t want anyone to:

- change the message, or  
- pretend to be you.

So, you put your message in a box and lock it with **two locks**:
one inside and one outside.  
That’s exactly what HMAC does — two layers of hashing security.

---

#### How HMAC Works Step by Step

#### Step 1 – Prepare the Key

You and Bob share a **secret key** (like a password).  
HMAC first adjusts this key to the right length (usually 64 bytes)  
so that it fits perfectly into the “lock mechanism” (the hash function).

---

#### Step 2 – The First Lock (Inner Hash)

1. Mix your key with a special pattern called **ipad** (hex value `0x36` repeated).  
   → Think of it as *Lock Pattern A*.  
2. Combine this with the **message** you want to send.  
3. Pass the whole thing through a **hash function** (like SHA-256).  
   → The result is your **first lock**, a hash that mixes the secret and the message.

---

#### Step 3 – The Second Lock (Outer Hash)

1. Take the same key, but this time mix it with a different pattern: **opad** (`0x5C` repeated).  
   → Think of it as *Lock Pattern B*.  
2. Combine it with the result from the first step (the first hash).  
3. Hash it again.

The output is your final **HMAC** — a *double-locked digital signature* of your message.

---

#### Why Two Locks?

Because the double hashing:

- Hides the real secret key (nobody can recover it from the HMAC).  
- Protects against certain attacks on simple hashes (like length extension attacks).  

A simple hash like `hash(key + message)` can be attacked.  
HMAC’s double-layer (inner + outer) design prevents this.

---

#### What Each Part Represents

| Real-world Analogy | In HMAC |
|---------------------|---------|
| Secret key (you & Bob share it) | The password-like shared secret |
| Inner lock | First hash using `ipad` |
| Outer lock | Second hash using `opad` |
| Final HMAC | The “double-locked box” — a unique signature for the message |

---

#### The Technical Formula

\[
\text{HMAC}(key, message) = hash((key ⊕ opad) ∥ hash((key ⊕ ipad) ∥ message))
\]

Where:

- `⊕` = XOR operation  
- `∥` = concatenation  
- `ipad` = inner pad (`0x36` repeated)  
- `opad` = outer pad (`0x5C` repeated)

---

#### Summary

- **HMAC** is a way to *authenticate* and *protect* messages.  
- It uses a **shared secret key** and **two layers of hashing**.  
- The result is a unique digital fingerprint that only someone with the key can produce.  

**In short:**  
> HMAC is like putting your message in a box, locking it twice with two different locks, and sending it to someone who has the same keys to verify it.

#### Why This Two-Round Construction?

The nested structure of HMAC provides several critical security properties:

1. **Prevents Length Extension Attacks**: Simple constructions like `H(K || M)` are vulnerable to attacks where an adversary can append data and compute valid MACs
2. **Key Recovery Resistance**: The double hashing with different padding makes it computationally infeasible to recover the secret key
3. **Collision Resistance**: Even if the underlying hash function has some weaknesses, the HMAC construction maintains security
4. **Formal Security Proof**: HMAC's security can be mathematically proven based on the security of the underlying hash function

#### Security Properties

| Property | HMAC Guarantee |
|----------|----------------|
| **Unforgeability** | Cannot create valid MACs without the secret key |
| **Key Recovery Resistance** | Computationally infeasible to determine K from HMAC values |
| **Collision Resistance** | Inherits and strengthens the hash function's collision resistance |
| **Length Extension Immunity** | Protected against common hash function vulnerabilities |

#### Common HMAC Variants

- **HMAC-MD5**: Uses MD5 (deprecated due to MD5 weaknesses)
- **HMAC-SHA1**: Uses SHA-1 (still secure for HMAC despite SHA-1 collision vulnerabilities)
- **HMAC-SHA256**: Uses SHA-256 (current gold standard)
- **HMAC-SHA3**: Uses SHA-3 (newest standard)

#### Real-World Applications

- **TLS/SSL**: Used for message integrity in secure web connections
- **JWT**: JSON Web Tokens use HMAC for signature verification
- **API Authentication**: Many REST APIs use HMAC for request signing
- **IPSec**: Network layer security protocol authentication
- **Git**: Some Git operations use HMAC for integrity verification

**Key Insight**: HMAC's elegant design transforms any cryptographic hash function into a secure message authentication code, providing both theoretical security guarantees and practical robustness against real-world attacks.

### CBC-MAC (Cipher Block Chaining Message Authentication Code)

---

#### Overview

**CBC-MAC** is a technique used to generate a **Message Authentication Code (MAC)** using a **block cipher** in **Cipher Block Chaining (CBC)** mode.  
It ensures the **integrity and authenticity** of a message — confirming that it has not been altered and that it comes from the expected sender.

---

#### Core Idea

CBC-MAC creates a **chain of encrypted blocks**, where each block depends on the encryption of the previous one.  
By chaining the blocks together, any modification in the message affects the final output, making forgery nearly impossible without knowing the secret key.

The final encrypted block (the last ciphertext block) becomes the **MAC tag** — a short, fixed-length value used to verify message authenticity.

---

#### Step-by-Step Explanation

Let’s assume a message is divided into blocks:  
`M = (m1, m2, ..., mx)` — each block having the same size as the block cipher (e.g., 128 bits).

1. **Initialization**  
   - Start with an **initial vector (IV)** of all zeros:  
     `IV = 0`.

2. **Encryption Process**  
   - For each message block `mi`:
     - XOR (`⊕`) the message block with the **previous ciphertext block** (or IV for the first block).  
     - Encrypt the result with the **secret key k** using a block cipher (for example, AES).  
     - The output becomes the next input for the following block.

   In formula form:

   C0 = 0

   Ci = E_k (mi ⊕ C_{i-1})
   - Repeat this for all blocks `i = 1 to x`.

3. **Final Output**
   - The final ciphertext block `Cx` is the **CBC-MAC tag** for the
   entire message.
   - This tag is sent along with the message for verification.

**Visual Representation**

   ```txt
      m1          m2                 mx
       |           |                  |
       ⊕           ⊕                  ⊕
       0 ——> E_k ——> E_k ——> … ——> E_k ——> result (MAC)
   ```

   Where:

- `m1, m2, ..., mx` are the message blocks
- `⊕` represents XOR operation
- `E_k` is encryption with secret key `k`
- `0` is the initial vector (all zeros)
- The final output is the CBC-MAC tag
