# Quantum Computing and Post-Quantum Cryptography

## 1. Quantum Computing – The Current Phase

### 🧠 What is Quantum Computing?

Quantum computing exploits the **principles of quantum mechanics**—superposition and entanglement—to perform computations that would be infeasible for classical computers.

Each quantum bit (qubit) can represent both **0 and 1 simultaneously**, exponentially increasing computational power for certain types of problems (e.g., factoring, discrete logarithms).

---

### 📈 Current Development Status (as of 2024–2025)

- **IBM (2022)** revealed a quantum processor with **433 qubits**.  
  This represents one of the largest superconducting quantum chips built to date.

- The **goal** for the next decade:  
  Build a machine with **100,000 qubits** (IBM, Google, and others have this roadmap).

- Despite these advances, **no current quantum computer** has achieved something **practically useful** that classical supercomputers cannot still perform.

---

### ⚙️ Technical Limitations Today

1. **Too few qubits**  
   → We are far below the scale needed to outperform traditional architectures.

2. **Quantum noise and decoherence**  
   → Even minor *perturbations from the external environment*—like temperature fluctuations, magnetic fields, or cosmic rays—collapse the fragile quantum states.  
   This makes quantum computations unstable.

3. **Error correction overhead**  
   → Quantum error correction requires many physical qubits to form one **logical qubit**.  
   For example, Google’s goal of **1 million qubits** might yield only about **10,000 usable qubits** after correction.

4. **Hardware scaling limitations**  
   - IBM’s qubits are made of **superconducting metal rings** cooled to **millikelvin temperatures**, just above absolute zero.
   - Each superconducting qubit consumes **~65 watts** of power.
   - To operate a 100,000-qubit system, IBM’s VP Jay Gambetta estimated it would require:
     > “Something the size of a building, a nuclear power plant, and a billion dollars.”

   (Source: [MIT Technology Review, 2023](https://www.technologyreview.com/2023/05/25/1073606/ibm-wants-to-build-a-100000-qubit-quantum-computer))

---

### 🔬 Real-World Implications

Even though quantum computers are still in a **pre-industrial phase**, they represent a **paradigm shift**.  
The same physical laws that make them powerful also make them fragile.

| External Factor | Effect on Quantum System |
|------------------|--------------------------|
| Temperature fluctuations | Causes decoherence and loss of quantum state |
| Electromagnetic interference | Introduces phase errors |
| Vibrations or ground motion | Disturbs qubit alignment |
| Cosmic radiation | Random bit flips and transient faults |
| Imperfect shielding | Thermal photons cause instability |

Quantum computing operates in a **delicate equilibrium** between maintaining coherence and isolating the system from the outside world.  
Each alteration of the environment directly **reduces reliability** and **increases error rates**.

---

## 2. Post-Quantum Cryptography (PQC)

### ⚠️ Why It Matters

Quantum computers pose a **direct threat to current cryptographic systems**, particularly those relying on mathematical problems that can be efficiently solved by quantum algorithms.

| Algorithm | Threatened By | Quantum Algorithm |
|------------|---------------|------------------|
| RSA | Integer factorization | Shor’s algorithm |
| ECC (Elliptic Curve Cryptography) | Discrete logarithm problem | Shor’s algorithm |
| DH (Diffie–Hellman) | Discrete logarithm | Shor’s algorithm |

If a sufficiently powerful quantum computer emerges (e.g., ~10,000–20,000 error-corrected qubits), it could break these schemes **within minutes or hours**.

---

### 🔐 Symmetric vs Asymmetric Resilience

- **Symmetric algorithms** (like AES) are *more resistant* to quantum attacks.
  - Grover’s algorithm provides only a **quadratic speed-up**.
  - Thus, doubling the key size restores equivalent security (e.g., AES-256 ≈ AES-128 post-quantum).

- **Asymmetric algorithms** (RSA, ECC) are *vulnerable*.
  - Quantum algorithms can solve their underlying math *exponentially faster*.
  - Therefore, new algorithmic families must replace them.

---

### 🧩 NIST Post-Quantum Cryptography Standardization

To prepare for the quantum era, the **National Institute of Standards and Technology (NIST)** began a competition in **2016** to select algorithms that are **quantum-resistant**.

- **69 candidate algorithms** were initially submitted.  
- In **2022**, the **first set of winners** were announced:

| Category | Algorithm | Description |
|-----------|------------|-------------|
| Public Key Encryption | **CRYSTALS-Kyber** | Based on lattice problems |
| Digital Signatures | **CRYSTALS-Dilithium**, **FALCON**, **SPHINCS+** | Based on lattices / hash-based signatures |

👉 Final standards and recommendations are expected **in 2024–2025**.

References:  

- [NIST PQC Project](https://csrc.nist.gov/Projects/post-quantum-cryptography)  
- [Wikipedia: Post-Quantum Cryptography](https://en.wikipedia.org/wiki/Post-quantum_cryptography)

---

### 🌐 Transition Plan for Modern Cryptosystems

1. **Inventory existing cryptography**  
   → Identify where RSA/ECC are used (TLS, PKI, VPNs, IoT devices).

2. **Hybrid approach**  
   → Combine classical and quantum-safe algorithms during migration.

3. **Key management evolution**  
   → Update hardware security modules (HSMs), key vaults, and certificate infrastructures.

4. **Security-by-design adaptation**  
   → Software and protocols must be redesigned to allow cryptographic agility—switching algorithms without breaking interoperability.

---

### 🧭 Summary

| Aspect | Classical Era | Quantum Era |
|---------|----------------|--------------|
| Computing Unit | Bit (0 or 1) | Qubit (0 and 1 simultaneously) |
| Scaling | Linear | Exponential for specific problems |
| Stability | High | Fragile, sensitive to noise |
| Impact on Security | Predictable | Disruptive—threatens current crypto |
| Countermeasure | Larger keys | Post-Quantum algorithms |

---

### 🧩 Key Takeaways for Students

1. Quantum computers are **not yet practical**, but they **will be** within the next decade.
2. The main obstacle is **environmental stability** — every small change (temperature, vibration, radiation) affects qubit reliability.
3. **Cryptographers must anticipate**, not react: start designing systems **resilient to quantum attacks now**.
4. The **future of cryptography** lies in *mathematical diversity* — moving beyond factoring and discrete logarithms to **lattice-based**, **code-based**, and **hash-based** schemes.
