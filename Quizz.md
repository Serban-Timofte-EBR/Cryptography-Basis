# Quiz on Cryptography Basics

R: A login process to authentificate users in a health application.
Q: Hashing & salt

R: Secure file sharing. Verify users identity before sharing files
Q: Digital signatures
OBS: Assymetric is not enough cause you are encrypting files, but cannot verify identity without signatures (everybody can use the public key to encrypt files)

R: Financial app: Configential transactions between users, Integrity, Non-repudiation
Q: Hash for data integrity
Q: Public Key Encryption for confidentiality
Q: Symetric Encryption for transaction data confidentiality
Q: Multi FA auth

R: Cloud Storage: Store files securely, confidential for cloud provider, encryption in tranzit and in rest and the can verify the identity of the sneder. Files confidential to the cloud provider
Q: Digital Certificates for mutual authentificat between used
Q: Asymentric Encryption for key exchange and digital
Q: Symetric Encryption for encryption during transmission and storage

R: SHA-1 hash in bytes: 20

R: strong in bits for a passowrd:
    - has a zise of 10 chars
    - uses digits from 0 to 9
    - uses small cap letter
    - use only !

- Unique values: 10 + 26 + 1 = 37
- 10 * log2(37) = 10 * 5.209 = 52 bits

R: Number of security bits tells the security of a system

R: Encryption and authentification
Q: Hybrid Encryption (Combination of AES and RSA)

R: Payment details cannot be altered during transmission
Q: Hashing for data integrity

R: Messaging app between users are confidencial and sender cannot deny later sanding the message
Q: Symmetric for confidentiality and Digital Signatures for non-repudiation