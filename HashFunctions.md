# Hash Functions

## Algorithm Complexity

- Algorithm complexity is measured by the relationship between input length and processing time

### P vs NP Problem

- **P**: Problems that can be solved in polynomial time
- **NP**: Problems where solutions can be verified in polynomial time
- **Key Question**: If verifying a solution takes polynomial time, how much time is needed to find all possible solutions?

#### Shopping Bag Example

Imagine you're doing your shopping and putting all items in a bag. You tell me the total weight of your bag is 5 kg and ask me to recreate the exact same combination of items without looking inside the bag.

- **Verification (P)**: I can easily verify if my recreated bag weighs 5 kg by putting it on a scale - this takes polynomial time
- **Finding the solution (NP)**: However, finding the exact combination of items that gives 5 kg without seeing inside requires trying potentially exponential combinations of products

This illustrates the P vs NP gap: verifying a solution is easy, but finding it among all possibilities can be computationally expensive.

### Big Numbers in Cryptography

- **Definition**: Big numbers refer to integers with at least 100 digits (much larger than standard data types like `long long`)
- **Storage**: Numbers are managed using arrays or specialized data structures
- **Operations**: Arithmetic uses each digit multiplied by appropriate powers of 10 or base operations

#### Cryptographic Significance

- **RSA Keys**: Typical RSA keys use 2048-bit numbers (approximately 617 decimal digits)
- **Prime Numbers**: Cryptographic security relies on the difficulty of factoring products of large primes
- **Discrete Logarithm**: Many cryptosystems depend on the computational difficulty of solving discrete logarithm problems with large numbers

#### Implementation Challenges

- **Multiplication**: Standard algorithms become inefficient; specialized methods like Karatsuba or FFT-based multiplication are used
- **Modular Arithmetic**: Essential for cryptographic operations, requires efficient modular reduction algorithms
- **Prime Generation**: Testing primality of large numbers requires probabilistic algorithms like Miller-Rabin
- **Memory Management**: Large numbers require careful memory allocation and optimization

#### Performance Considerations

- **Time Complexity**: Operations on n-digit numbers typically require O(n²) time for basic multiplication
- **Space Complexity**: Storage requirements grow linearly with number size
- **Optimization**: Libraries like GMP (GNU Multiple Precision) provide highly optimized implementations

#### Security Implications

- **Key Size**: Larger numbers provide stronger security but require more computational resources
- **Quantum Resistance**: Current large number cryptography may be vulnerable to quantum computers
- **Side-Channel Attacks**: Implementation must protect against timing and power analysis attacks

### Randon Numbers

- Problem: How do we choose these random numbers? Is choosing in a class enough?
- There are tests to determine if some numbers are really random
  - Statistically random, uniform distribution, independent

#### Source: True Random Numbers Generator (TRNG)

- Uses **entropy** to make sure that is completely random

**What is TRNG?**
A True Random Number Generator relies on unpredictable physical phenomena to generate truly random numbers. Think of it like rolling a perfect dice - each outcome is genuinely unpredictable.

**Sources of Entropy (Randomness):**

- **Mouse movements and keyboard timings** - The exact microsecond you move your mouse is unpredictable
- **Atmospheric noise** - Radio static from space and weather
- **Hardware noise** - Electronic components naturally produce random electrical fluctuations
- **Radioactive decay** - The exact moment an atom decays is quantum-mechanically random

**Practical Examples:**

```java
// Example: Using SecureRandom in Java (hardware-based entropy)
import java.security.SecureRandom;

public class TrueRandomExample {
    public static void main(String[] args) {
        // SecureRandom uses the system's entropy pool
        SecureRandom secureRandom = new SecureRandom();
        
        // Generate 16 truly random bytes
        byte[] trueRandomBytes = new byte[16];
        secureRandom.nextBytes(trueRandomBytes);
        
        // Convert to hex for display
        StringBuilder hex = new StringBuilder();
        for (byte b : trueRandomBytes) {
            hex.append(String.format("%02x", b));
        }
        
        System.out.println("True random: " + hex.toString());
    }
}
```

**Real-world Usage:**

- **Generating master encryption keys** for banks and governments
- **Creating SSL certificates** for websites
- **Cryptocurrency wallet seed generation**
- **Gaming industry** for fair lottery systems

**Gotcha:** TRNGs are slow! They might only produce a few hundred random bits per second because they must wait for physical events.

#### Source: Pseudo Random Number Generators (PRNG)

**What is PRNG?**
A Pseudo Random Number Generator uses mathematical algorithms to produce numbers that *appear* random but are actually deterministic. Think of it like a very complex recipe - if you know the recipe (algorithm) and starting ingredient (seed), you can reproduce the exact same "random" sequence.

**How it works:**

```java
// Simple example of Linear Congruential Generator (LCG)
class SimplePRNG {
    private long current;
    
    public SimplePRNG(long seed) {
        this.current = seed;
    }
    
    public long nextRandom() {
        // Formula: (a * current + c) % m
        this.current = (1664525L * this.current + 1013904223L) % (1L << 32);
        return this.current;
    }
}

// Same seed = same sequence!
SimplePRNG prng1 = new SimplePRNG(12345);
SimplePRNG prng2 = new SimplePRNG(12345);

System.out.println("PRNG1: " + prng1.nextRandom());  // Same output
System.out.println("PRNG2: " + prng2.nextRandom());  // Same output
```

**Practical Examples:**

```java
import java.util.Random;

// Java's built-in PRNG
Random random = new Random(42);  // Setting the seed
System.out.println(random.nextInt(100) + 1);  // Always produces: 82
System.out.println(random.nextInt(100) + 1);  // Always produces: 15

// Reset with same seed
random = new Random(42);
System.out.println(random.nextInt(100) + 1);  // Again produces: 82
```

**Cloudflare's Random Beacon Example:**

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.nio.charset.StandardCharsets;
import java.security.MessageDigest;

public class CloudflareRandomBeacon {
    private static final String CLOUDFLARE_BEACON_URL = "https://drand.cloudflare.com/public/latest";
    
    public static void main(String[] args) throws Exception {
        // Fetch random beacon from Cloudflare's drand network
        HttpClient client = HttpClient.newHttpClient();
        HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create(CLOUDFLARE_BEACON_URL))
            .build();
        
        HttpResponse<String> response = client.send(request, 
            HttpResponse.BodyHandlers.ofString());
        
        System.out.println("Cloudflare Random Beacon Response:");
        System.out.println(response.body());
        
        // Extract randomness value (this is publicly verifiable)
        // The beacon provides cryptographically secure randomness
        // that can be verified by anyone using threshold cryptography
        
        // Example: Use beacon randomness as seed for PRNG
        String randomnessHex = extractRandomnessFromJson(response.body());
        long seed = hashToLong(randomnessHex);
        
        Random seededRandom = new Random(seed);
        System.out.println("PRNG seeded with Cloudflare beacon: " + 
                          seededRandom.nextInt(1000));
    }
    
    private static String extractRandomnessFromJson(String json) {
        // Simple extraction - in production use proper JSON parser
        int start = json.indexOf("\"randomness\":\"") + 14;
        int end = json.indexOf("\"", start);
        return json.substring(start, end);
    }
    
    private static long hashToLong(String hex) throws Exception {
        MessageDigest md = MessageDigest.getInstance("SHA-256");
        byte[] hash = md.digest(hex.getBytes(StandardCharsets.UTF_8));
        
        // Convert first 8 bytes to long
        long result = 0;
        for (int i = 0; i < 8; i++) {
            result = (result << 8) + (hash[i] & 0xff);
        }
        return result;
    }
}
```

**Why Cloudflare's drand Beacon?**

- **Lava Lamp Entropy**: Cloudflare uses a wall of lava lamps as one of their entropy sources - the unpredictable bubbling patterns in the lamps provide true randomness that gets fed into their cryptographic systems
- **Publicly Verifiable**: Anyone can verify the randomness using cryptographic proofs
- **Unpredictable**: Generated using threshold cryptography across multiple servers
- **Timestamped**: Each beacon pulse is tied to a specific time (every 30 seconds)
- **Distributed Trust**: No single entity can control or predict the output


**Real-world Usage:**

- **Video game procedural generation** - Minecraft worlds use seeds
- **Simulations and modeling** - Weather prediction, traffic simulation
- **Testing and debugging** - Reproducible test cases
- **Monte Carlo methods** - Financial risk analysis

**Key Differences:**

| Aspect | TRNG | PRNG |
|--------|------|------|
| **Speed** | Slow (100s-1000s bits/sec) | Very fast (millions/sec) |
| **Predictability** | Unpredictable | Predictable with seed |
| **Reproducibility** | Never repeats | Can reproduce sequences |
| **Security** | Cryptographically secure | Depends on algorithm |
| **Cost** | Expensive hardware | Software only |

**When to Use Which:**

**Use TRNG when:**

- Generating cryptographic keys
- Creating passwords for high-security systems
- Gambling/lottery applications
- Scientific research requiring true randomness

**Use PRNG when:**

- Game development (procedural generation)
- Simulations requiring reproducible results
- Testing (need same "random" data every time)
- General programming where security isn't critical

**Security Gotcha:** 
Never use basic PRNGs (like `Random`) for security! Use cryptographically secure PRNGs:

```java
import java.security.SecureRandom;
import java.util.Base64;

// For security applications
SecureRandom secureRandom = new SecureRandom();

// Generate secure token (16 bytes = 32 hex characters)
byte[] tokenBytes = new byte[16];
secureRandom.nextBytes(tokenBytes);
String secureToken = bytesToHex(tokenBytes);

// Generate secure password (32 bytes, URL-safe base64)
byte[] passwordBytes = new byte[32];
secureRandom.nextBytes(passwordBytes);
String securePassword = Base64.getUrlEncoder().withoutPadding().encodeToString(passwordBytes);

// Helper method to convert bytes to hex
private static String bytesToHex(byte[] bytes) {
    StringBuilder result = new StringBuilder();
    for (byte b : bytes) {
        result.append(String.format("%02x", b));
    }
    return result.toString();
}
```

**Hybrid Approach:**
Many systems combine both:

1. Use TRNG to generate a truly random seed
2. Use that seed in a cryptographically secure PRNG for fast, secure random numbers

This gives you the security of TRNG with the speed of PRNG!
