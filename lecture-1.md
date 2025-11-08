# CS716 Lecture 1 Summary – Course Orientation, Classical Crypto, Perfect Secrecy

_Assoc. Prof. Hisham Dahshan • Fall 2025 • Week 1_

Topics this week:

- Course logistics, expectations, and grading.
- Historical ciphers and how they fail.
- Formalizing encryption and perfect secrecy (One-Time Pad).
- Randomness assumptions and practical generation.
- How to read and review research papers.

---

## 1. Course Overview

- **Grading:**
  - Midterm block (Weeks 3–7): `30%` (`20%` exam + `10%` assignments/quizzes).
  - Midterm block (Weeks 8–12): `10%`.
  - Presentation & paper review: `10%`.
  - Project (discussed in last week): `10%`.
  - Final exam: `40%`.
- **Attendance:** mandatory for lectures and labs.
- **Syllabus roadmap:** from One-Time Pad and perfect secrecy (Week 1) through block ciphers, public key crypto, signatures, ZK proofs, and applied security topics, ending with the final exam.

ASCII timeline:

```
W1 OTP & Perfect Secrecy → … → W7 Midterm → … → W12 Midterm → W16 Final
```

Course philosophy:

- Focus on theoretical cryptography: rigorous definitions, mathematical proofs, clear threat models.
- Not a hacking/practical IT security course; we avoid implementation details and concrete cost estimates.
- Expect to refresh discrete probability, algorithms/asymptotics, and proof techniques.

---

## 2. Why Study Crypto Historically?

- Pre-computer cryptanalysts were inventive; history offers intuition for modern designs.
- Goals:
  - Recognize “good vs. bad” crypto intuitively.
  - Understand classical attacks (frequency analysis, brute force, meet-in-the-middle).
  - See why modern cryptography embraces formalism (Kerckhoffs’s principle: system remains secure even if everything but the key is public).

---

## 3. Classical Cipher Case Studies

### 3.1 Caesar Cipher

- Shift alphabet by constant offset (e.g., `key = 3`).
- Vulnerable to brute-force (26 possibilities) ⇒ tiny key space.

ASCII brute-force view:

```
shift 0: dwwdfndwrqfh
shift 3: attackatonce ← readable ⇒ key found
```

### 3.2 Monoalphabetic Substitution

- Permutes alphabet; key space `26! ≈ 2^88` yet still breakable.
- Al-Kindi (9th century) introduced **frequency analysis**: match ciphertext letter distribution to known language frequencies.

### 3.3 Vigenère Cipher

- Polyalphabetic: repeat keyword, akin to column-wise Caesar shifts.
- Broken via Kasiski / Friedman tests (guess key length; frequency analysis per column).
- Took centuries to break but still succumbed eventually.

Lessons:

- Large key space alone is insufficient; structure leaks information.
- Need formal guarantees against adaptive, clever adversaries.

---

## 4. Modern Cryptographic Mindset

- Emphasize rigorous threat modeling and definitions:
  - What data is public vs. secret?
  - What does “secure” formally mean? (e.g., no info gain vs. bounded advantage.)
- Formalize encryption scheme `Π = (KeyGen, Enc, Dec)`:
  - `k ← KeyGen(1^n)` produces secret key.
  - `c ← Enc_k(m)` encrypts bitstring message.
  - `m ← Dec_k(c)` recovers original message (correctness: `Dec_k(Enc_k(m)) = m`).

ASCII workflow:

```
KeyGen → k
Plaintext m --Enc_k--> Ciphertext c --Dec_k--> m
```

- Kerckhoffs’s principle: algorithm may be public; secrecy rests solely on the key.

---

## 5. One-Time Pad (OTP)

- Construction:
  - KeyGen: uniform `k ∈ {0,1}^n`.
  - Enc: `Enc_k(m) = m ⊕ k`.
  - Dec: `Dec_k(c) = c ⊕ k`.
- Bitwise XOR truth table ensures correctness.

Example:

```
 m = 1110
 k = 0110
 c = m ⊕ k = 1000
Dec: 1000 ⊕ 0110 = 1110
```

- OTP achieves **perfect secrecy** when key is uniform, used once, and length equals message length.

---

## 6. Defining Secrecy — Four Equivalent Views

All capture “ciphertext reveals nothing about plaintext”:

1. **Semantic security (informal):** no efficient adversary learns new function `f(m)` beyond prior info `g(m)`.
2. **Posterior = prior:** `Pr[M = m | C = c] = Pr[M = m]` for all distributions/messages/ciphertexts.
3. **Ciphertext distribution independent of message:** `Pr_k[Enc_k(m) = c] = Pr_k[Enc_k(m') = c]`.
4. **Indistinguishability experiment (IND):** adversary chooses `(m0, m1)`; wins if it guesses challenge bit better than `1/2`. Perfect secrecy ⇒ win prob = `1/2`.

Theorem: Definitions 1–4 are equivalent. OTP satisfies them (easy via Definition 3: `m ⊕ k` uniform for random `k`).

---

## 7. Limits of Perfect Secrecy

- Shannon’s theorem (with `|K| = |M| = |C|`): perfect secrecy ⇔
  1. `KeyGen` outputs each key with probability `1/|K|`.
  2. For every message/ciphertext pair there is a unique key producing it.
- Corollary: key space must be at least as large as message space (`|K| ≥ |M|`).
- Practical drawbacks:
  - Key as long as message; key reuse catastrophic (`c ⊕ c' = m ⊕ m'`).
  - Key distribution/storage at scale is infeasible (VENONA project exploited pad reuse).
- Drives motivation for computational security (next lecture).

ASCII caution:

```
Reuse OTP:
 c  = m  ⊕ k
 c' = m' ⊕ k
 c ⊕ c' = m ⊕ m'  ⇒ leaks structure
```

---

## 8. Randomness Assumptions

- Theoretical results assume access to unbiased, independent random bits for key generation.
- Practical challenges:
  - Hard to flip millions of fair coins.
  - Human inputs (mouse moves, keystrokes) need entropy extraction.
  - Hardware RNGs must be trusted and validated (avoid naive `rand()` usage).
- Randomness extractors: convert high-entropy but biased sources into near-uniform bits (active research area).

---

## 9. Reading and Reviewing Research Papers

Structured approach:

1. **First pass:** title, abstract, figures, skim intro/conclusion, scan references.
2. **Second pass:** read intro carefully, study background/related work, parse figures.
3. **Third pass:** deep read, trace proofs, flag references for follow-up.

Review mindset:

- Evaluate motivation, relation to prior work, technical novelty, implementation effort, evaluation soundness.
- For class reviews:
  1. **Summary:** 5–10 honest sentences capturing problem, approach, results.
  2. **Strengths & weaknesses:** 2–4 bullet points each, grounded in analysis.
  3. **Suggestions for improvement:** detailed comments/questions, including experiment gaps, clarity issues, typos.

ASCII reminder:

```
Review = Summary + (Pros/Cons) + Suggestions
```

---

## 10. Key Takeaways for Exam Preparation

- Crypto course emphasizes formal models, not ad-hoc security.
- Classical ciphers fail despite clever design; formal definitions prevent repeating history.
- OTP achieves perfect secrecy but is impractical at scale; understanding its limits motivates computational notions.
- Perfect secrecy definitions are equivalent; practice translating between them.
- Strong randomness sources are essential; know pitfalls of weak RNGs.
- Paper literacy and review skills are integral parts of the course deliverables.

Study checklist:

- Re-derive frequency analysis for substitution cipher; explain weakness.
- Work through the IND game for OTP; prove adversary’s success is `1/2`.
- State and prove Shannon’s theorem conditions for perfect secrecy.
- Explain why key reuse breaks OTP with an explicit example.
- Outline a plan to gather randomness for a practical OTP (and why it is hard).
- Draft a sample paper review using the provided template.

By mastering these fundamentals, you are ready to dive into computational security, pseudorandomness, and beyond in Week 2.
