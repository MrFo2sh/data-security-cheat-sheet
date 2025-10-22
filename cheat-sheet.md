# CS716 Data Security - Mathematical Notation Cheat Sheet

## Quick Reference Table

| Symbol/Function | Name                    | Description                                                                 |
| --------------- | ----------------------- | --------------------------------------------------------------------------- |
| **⊕**           | XOR (Exclusive OR)      | Bitwise addition modulo 2. Returns 1 if bits differ, 0 if same.             |
| **∈**           | Element of              | Indicates membership in a set (e.g., k ∈ {0,1}ⁿ means k belongs to the set) |
| **←**           | Sampling/Assignment     | Randomly sample from a set or assign a value                                |
| **\|**          | Such that / Cardinality | "Such that" in set notation, or size/cardinality of a set                   |
| **≥**           | Greater than or equal   | Standard comparison operator                                                |
| **ℕ**           | Natural numbers         | Set of all natural numbers {0, 1, 2, 3, ...}                                |
| **ℳ**           | Message space           | Set of all possible messages                                                |
| **𝒦**           | Key space               | Set of all possible keys                                                    |
| **𝒞**           | Ciphertext space        | Set of all possible ciphertexts                                             |

---

## Sets and Spaces

| Notation   | Name                     | Description                                            |
| ---------- | ------------------------ | ------------------------------------------------------ |
| **{0,1}ⁿ** | Binary string space      | Set of all binary strings of length n                  |
| **{0,1}**  | Binary set               | Set containing only 0 and 1                            |
| **ℳ**      | Message space            | Set of all valid messages                              |
| **𝒦**      | Key space                | Set of all valid keys                                  |
| **𝒞**      | Ciphertext space         | Set of all valid ciphertexts                           |
| **\|𝒦\|**  | Cardinality of key space | Number of possible keys (size of key space)            |
| **2ⁿ**     | Exponential              | 2 to the power of n (size of binary space of length n) |

---

## Probability Notation

| Notation               | Name                    | Description                                                 |
| ---------------------- | ----------------------- | ----------------------------------------------------------- |
| **Pr[event]**          | Probability             | Probability that an event occurs                            |
| **Pr[M = m]**          | Probability of message  | Probability that message M equals specific value m          |
| **Pr[M = m \| C = c]** | Conditional probability | Probability M = m given that C = c (using Bayes' theorem)   |
| **Pr[A wins]**         | Win probability         | Probability that adversary A successfully breaks the scheme |
| **Pr[b = b']**         | Success probability     | Probability that guess b' equals actual bit b               |
| **Prₖ[event]**         | Probability over keys   | Probability of event when sampling uniformly random key k   |

---

## Encryption Scheme Components

| Notation              | Name                     | Description                                          |
| --------------------- | ------------------------ | ---------------------------------------------------- |
| **KeyGen** or **Gen** | Key generation algorithm | Generates a secret key (usually randomly)            |
| **Encₖ(m)**           | Encryption function      | Encrypts message m using key k to produce ciphertext |
| **Decₖ(c)**           | Decryption function      | Decrypts ciphertext c using key k to recover message |
| **k ← KeyGen**        | Key sampling             | Sample/generate a random key using KeyGen algorithm  |
| **c ← Encₖ(m)**       | Ciphertext generation    | Encrypt message m with key k to get ciphertext c     |
| **m ⊕ k**             | XOR encryption           | Bitwise XOR of message m and key k (One-Time Pad)    |

---

## One-Time Pad (OTP) Specific

| Expression          | Name             | Description                                           |
| ------------------- | ---------------- | ----------------------------------------------------- |
| **k ∈ {0,1}ⁿ**      | Random key       | Key k is a uniformly random binary string of length n |
| **Encₖ(m) = m ⊕ k** | OTP encryption   | Ciphertext is message XOR key                         |
| **Decₖ(c) = c ⊕ k** | OTP decryption   | Message recovered by XORing ciphertext with key       |
| **m ⊕ k ⊕ k = m**   | XOR cancellation | Key XORed twice cancels out, leaving original message |

---

## Perfect Secrecy Definitions

| Expression                               | Name                           | Description                                                |
| ---------------------------------------- | ------------------------------ | ---------------------------------------------------------- |
| **Pr[M = m \| C = c] = Pr[M = m]**       | Perfect secrecy (Definition 2) | Observing ciphertext doesn't change probability of message |
| **Prₖ[Encₖ(m) = c] = Prₖ[Encₖ(m') = c]** | Equal encryption probability   | Any two messages equally likely to produce same ciphertext |
| **Pr[A wins IND] = 1/2**                 | Perfect indistinguishability   | Adversary can't do better than random guessing (50%)       |
| **\|𝒦\| ≥ \|ℳ\|**                        | Shannon's theorem              | Key space must be at least as large as message space       |

---

## Computational Security

| Notation            | Name                          | Description                                                      |
| ------------------- | ----------------------------- | ---------------------------------------------------------------- |
| **PPT**             | Probabilistic Polynomial Time | Algorithm that runs in polynomial time with access to randomness |
| **negl(n)**         | Negligible function           | Function that decreases faster than any polynomial (e.g., 2⁻ⁿ)   |
| **p(n)**            | Polynomial function           | Function that grows polynomially (e.g., n², n¹⁰⁰)                |
| **1/2 + negl(n)**   | Negligible advantage          | Success probability barely better than random guessing           |
| **≤ 1/2 + negl(n)** | Computational security        | Adversary's success is negligibly better than 50%                |

---

## Pseudorandom Generator (PRG)

| Expression                                       | Name               | Description                                                            |
| ------------------------------------------------ | ------------------ | ---------------------------------------------------------------------- |
| **G: {0,1}ⁿ → {0,1}ℓ⁽ⁿ⁾**                        | PRG function       | Maps n-bit seed to ℓ(n)-bit output where ℓ(n) > n                      |
| **G(s)**                                         | PRG output         | Pseudorandom string generated from seed s                              |
| **s ← {0,1}ⁿ**                                   | Seed sampling      | Uniformly random seed of length n                                      |
| **ℓ(n)**                                         | Expansion function | Output length as function of input length (must be > n)                |
| **Pr[D(G(s)) = 1] - Pr[D(r) = 1]**               | PRG advantage      | Distinguisher's advantage in telling PRG output from random            |
| **\|Pr[D(G(s)) = 1] - Pr[D(r) = 1]\| ≤ negl(n)** | PRG security       | Distinguisher can't tell PRG from random with non-negligible advantage |

---

## Indistinguishability (IND) Game

| Notation            | Name                 | Description                                               |
| ------------------- | -------------------- | --------------------------------------------------------- |
| **A**               | Adversary            | The attacker trying to break the encryption scheme        |
| **D**               | Distinguisher        | Algorithm trying to distinguish between two distributions |
| **m₀, m₁**          | Message pair         | Two messages adversary wants to distinguish between       |
| **\|m₀\| = \|m₁\|** | Equal length         | Messages must have same length                            |
| **b ← {0,1}**       | Random bit           | Randomly choose 0 or 1 (coin flip)                        |
| **c ← Encₖ(mᵦ)**    | Challenge ciphertext | Encrypt message corresponding to random bit b             |
| **b'**              | Adversary's guess    | Adversary's guess for which message was encrypted         |
| **b = b'**          | Correct guess        | Adversary wins if guess matches actual bit                |

---

## PRG-Based Encryption Scheme

| Expression             | Name           | Description                                |
| ---------------------- | -------------- | ------------------------------------------ |
| **Encₖ(m) = m ⊕ G(k)** | PRG encryption | Message XORed with PRG expansion of key    |
| **Decₖ(c) = c ⊕ G(k)** | PRG decryption | Ciphertext XORed with PRG expansion of key |
| **k ∈ {0,1}ⁿ**         | Short key      | Key of length n (shorter than message)     |
| **m ∈ {0,1}ℓ⁽ⁿ⁾**      | Long message   | Message of length ℓ(n) (longer than key)   |

---

## Algorithms and Complexity

| Notation      | Name               | Description                                                |
| ------------- | ------------------ | ---------------------------------------------------------- |
| **A(x)**      | Algorithm on input | Algorithm A running on input x                             |
| **p(n)**      | Polynomial bound   | Running time bounded by polynomial p(n)                    |
| **2ⁿ**        | Exponential time   | Exponential running time (infeasible for large n)          |
| **2^√n**      | Superpolynomial    | Larger than any polynomial but not necessarily exponential |
| **n^(log n)** | Superpolynomial    | Grows faster than any polynomial                           |
| **O(p(n))**   | Big-O notation     | Running time is at most polynomial p(n) up to constants    |

---

## Negligible Functions

| Expression        | Name                    | Description                                                    |
| ----------------- | ----------------------- | -------------------------------------------------------------- |
| **negl(n)**       | Negligible function     | Function smaller than 1/p(n) for all polynomials p and large n |
| **f(n) < 1/p(n)** | Negligibility condition | For all polynomials p, eventually f(n) becomes smaller         |
| **2⁻ⁿ**           | Exponentially small     | Classic example of negligible function                         |
| **1/2ⁿ**          | Inverse exponential     | Another way to write 2⁻ⁿ                                       |
| **1/n¹⁰⁰**        | Non-negligible          | This is NOT negligible (it's polynomial)                       |

---

## Reduction Proof Notation

| Expression                           | Name                    | Description                                            |
| ------------------------------------ | ----------------------- | ------------------------------------------------------ |
| **"If A breaks X, then D breaks Y"** | Reductionist proof      | Proof by contradiction showing security                |
| **A wins IND → D breaks PRG**        | Reduction implication   | If adversary wins, distinguisher succeeds              |
| **Pr[D(G(s)) = 1]**                  | Distinguisher on PRG    | Probability D outputs 1 when given PRG output          |
| **Pr[D(r) = 1]**                     | Distinguisher on random | Probability D outputs 1 when given truly random string |

---

## Common Operations

| Operation         | Symbol | Example         | Result                   |
| ----------------- | ------ | --------------- | ------------------------ |
| **XOR**           | ⊕      | 0 ⊕ 0           | 0                        |
| **XOR**           | ⊕      | 0 ⊕ 1           | 1                        |
| **XOR**           | ⊕      | 1 ⊕ 1           | 0                        |
| **Sampling**      | ←      | k ← {0,1}ⁿ      | k is random n-bit string |
| **Concatenation** | \|\|   | s\|\|b          | Append b to s            |
| **Equality**      | =      | \|m₀\| = \|m₁\| | Lengths are equal        |

---

## Key Properties

| Property             | Expression                | Meaning                       |
| -------------------- | ------------------------- | ----------------------------- |
| **XOR Self-Inverse** | x ⊕ x = 0                 | XORing with itself gives 0    |
| **XOR Identity**     | x ⊕ 0 = x                 | XORing with 0 gives x         |
| **XOR Commutative**  | x ⊕ y = y ⊕ x             | Order doesn't matter          |
| **XOR Associative**  | (x ⊕ y) ⊕ z = x ⊕ (y ⊕ z) | Grouping doesn't matter       |
| **Key Cancellation** | (m ⊕ k) ⊕ k = m           | Key cancels out in decryption |

---

## Security Game Outcomes

| Expression                     | Meaning                  | Security Implication                          |
| ------------------------------ | ------------------------ | --------------------------------------------- |
| **Pr[A wins] = 1/2**           | Random guessing          | Perfect security - adversary learns nothing   |
| **Pr[A wins] = 1/2 + negl(n)** | Nearly random            | Computational security - negligible advantage |
| **Pr[A wins] = 1/2 + 1/p(n)**  | Non-negligible advantage | Insecure - adversary has polynomial advantage |
| **Pr[A wins] = 1**             | Always wins              | Completely broken - no security               |

---

## Attack Models

| Symbol  | Name          | Description                                       |
| ------- | ------------- | ------------------------------------------------- |
| **Eve** | Eavesdropper  | Passive adversary who observes ciphertexts        |
| **A**   | Adversary     | Generic attacker (may be passive or active)       |
| **D**   | Distinguisher | Adversary trying to distinguish two distributions |

---

## Practical Examples

### Example 1: OTP Encryption

```
Message: m = 10110101
Key:     k = 11001100
         ⊕ ___________
Cipher:  c = 01111001

Decryption:
Cipher:  c = 01111001
Key:     k = 11001100
         ⊕ ___________
Message: m = 10110101
```

### Example 2: PRG Expansion

```
Seed:     s ∈ {0,1}⁸     (8 bits)
PRG:      G: {0,1}⁸ → {0,1}¹⁶
Output:   G(s) ∈ {0,1}¹⁶  (16 bits)

Expansion factor: ℓ(8) = 16 (doubled the length)
```

### Example 3: IND Game Success

```
Perfect secrecy:     Pr[A wins] = 1/2 = 50%
Computational:       Pr[A wins] ≤ 1/2 + 2⁻⁸⁰ ≈ 50.0000...%
Weak encryption:     Pr[A wins] = 1/2 + 1/100 = 51%
Broken:              Pr[A wins] = 1 = 100%
```

### Example 4: Negligible vs Non-negligible

```
Negligible:          2⁻¹²⁸ (negligible for n=128)
Non-negligible:      1/n² (polynomial, not negligible)
Very small:          1/n¹⁰⁰⁰⁰ (still polynomial!)
Super small:         2⁻ⁿ (truly negligible)
```

---

## Important Theorems Summary

| Theorem                 | Statement                                         | Implication                                |
| ----------------------- | ------------------------------------------------- | ------------------------------------------ |
| **Shannon's Theorem**   | \|𝒦\| ≥ \|ℳ\| for perfect secrecy                 | Key must be as long as message             |
| **Equivalence Theorem** | Definitions 1-4 of perfect secrecy are equivalent | All perfect secrecy notions are the same   |
| **PRG Security**        | If G is PRG, then PRG-encryption is IND-secure    | Pseudorandomness implies secure encryption |

---

## Quick Reference: Common Patterns

### Pattern 1: Probability Comparison

```
Pr[event₁] - Pr[event₂] ≤ negl(n)
```

Means: Two events are computationally indistinguishable

### Pattern 2: Sampling

```
k ← {0,1}ⁿ
```

Means: Sample k uniformly at random from n-bit strings

### Pattern 3: Function Domain/Range

```
f: A → B
```

Means: Function f maps elements from set A to set B

### Pattern 4: Security Bound

```
Pr[adversary succeeds] ≤ 1/2 + negl(n)
```

Means: Adversary can't do much better than random guessing

---

## Notation Usage Examples

| Concept                  | Formal Notation                     | Plain English                                 |
| ------------------------ | ----------------------------------- | --------------------------------------------- |
| **Key generation**       | k ← {0,1}ⁿ                          | Choose k randomly from all n-bit strings      |
| **Encryption**           | c ← Encₖ(m)                         | Encrypt message m with key k to get c         |
| **XOR operation**        | c = m ⊕ k                           | XOR message with key bit-by-bit               |
| **Security**             | Pr[A wins] ≤ 1/2 + negl(n)          | Adversary can't win much better than guessing |
| **PRG expansion**        | G: {0,1}ⁿ → {0,1}²ⁿ                 | G doubles the input length                    |
| **Indistinguishability** | \|Pr[D(x) = 1] - Pr[D(y) = 1]\| ≤ ε | D can't distinguish x from y                  |

---

## Study Tips

1. **XOR (⊕)** appears everywhere - memorize its properties!
2. **← arrow** means "sample randomly" or "assign"
3. **Pr[·]** always means probability of something
4. **negl(n)** means "so small we don't care"
5. **PPT** means "realistic computer algorithm"
6. **1/2 + something small** means "barely better than guessing"

---

## Common Mistakes to Avoid

| Wrong ❌                          | Correct ✓                    | Explanation                      |
| --------------------------------- | ---------------------------- | -------------------------------- |
| 1/n is negligible                 | 1/n is NOT negligible        | It's polynomial, not negligible  |
| Pr[A wins] = 0.5 is bad           | Pr[A wins] = 0.5 is perfect! | Means adversary learns nothing   |
| OTP key can be reused             | OTP key CANNOT be reused     | Reuse breaks security completely |
| \|𝒦\| < \|ℳ\| for perfect secrecy | \|𝒦\| ≥ \|ℳ\| required       | Shannon's theorem                |

---

**End of Cheat Sheet**
