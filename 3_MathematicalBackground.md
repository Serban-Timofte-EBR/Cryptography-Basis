# Mathematical Background

- What is the entropy of your 8 case-insensitive alpha (a-z) characters password?

## Example

- What is the bit strength (how many bits of information) of a password with 20 characters, having 156 unique values? => Cat de imprevizibila este pentru un atacator

2️⃣ How to calculate entropy

If each character in the password can be chosen independently from N possible characters,
and the password has L characters,
then the total number of possible passwords is:

$$N^L$$

Example: if you have 10 digits and 3 characters in the password → 10^3 = 1000 possible passwords.

⸻

3️⃣ Converting to bits of information

Each choice adds a certain amount of information (measured in bits).

The amount of information for a choice from N options is:

$$\log_2(N)$$

👉 because we want to express "how many yes/no questions (bits)" you need to identify one option out of N possible.

⸻

4️⃣ For a password of L independent characters:

$$\text{Total entropy} = L \times \log_2(N)$$

This means that each character contributes \log_2(N) bits of information,
and 20 characters contribute 20 times more.

⸻

5️⃣ Numerical application

In your case:
    • N = 156 possible characters
    • L = 20 characters
$$\text{Entropy} = 20 \times \log_2(156)$$

$$\log_2(156) =approx 7.283$$

$$20 \times 7.283 = 145.66$$

➡️ 145.7 bits of entropy

⸻

6️⃣ Intuitive interpretation

This means that a 20-character password chosen completely randomly from a set of 156 characters has:

$2^{145.7} \text{ possible combinations} \approx 5.1 \times 10^{43}$

Even a supercomputer testing $10^{18}$ passwords/second would need:

$$\frac{2^{145.7}}{10^{18} \times 60 \times 60 \times 24 \times 365} \approx 1.6 \times 10^{18} \text{ years}$$

So — practically impossible to crack through brute-force.

### Entropy

- If each character from password could be independently chosen from N characters and the password has L characters length, then we have N^L possibilities

N = number of unique characters = 156
L = password length = 20

log2(156) = 7.283
20 * log2(156) = 20 * 7.283 = 145.66

## Example 2

- What is the bit strength (how many bits of information) of a password with 20 characters, each char having 5 unique values?

> **Important**: The key is to identify N (the number of unique values)

- How much entrophy I have in a bit? N = unique values = 5

$$\log_2(5) = 2.3219$$

$$ Entrophy In A Char: 2 ^ 3 = 8 $$

- L = length of a password = 20

$$ L * \log_2(5) = 20 * 2.3219 = 47 (always round up) $$

