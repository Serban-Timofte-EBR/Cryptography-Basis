# Attack

## Types 1

- There are 2 types of attack

### Passive Attacks

- Attacker does not change anything

- Scope: Collect information -> Like what type of firewall a company uses? Scan Network Jobs at that company

1. Evesdropping
    - The attackers are collecting data from communication channel
    - Common for WiFi (WiFi broadcast all the communication to everywhere) and also automotive industry recently
    - Mitigation: Encrypt the communication on the channel

2. Information Gathering / Reconnaissance
    - A reconnaissance attack is any effort by a hacker to gather information about a target before launching a more significant attack. An example is a port scanning attack, where a hacker uses tools like Nmap to find open ports on a network's IP addresses, identifying potential services and entry points to exploit later.
    - Other examples include social engineering to gather information from employees, or Open-Source Intelligence (OSINT) by searching public records and social media

3. Passive DNS
    - It easy to track where you are going on the internet
    - Machines works with IP addresses, while humans works with the domain names - DNS makes this connection
    - Could see where the traffic is going, but not the content - can see that you are changing webstudent.ase.ro but not the grades => The attacker will know that you are a student at ASE
    - A solution is to use 1111 (Google DNS) but will result in lack of performance -> DNS admin still have access
    - Mitigation: Network Segmantation, Encryption, Awarness

### Active Attacks

- Attacker actively interferes with or modifies the communication
- Scope: Alter, inject, or manipulate data in transit

1. **Replay Attack**
    - **Definition**: An attacker captures and retransmits previously sent legitimate messages
    - **How it works**:
      - Alice sends an encrypted message to Bob (e.g., "Transfer $100 to account X")
      - An attacker intercepts this encrypted message
      - Later, the attacker resends the same encrypted message to Bob
      - Bob receives what appears to be a legitimate message from Alice and processes it again => Will make a new transaction of $100 to account X
    - **Real-world example**: Replaying a bank transfer command to execute the same transaction multiple times
    - **Key insight**: Even though the attacker cannot decrypt the message, they can still cause damage by replaying it
    - **Mitigation strategies**:
      - Use timestamps in messages
      - Implement sequence numbers
      - Use nonces (numbers used only once)
      - Implement session tokens that expire

2. **Tampering Attack**
    - **Definition**: An attacker intercepts and modifies data in transit without the knowledge of sender or receiver
    - **How it works**:
      - Attacker positions themselves between sender and receiver (Man-in-the-Middle)
      - Intercepts the original message
      - Modifies the content, headers, or parameters (in general headers)
      - Forwards the altered message to the intended recipient
    - **Real-world example**: Changing the amount in a payment request from $10 to $1000, or modifying DNS responses to redirect traffic to malicious sites
    - **Key insight**: The integrity of the message is compromised, leading to incorrect or malicious actions
    - **Mitigation strategies**:
      - Use digital signatures to verify message integrity
      - Implement message authentication codes (MAC)
      - Use end-to-end encryption
      - Deploy integrity checking mechanisms like checksums or hashes

3. **Distributed Denial of Service (DDoS) Attack**
    - **Definition**: An attacker overwhelms a target system or network with a flood of traffic from multiple sources, making it unavailable to legitimate users
    - **How it works**:
      - Attacker controls a network of compromised devices (botnet)
      - Coordinates these devices to simultaneously send massive amounts of requests to the target
      - Target system becomes overloaded and cannot respond to legitimate requests
      - Service becomes unavailable or significantly degraded for normal users
    - **Real-world example**: Flooding a company's website with millions of fake requests, causing it to crash and preventing customers from accessing online services
    - **Key insight**: The attack focuses on availability rather than confidentiality or integrity
    - **Mitigation strategies**:
      - Implement rate limiting and traffic filtering
      - Use content delivery networks (CDNs) for traffic distribution
      - Deploy DDoS protection services and firewalls
      - Implement load balancing and redundancy
      - Monitor network traffic for unusual patterns

4. **Injection Attack**
    - **Definition**: An attacker inserts malicious code or commands into an application's input fields, which are then executed by the system
    - **How it works**:
      - Attacker identifies input fields that are not properly validated or sanitized
      - Crafts malicious input containing code or commands
      - Application processes the input without proper filtering
      - Malicious code gets executed with the application's privileges
    - **Real-world example**: SQL injection where an attacker enters `'; DROP TABLE users; --` in a login form, causing the database to delete the users table
    - **Key insight**: Exploits the trust between user input and application processing
    - **Mitigation strategies**:
      - Use parameterized queries and prepared statements
      - Implement input validation and sanitization
      - Apply the principle of least privilege
      - Use web application firewalls (WAF)
      - Regular security testing and code reviews

5. **Man-in-the-Middle (MitM) Attack**
    - **Definition**: An attacker secretly intercepts and potentially alterss communications between two parties who believe they are communicating directly with each other
    - **How it works**:
      - Attacker positions themselves between the sender and receiver
      - Intercepts all communication flowing between the parties
      - Can read, modify, or inject new messages -> IMPORTANT: It has full control
      - Both parties remain unaware of the attacker's presence
    - **Real-world example**: An attacker on a public WiFi network intercepting login credentials, or creating a fake WiFi hotspot to capture all traffic from connected devices
    - **Key insight**: Compromises confidentiality and integrity by breaking the direct communication channel
    - **Mitigation strategies**:
      - Use end-to-end encryption (HTTPS, TLS/SSL)
      - Implement certificate pinning
      - Avoid public WiFi for sensitive communications
      - Use VPNs for secure connections
      - Verify SSL/TLS certificates before transmitting sensitive data
      - Important: OpenSSL ensures that the Public Key is valid (locally on computers)

#### Tamper Protection Levels

##### Tamper

- **Definition**: To interfere with or make unauthorized changes to something
- **Example**: Changing the expiration date on a food package or modifying the odometer reading on a car

##### Tamper-Evident

- **Definition**: Designed to show visible signs when someone has attempted to access or modify it
- **Examples**:
        - Sealed medicine bottles with plastic wrap that breaks when opened
        - Security tape that shows "VOID" pattern when removed
        - Wax seals on documents that crack when broken
- **Purpose**: Doesn't prevent tampering but makes it obvious that tampering occurred

##### Tamper-Resistant

- **Definition**: Designed to withstand or make tampering attempts significantly more difficult
- **Examples**:
        - Smart cards with embedded chips that are hard to extract or modify
        - Secure USB drives with encrypted storage
        - ATM machines with reinforced casings and multiple security layers
- **Purpose**: Actively resists tampering attempts and may destroy sensitive data if breach is detected

## Type - What attacker knows

- Knows: Encryted Messages and the Algorithm -> from libraries (Safe)
- Knows: Plain-text + Cipher Text
  - Extracting text from a YubiKey device

1. **Side Channel Attack**
    - **Definition**: An attacker exploits information leaked through the physical implementation of a cryptographic system rather than weaknesses in the algorithms themselves
    - **How it works**:
      - Attacker measures physical phenomena during cryptographic operations
      - Analyzes patterns in power consumption, electromagnetic emissions, timing, or acoustic signatures
      - Correlates these physical measurements with the secret data being processed
      - Extracts cryptographic keys or sensitive information without directly breaking the encryption
    - **Real-world example**: Measuring the power consumption of a smart card during encryption operations to determine the secret key, or analyzing the time it takes for a system to process different passwords to guess the correct one
    - **Key insight**: Exploits the gap between theoretical cryptographic security and physical implementation vulnerabilities
    - **Mitigation strategies**:
      - Implement constant-time algorithms that don't vary execution time based on input
      - Use power analysis countermeasures like noise injection or power line filtering
      - Add random delays to operations to mask timing patterns
      - Implement electromagnetic shielding for sensitive devices
      - Use masking techniques to randomize intermediate values during computation
      - Physical security measures to prevent direct access to devices during operation
