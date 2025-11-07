# CS716 Lecture 2 Summary – Computational Security against Eavesdroppers

_Assoc. Prof. Hisham Dahshan • Fall 2025 • Week 2_

Topics this week:

- Computational security in the presence of an eavesdropper.
- Building encryption schemes that stay secure once we relax perfect secrecy.

---

## 1. Recap — One-Time Pad (OTP)

OTP refresher:

- **KeyGen:** pick a uniform random key `k ∈ {0,1}^n`.
- **Enc:** `Enc_k(m) = m ⊕ k` (bitwise XOR).
- **Dec:** `Dec_k(c) = c ⊕ k`.
- Messages, keys, ciphertexts are all length `n` bitstrings.

ASCII picture:

```
 Plaintext m    Key k           Ciphertext c
   101101       010010   -->       111111
          \      /
           XOR (bit by bit)
```

XOR truth table (`⊕` is addition mod 2):

```
0 ⊕ 0 = 0
0 ⊕ 1 = 1
1 ⊕ 0 = 1
1 ⊕ 1 = 0
```

Why OTP gives perfect secrecy (informal sketch):

1. The key is uniform over all `2^n` possibilities.
2. Ciphertext is just the key shifted by the plaintext.
3. A uniform key plus any fixed shift is still uniform.
4. Therefore each ciphertext is equally likely for any plaintext.

---

## 2. Perfect Secrecy — Four Equivalent Definitions

All of the following say the same thing (Theorem 1):

1. **Semantic secrecy (informal):** an adversary learns nothing useful (`f(m)`) beyond prior knowledge `g(m)` even after seeing the ciphertext.
2. **Definition 2:** `Pr[M = m | C = c] = Pr[M = m]` for every distribution and every plaintext `m`.
3. **Definition 3:** For any two plaintexts `m, m'` and ciphertext `c`, `Pr_k[Enc_k(m) = c] = Pr_k[Enc_k(m') = c]`.
4. **Definition 4 (IND):** any adversary wins the indistinguishability game with probability exactly `1/2`.

Mnemonic chart:

```
Semantic secrecy ←→ Posterior = Prior ←→ Equal enc probabilities ←→ IND win = 1/2
```

Because of Theorem 1, proving any one of these for OTP proves them all.

---

## 3. Shannon’s Theorem — Consequences for Perfect Secrecy

Let `ℳ`, `𝒦`, `𝒞` be message, key, ciphertext sets. If an encryption scheme is perfectly secret:

1. `|𝒦| ≥ |ℳ|` (key space must be at least as large as message space).
2. If `|𝒦| = |ℳ|`, then:
   - KeyGen outputs uniform keys.
   - For every message `m` and ciphertext `c`, there is **exactly one** key `k` with `Enc_k(m) = c`.

Implication: perfect secrecy forces key length ≥ message length. OTP satisfies this with equality.

Small thought experiment: if `n = 8` (256 keys), brute-force over all keys is easy for an attacker. Perfect secrecy does **not** stop exhaustive search; it only says ciphertext gives no info before brute force.

---

## 4. OTP Limitations in the Real World

OTP assumes:

```
- Alice and Bob already share a secret key of length n.
- They protect their private spaces.
- Only one message travels on the single channel.
- Eve is passive; she only listens.
```

ASCII sketch:

```
Alice ----(ciphertext)----> Bob
  ^                         |
  |                         v
 Secret key shared offline  Eve (listens only)
```

What goes wrong when assumptions break:

- **Key reuse (two messages):**
  - `c  = m  ⊕ k`
  - `c' = m' ⊕ k`
  - Eve can XOR ciphertexts: `c ⊕ c' = m ⊕ m'`, leaking structure (e.g., spaces, repeated bits).
  - If Eve knows one plaintext, she fully recovers `k` and the other plaintext.
- **Multiple transmissions / leakage / tampering:** Shannon shows you cannot reuse OTP even for `n+1` bits without losing perfect secrecy.
- **Practical storage:** you would need a second hard drive of random key bits to protect the first one.

Conclusion: OTP is only “perfect” under very strong and impractical assumptions.

---

## 5. From Perfect to Computational Security

Because of Shannon’s limits, we relax the goal:

- We accept that adversaries might eventually break the scheme, but we require it to be **infeasible**.
- We accept tiny success probabilities (negligible) or enormous running times for the attacker.

Guiding questions:

- If breaking takes `10^10` years, do we care?
- If success chance is `1 / 10^100`, do we care?
- Probably not. So we define security against **efficient** adversaries only.

This shift opens the door to modern cryptography: without it, we are stuck with OTP.

---

## 6. Efficient vs. Inefficient Algorithms

We follow complexity-theory style rules:

- Inputs are bitstrings `x ∈ {0,1}^n`.
- Running time is counted as a function of `n` (the input length).
- Algorithms may use randomness (coin flips).
- **Efficient** = Probabilistic Polynomial Time (PPT).

Definition (PPT): An algorithm `A` is efficient if ∃ polynomial `p(n)` and integer `N` so that for all `n > N` and all `x ∈ {0,1}^n`, `A(x)` halts in at most `p(n)` steps.

ASCII reminder:

```
Input size n  -->  Time(n)
Efficient:       ≤ poly(n)
Inefficient:     > every poly(n)  (e.g., 2^n, 2^{√n}, n^{log n})
```

**Negligible probabilities:** `ε(n)` is negligible (written `negl(n)`) if it is smaller than `1 / p(n)` for every polynomial `p`. Negligible events stay negligible even after repeating a polynomial number of times.

---

## 7. Pseudorandomness — Stronger than Standard RNGs

Cryptographic pseudorandom generators (PRGs) turn a short uniform seed into a longer “random-looking” string that fools every PPT distinguisher.

Definition: A PRG is a deterministic PPT algorithm `G` with two properties:

1. **Expansion:** `G : {0,1}^n → {0,1}^{ℓ(n)}` where `ℓ(n) > n`.
2. **Pseudorandomness:** for every PPT distinguisher `D`,
   `| Pr_{s←{0,1}^n}[D(G(s)) = 1] - Pr_{r←{0,1}^{ℓ(n)}}[D(r) = 1] | ≤ negl(n)`.

Experiment diagrams:

```
Experiment A: r ← {0,1}^{ℓ(n)}           Experiment B: s ← {0,1}^n
              b ← D(r)                                 y = G(s)
                                                        b ← D(y)
Goal: no efficient D can tell which experiment it is in.
```

Breaking any PRG by brute force is easy if you ignore efficiency: just try every seed and check if `G(seed) = output`. Security comes from the fact that trying all seeds is infeasible when `n` is large.

---

## 8. PRG-Based Encryption Scheme

We recycle the OTP template, but replace the perfect random key stream with a PRG output.

Construction (messages of length `ℓ(n)`):

- KeyGen: pick seed `k ← {0,1}^n`.
- Enc: `Enc_k(m) = m ⊕ G(k)`.
- Dec: `Dec_k(c) = c ⊕ G(k)`.

ASCII pipeline:

```
Seed k (n bits) --G--> keystream G(k) (ℓ(n) bits)
                     ⊕
Plaintext m (ℓ(n) bits) -----> Ciphertext c
```

Notes:

- `ℓ(n)` can be much larger than `n`, so we cannot have perfect secrecy (Shannon’s bound).
- Goal: show ciphertexts are indistinguishable from OTP ciphertexts to any PPT adversary.

---

## 9. IND Security Game (Computational Version)

Indistinguishability experiment for any scheme:

1. Adversary `A` outputs two equal-length messages `m0, m1`.
2. Challenger samples key `k` and random bit `b`, returns `c = Enc_k(m_b)`.
3. `A` outputs guess `b'`. `A` wins if `b' = b`.

Security definition: scheme is IND-secure if for every PPT `A`,
`Pr[A wins] ≤ 1/2 + negl(n)`.

ASCII flow:

```
A: choose (m0, m1)        Challenger: pick k, b
                          c = Enc_k(m_b)
A: see c, output b'       Win if b' = b
```

---

## 10. Security Proof Idea for PRG Encryption

Goal: use the assumption “`G` is a secure PRG” to prove the encryption scheme is IND-secure.

Proof by reduction (outline):

1. Assume ∃ adversary `A` that wins the IND game against the PRG-based scheme with noticeable advantage.
2. Build distinguisher `D` against the PRG:
   - `D` receives string `r` (either uniform or `G(s)`).
   - `D` runs `A` in the IND game but uses `r` as the keystream.
   - If `r` is uniform, `A` is actually attacking OTP → wins with prob `1/2`.
   - If `r = G(s)`, `A` is attacking the PRG scheme → wins with prob `1/2 + ε(n)`.
3. `D` outputs 1 when `A` wins. `D` distinguishes PRG output from uniform with advantage `ε(n)`.
4. This contradicts the PRG definition. Therefore no such `A` exists.

ASCII reduction picture:

```
Distinguisher D (goal: break G)
  |-- give r to A as keystream
  |-- simulate IND game for A
  |-- output 1 if A guesses right
Two worlds:
  r uniform ----> A sees OTP  (win 1/2)
  r = G(s)  ----> A sees PRG  (win 1/2 + ε)
Difference ε ⇒ D breaks G ⇒ contradiction.
```

---

## 11. Roadmap Going Forward

What we must define, build, and prove for computational security:

1. Rigorous definitions of “random-looking,” “good-enough randomness,” and “feasible.”
2. Explicit constructions of PRGs (or assume hardness to get them).
3. Reduction proofs that encryption based on PRGs (and later, block ciphers, etc.) achieve IND security.

---

## 12. Key Takeaways for Exam Preparation

- Perfect secrecy ⇔ four equivalent definitions; OTP is the canonical example.
- Shannon’s theorem forces keys to be at least as large as messages; no free lunch.
- OTP breaks down when any assumption fails (especially key reuse).
- Computational security shifts the focus to PPT adversaries and negligible advantages.
- PPT, polynomial vs. super-polynomial time, and negligible functions are core vocabulary.
- Cryptographic pseudorandomness = indistinguishability from uniform for all PPT distinguishers.
- PRG-based encryption extends OTP to long messages while staying IND-secure.
- Reduction proofs (adversary for scheme ⇒ distinguisher for PRG) are a central proof style.

Study checklist:

- Be able to explain each secrecy definition and why they match.
- Work through Shannon’s theorem statement; know why `|𝒦| ≥ |ℳ|`.
- Practice showing why key reuse breaks OTP (do the XOR algebra yourself).
- Memorize the PPT definition and how negligible functions behave under repetition.
- Understand the PRG definition and the IND game steps.
- Rehearse the reduction proof outline for the PRG-based encryption scheme.

Good luck, and keep an eye out for more on PRGs and computational security next week!
