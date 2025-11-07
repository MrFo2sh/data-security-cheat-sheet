# CS716 Lecture 4 Summary – CPA/CCA Security and Message Authentication Codes

_Assoc. Prof. Hisham Dahshan • Fall 2025 • Week 4_

Topics this week:

- Constructing chosen-plaintext secure (CPA) encryption.
- Defining chosen-ciphertext security (CCA).
- Building and analyzing message authentication codes (MACs).

---

## 1. Motivation — Adversaries Choose the Plaintexts

- Real attackers influence what honest parties encrypt (e.g., WWII Allied deceptions).
- CPA model: adversary may request encryptions of adaptively chosen messages.
- Historical case study: Battle of Midway. Sending a fake “water shortage” message forced the attacker to reveal plaintext/ciphertext linkage.

ASCII prompt:

```
Adversary picks m1, m2, …  -->  Encryption oracle returns Enc_k(mi)
Goal: distinguish challenge ciphertext or derive key info
```

---

## 2. CPA Security Experiment (Single Message)

Game `Priv^{cpa}_{A, Π}(1^n)` (encryption scheme `Π = (Gen, Enc, Dec)`):

1. Challenger runs `k ← Gen(1^n)`.
2. Adversary outputs challenge pair `(m0, m1)` of equal length.
3. Challenger samples `b ← {0,1}` and returns `c = Enc_k(m_b)`.
4. Adversary may continue querying the encryption oracle `Enc_k(·)` on messages of its choice (including previously seen ones).
5. Adversary outputs guess `b'`.

Security requirement:

```
For every PPT adversary A, Pr[Priv^{cpa}_{A, Π}(1^n) = 1] ≤ 1/2 + μ(n)
where μ(n) is negligible.
```

ASCII flow:

```
           ┌───────────────┐
Choose m0, m1 ──►│  Challenger   │
                 │  k ← Gen      │
       Oracle ◄──┤  b ← {0,1}    │
                 │  c = Enc_k(mb)│
                 └───────────────┘
                    │
                c ──┘
A outputs b'          Win if b' = b
```

**Theorem:** CPA security for single encryptions ⇒ CPA security for multiple adaptive encryptions. We usually refer simply to “CPA security.”

---

## 3. From CPA to CCA — Adding a Decryption Oracle

- Chosen-ciphertext attacks (CCA) model adversaries who can get decryptions of ciphertexts they craft.
- Motivations: protocol error messages, adaptive attacks (e.g., padding oracles), authentication schemes that echo plaintext.

CCA (IND-CCA2) experiment:

1. Challenger picks `k ← Gen(1^n)` and secret bit `b`.
2. Adversary has oracle access to both `Enc_k(·)` and `Dec_k(·)`.
3. Challenger gives `c* = Enc_k(m_b)` for adversary’s chosen `(m0, m1)`.
4. Adversary continues querying oracles **except** it may not ask `Dec_k(c*)`.
5. Adversary outputs `b'`; success if `b' = b`.

Definition: `Π` is CCA-secure if `Pr[Priv^{cca}_{A, Π}(1^n) = 1] ≤ 1/2 + negl(n)` for all PPT adversaries `A`.

ASCII reminder:

```
Enc oracle ─────► c_i           Dec oracle ─────► m_i   (but not on c*)
                   ▲                               ▲
Challenge c*  ----─┘                               │
```

Relationships:

- CCA security ⇒ CPA security (strictly stronger).
- Multiple-encryption CCA security follows from single-challenge security.
- Classic stream/block ciphers without authentication are generally **not** CCA secure.

Standard CCA-secure constructions:

- RSA-OAEP (in the random-oracle model).
- Cramer–Shoup (standard model, DDH assumption).
- Hybrid schemes: CCA-secure KEM + authenticated symmetric encryption (AEAD).
- Modern KEM examples: Kyber, NTRU, Classic McEliece.

---

## 4. Message Authentication Codes (MACs)

Security goals recap:

- **Confidentiality:** hide the content.
- **Integrity:** detect unauthorized modifications.
- **Authenticity:** confirm sender’s identity.

Encryption alone ≠ integrity (CTR-bit-flip example: change “$3850” to “$10”). MACs give tamper detection.

### 4.1 MAC Syntax

- `Gen(1^n) → k` (shared secret key).
- `Mac_k(m) → t` (tag generation, may be probabilistic).
- `Vrfy_k(m, t) → {0,1}` (verification; deterministic).
- Correctness: `Vrfy_k(m, Mac_k(m)) = 1` for all `m`.

ASCII workflow:

```
Sender:   (m, t) = (m, Mac_k(m))  ───────────────► Receiver
Receiver: checks Vrfy_k(m, t) ? 1 : reject (⊥)
```

### 4.2 Forgery Experiments

- **Existential unforgeability under adaptive chosen-message attack (EUF-CMA):**

  - Adversary queries MAC oracle on messages `m1…mq`, receiving tags `t1…tq`.
  - Wins if it outputs `(m, t)` with `Vrfy_k(m, t) = 1` and `m` was never queried.
  - Requirement: success probability ≤ negligible.

- **Strong EUF-CMA:** disallows forging a _new valid tag_ even for previously queried messages `(m, t)`.

Replay risk: MACs alone are stateless; they cannot prevent re-sending valid `(m, t)` pairs. Mitigations include sequence numbers or timestamps (careful with loss/delay).

---

## 5. CBC-MAC and Variants

- Basic CBC-MAC (for fixed-length messages):

```
IV = 0
T0 = 0
For i = 1..ℓ:
  Ti = E_k(T_{i-1} ⊕ Mi)
Tag = Tℓ
```

- Secure only if all messages are the same fixed length; otherwise length-extension attacks apply.
- Padding must be injective (e.g., ISO/IEC 9797-1 padding: append `1` then zeros, and add a full block if message length already equals block size).

Warnings:

- CBC-MAC with a non-zero IV is insecure.
- CBC encryption **without** random IV leaks patterns (ECB-like).

- **CMAC (NIST standard, aka One-Key MAC):**
  - Uses AES + derived subkeys `K1`, `K2` for final block depending on padding.
  - Avoids dummy block; secure for variable-length messages.
  - Widely deployed (IPsec, TLS, WPA2/3, EMV, SWIFT).

ASCII comparison:

```
CBC-MAC:   IV=0, sequential AES, last block output is tag.
CMAC:      Same core, but adjusts final block with K1/K2 to handle length safely.
```

---

## 6. Putting It Together — Authenticated Encryption

- Confidentiality alone (CPA/CCA) and integrity alone (MAC) are insufficient for many protocols.
- Combine via:
  1. **Encrypt-then-MAC**: AEAD like AES-GCM, ChaCha20-Poly1305.
  2. **KEM-DEM hybrids**: CCA-secure KEM + AEAD for symmetric channel.
- Goal: resist both CCA attacks and message forgeries with replay protection via nonces/associated data.

---

## 7. Key Takeaways for Exam Preparation

- CPA security captures adaptive encryption queries; single-challenge proofs extend to multi-challenge.
- CCA security forbids decrypting the challenge ciphertext but allows other adaptive decryptions; it is the gold standard for robust encryption.
- Classic block/stream ciphers without authentication fail CCA; rely on hybrid/AES-GCM style schemes.
- MACs provide integrity/authenticity; EUF-CMA is the baseline notion, strong EUF-CMA prevents tag duplication.
- CBC-MAC is safe only with fixed-length messages and zero IV; CMAC generalizes it securely.
- Always plan for replay defenses (sequence numbers, timestamps, nonces).

Study checklist:

- Write out the CPA and CCA games; explain why the `Dec` oracle restriction is necessary.
- Compare CPA vs. CCA examples (why OTP + PRG is CPA-secure but not CCA-secure).
- Practice proving MAC EUF-CMA security via reduction to block-cipher PRF security.
- Derive why CBC-MAC fails with variable-length inputs; construct the length-extension attack.
- Memorize CMAC final-block handling with `K1`/`K2` and explain why padding must be injective.
- Identify real-world protocols using MACs/AEAD (TLS, IPsec) and state what threats each mitigates.

Questions to ponder: How can authenticated encryption be combined with post-quantum KEMs to deliver CCA security? What transforms are safe for converting MACs to signature-like functionality?
