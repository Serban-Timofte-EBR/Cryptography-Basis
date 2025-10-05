# Protocols

In cryptography, protocols are structured sequences of steps that define how parties should interact to achieve a specific security goal. These protocols ensure secure communication, authentication, key exchange, and other cryptographic operations while maintaining properties like confidentiality, integrity, and authenticity.

## Arbitrated Protocols

- **Definition**: Protocols that rely on a trusted third party (arbitrator) to facilitate and oversee the communication between participants
- **Key Characteristics**:
  - The arbitrator is trusted by all parties involved
  - All communication typically flows through the arbitrator
  - The arbitrator has access to all sensitive information
  - Provides strong security guarantees but creates a single point of failure
- **Examples**:
  - Banking transactions through a central authority
  - Certificate authorities in PKI systems
  - Kerberos authentication protocol
- **Advantages**:
  - High security assurance
  - Dispute resolution capability
  - Simplified protocol design
- **Disadvantages**:
  - Single point of failure
  - Requires absolute trust in the arbitrator
  - Performance bottleneck
  - Privacy concerns (arbitrator sees everything)

## Adjudicated Protocols

- **Definition**: Protocols composed of 2 lower-level sub-protocols that work in conjunction
- **Key Characteristics**:
  - Normal execution doesn't require a third party
  - Adjudicator only intervenes when disputes arise
  - Participants can resolve issues independently in most cases
  - More efficient than arbitrated protocols during normal operation
- **Structure**:
  - **Sub-protocol 1**: Normal execution between parties
  - **Sub-protocol 2**: Dispute resolution involving the adjudicator
- **Examples**:
  - Digital contract signing with dispute resolution
  - Fair exchange protocols
  - Electronic commerce transactions
- **Real-World Example**: **PayPal Transaction System**
  - Normal operation: Buyer and seller complete transactions directly through the platform
  - Dispute resolution: PayPal acts as adjudicator when buyers file claims for non-delivery or fraud
  - The system operates efficiently without intervention in 99%+ of transactions
  - Only requires PayPal's involvement when disputes arise
- **Advantages**:
  - Better performance during normal operation
  - Reduced dependency on third parties
  - Maintains dispute resolution capability
- **Disadvantages**:
  - More complex protocol design
  - Still requires trust in the adjudicator for disputes
  - Potential for protocol interruption

## Self-Enforcing Protocols

- **Definition**: Protocols where participants trust each other and no external authority is needed
- **Key Characteristics**:
  - Mutual trust between all participants
  - No third-party involvement
  - Relies on cryptographic mechanisms for security
  - Participants are responsible for protocol compliance
- **Security Mechanisms**:
  - Cryptographic commitments
  - Zero-knowledge proofs
  - Multi-party computation
  - Game-theoretic incentives
- **Examples**:
  - Peer-to-peer networks
  - Blockchain consensus mechanisms
  - Secret sharing schemes
  - Secure multi-party computation
- **Advantages**:
  - No single point of failure
  - Maximum privacy (no third party sees data)
  - High performance potential
  - True decentralization
- **Disadvantages**:
  - Requires initial trust establishment
  - Vulnerable to collusion
  - Difficulty in dispute resolution
  - Complex cryptographic requirements

## External Environment Considerations

**Threat Model Changes**: Any alteration from outside the protocol environment can significantly impact security:

- **Network Security**: Changes in network infrastructure, eavesdropping capabilities, or man-in-the-middle attacks
- **Computational Advances**: Quantum computing threats, improved factorization algorithms, or increased computational power
- **Regulatory Changes**: New compliance requirements, key escrow laws, or encryption restrictions
- **Participant Behavior**: Compromise of participants, changes in trust relationships, or malicious insider threats
- **Implementation Vulnerabilities**: Side-channel attacks, timing attacks, or software vulnerabilities

**Adaptive Security**: Protocols must be designed to maintain security properties even when the adversarial environment evolves, requiring:

- Forward secrecy mechanisms
- Post-quantum cryptographic resistance
- Robustness against adaptive attacks
- Graceful degradation under compromise

## Coin Flipping Protocols

- **Definition**: Cryptographic protocols that allow two or more mutually distrusting parties to generate a random bit (0 or 1) fairly over a network, where no party can bias the outcome
- **Purpose**: Enables fair randomness generation when parties cannot physically meet or trust each other
- **Key Properties**:
  - **Fairness**: Each outcome has equal probability (50/50)
  - **Unpredictability**: No party can determine the result in advance
  - **Verifiability**: All parties can verify the protocol was executed correctly
  - **Binding**: Once committed, parties cannot change their contributions

### Protocol Mechanics

**Basic Commitment-Based Approach**:

1. **Commitment Phase**: Each party commits to a random bit without revealing it
2. **Reveal Phase**: Parties simultaneously reveal their committed values
3. **Result Computation**: XOR all revealed bits to get the final result
4. **Verification**: All parties verify the commitments match the revealed values

**Cryptographic Components**:

- **Bit Commitment Schemes**: Hash-based or cryptographic commitments
- **Secure Hash Functions**: SHA-256 or similar for commitment binding
- **Digital Signatures**: For non-repudiation of commitments
- **Time-lock Mechanisms**: Prevent premature revelation

### Real-World Example: **Online Poker Tournament Seeding**

**Scenario**: A major online poker platform needs to fairly assign starting positions for 1,000 players in a championship tournament. The seating order determines table assignments and can significantly impact player advantages.

**Implementation**:

- **Participants**: Tournament director and 5 randomly selected players act as "coin flippers"
- **Process**:
  - Each participant generates a 256-bit random number
  - Hash commitments are published simultaneously at 12:00 PM UTC
  - 30 minutes later, all parties reveal their random numbers
  - Numbers are XORed together to create the master seed
  - This seed feeds a deterministic algorithm assigning all 1,000 seats
- **Verification**: All commitments, reveals, and final calculations are publicly auditable
- **Outcome**: No single party (including the house) can manipulate seating arrangements

**Why This Matters**: In high-stakes tournaments with millions in prize money, even small seating advantages can be worth thousands of dollars. This protocol ensures mathematical fairness that all participants can verify.
