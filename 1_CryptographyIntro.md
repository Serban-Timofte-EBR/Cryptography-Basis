# Introduction in Cryptography

## Concepts

1. Cyrptography - secret writing science - science of information security
2. Cryptoanlysis - breaking ciphertext without knowing cipher code
3. Cryptology - mathematic field that studies the mathematicall fundaments of cryptography
4. Steganography - the art of hiding information

## Cryptography

- Secret writing science

- Is NOT:
  - A solution of all security issues (e.g. social engineering)
  - A solution of desing errors / software bugs

- **🚨 Warning:** <span style="color: red">Never trust proprietary cryptographic solutions!</span>

## Standards - Intro

### RFC 4949

[RFC 4949](https://datatracker.ietf.org/doc/html/rfc4949#page-9), officially titled "Internet Security Glossary, Version 2", is an Internet Engineering Task Force (IETF) publication that serves as a glossary of definitions, abbreviations, and explanations of terminology related to information system security.

### Cybersecurity Dictionary

- Alice & Bob -> main actors to explain a flow

- Workflow:

```txt
Original information  -------------------------------------> Cipher Text
    Message M           Encryption Algorithm / System       Encrypted Text
    Clear text
    Plain text
```

- Functions: Encryption / Decryption -> Encipher / Decipher

- Encryption Key is used during Encryption

- Decryption Key is used during Decryption

- The communication of information is performed via Communication System - DO NOT TRUST IT

```txt
Alice --> Plain text --> Cipher Text ==============> Cipher Text --> Plain Text --> Bob
                                     Communication 
                                     Channel
```

#### Classification

- Algorithms: Hash Functions, Symmetric, Asymmetric

- Symmetric Algorithm: Processing: Stram ciphers, Block ciphers

#### Vulnerabilities

- Weak Passowrd
  - Brute force always works but depends on the time: 1 minute or 20 years?

- Lack of Regular Updates and Patches

- Access Control

- Insufficient or not encryption

- Social Engineering

- Ensecured Apps / Endpoints

- Poor Incident Response Plan
