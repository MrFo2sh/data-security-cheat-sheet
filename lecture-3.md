# CS716 Lecture 3 Summary – Block Cipher Foundations

_Assoc. Prof. Hisham Dahshan • Fall 2025 • Week 3_

Topics this week:

- Classic block cipher constructions (DES family).
- Modern substitution–permutation networks (AES).
- Practical modes of operation and attacker models.

---

## 1. Block Cipher Basics

- A block cipher deterministically maps `n`-bit plaintext blocks to `n`-bit ciphertext blocks using a `k`-bit secret key.
- Acts as the workhorse primitive for symmetric cryptography.
- Canonical parameters:
  - **3DES:** block `n = 64`, key `k = 168` bits (three 56-bit keys).
  - **AES:** block `n = 128`, key `k ∈ {128,192,256}` bits.

ASCII snapshot:

```
Key (k bits)
    |
    v
+---------+    Plaintext (n bits) --+--> Ciphertext (n bits)
|  Block  |                         |
| Cipher  |<-- Decryption ---------+
+---------+
```

---

## 2. Iterated Block Ciphers and Key Schedules

- Real-world designs iterate a simple round function `R(ki, ·)` many times.
- A key schedule expands master key `k` into subkeys `k1, k2, …, kr`.
- More rounds ⇒ more diffusion/confusion, but higher cost.

Pipeline picture:

```
        k  -- Key Expansion -->  k1, k2, …, kr
Plaintext  ---- R(k1, ·) ---- R(k2, ·) ---- … ---- R(kr, ·) ----> Ciphertext
```

---

## 3. Feistel Networks — Invertibility for Free

- Split the state into left/right halves (`L`, `R`).
- Each round mixes one half through an arbitrary function and XORs it into the other half.
- Works for any round functions `f1, …, fd : {0,1}^n → {0,1}^n`.

Encryption round:

```
Li+1 = Ri
Ri+1 = Li ⊕ fi(Ri)
```

ASCII diagram:

```
Round i
---------
Li ----+-------------------->
       |          +-----+
       +---> Ri --| fi  |--+
                  +-----+  |
                            v
                       Li ⊕ fi(Ri)
```

- **Invertibility proof idea:** swap roles of `L`/`R` and apply the same subkeys in reverse order.
- Decryption reuses identical hardware/software ⇒ efficient for Feistel designs (e.g., DES, 3DES).

---

## 4. DES Heritage and Timeline

- Early 1970s: IBM’s Lucifer (128-bit key & block) by Horst Feistel.
- 1976: NBS (now NIST) standardizes DES with 56-bit key, 64-bit block.
- Widely adopted in finance until practical brute-force attacks emerged.
- 1997–2006: successive DES cracking efforts reduce exhaustive search time to days/hours using FPGAs and distributed search.
- 2000: Rijndael selected as AES successor.

---

## 5. DES Architecture at a Glance

- 16-round Feistel network with 64-bit blocks.
- Initial permutation (IP) and final permutation (FP) are fixed bit shuffles (straight P-boxes) and inverses of each other.
- Round structure:
  1. Right half expanded from 32 → 48 bits via expansion permutation.
  2. XOR with 48-bit subkey.
  3. Pass through eight S-boxes `Si : {0,1}^6 → {0,1}^4`.
  4. Apply P-box permutation (32 bits).
  5. XOR into left half.

Round sketch:

```
Ri-1 (32) -- Expand --> 48 --⊕ ki--> 48 -- S-boxes --> 32 -- Permute --> f(Ri-1, ki)
Li-1 (32) -------------------------------------------------------⊕----------> Ri
Ri -------------------------------------------------------------------------> Li
```

- Decryption runs the same circuit in reverse key order.

---

## 6. Designing S-Boxes and P-Boxes

- Poor S-boxes (e.g., linear functions) lead to trivial linear/algebraic attacks.
- DES S-box criteria enforce nonlinearity, balanced outputs, and resistance to differential trails (per NSA & IBM heuristics).
- P-boxes spread S-box outputs to different inputs in the next round, promoting diffusion.

Mini example (good vs. bad S-box):

```
Input bits:  b1 b2 b3 b4 b5 b6
Bad choice:  linear combos only  ⇒ predictable ⇒ insecure
Good choice: non-linear mapping   ⇒ thwarts linear approximation
```

---

## 7. Exhaustive Search and Meet-in-the-Middle Attacks

- 56-bit key ⇒ `2^56 ≈ 7.2 × 10^16` possibilities.
- Historical DES challenge progress:
  - 1997 Internet search: 3 months.
  - 1998 EFF Deep Crack: 3 days (~$250K).
  - 1999 combined effort: 22 hours.
  - 2006 COPACOBANA (120 FPGAs): 7 days (~$10K).
- **Triple DES (3DES)** increases key size to 168 bits via `E_{k3}(D_{k2}(E_{k1}(m)))`, but still vulnerable to meet-in-the-middle with complexity ≈ `2^118`.
- **Double DES** (112-bit naive idea) is broken faster:
  1. Precompute `E_{k2}(m)` for all `k2` (table of size `2^56`).
  2. For each candidate `k1`, compute `D_{k1}(c)` and look up collisions.
  3. Complexity ≈ `2^57`, not `2^112`.

Table view:

```
Step 1: store pairs  (E_{k2}(m), k2 )  for all 2^56 keys.
Step 2: for each k1, compute D_{k1}(c); if it matches table entry ⇒ candidate key pair.
```

- **Meet-in-the-middle** extends to 3DES with more effort but still below `2^168`.

---

## 8. Quantum Exhaustive Search

- Grover’s algorithm speeds up generic search to `O(√|K|)` queries.
- DES effective security drops to ≈ `2^28` steps; AES-128 drops to ≈ `2^64`.
- Quantum advantage halves the exponent but does not break AES-256 level security.

---

## 9. AES Overview — Rijndael Family

- 1997: NIST launches AES competition; 2000: Rijndael announced winner.
- Block size fixed at 128 bits; keys of 128/192/256 bits with 10/12/14 rounds respectively.
- Structure: substitution–permutation network (SPN), not Feistel.

Round layout (AES-128):

```
State (4×4 bytes)
  ↓ AddRoundKey (k0)
  [Rounds 1..9]
    - SubBytes
    - ShiftRows
    - MixColumns
    - AddRoundKey
  [Final Round]
    - SubBytes
    - ShiftRows
    - AddRoundKey (k10)
```

- **State array:** column-major layout of 16 bytes.
- **SubBytes:** link each byte through an invertible S-box over GF(2^8).
- **ShiftRows:** cyclically shift rows by offsets (0,1,2,3).
- **MixColumns:** multiply each column by an MDS matrix in GF(2^8) ⇒ strong diffusion.
- **AddRoundKey:** XOR state with round key.
- **Key schedule:** expands 16-byte key into 176-byte output (11 round keys).

Diffusion snapshot:

```
Input State  → ShiftRows  → MixColumns
[a0 a4 a8 a12]   [a0 a4 a8 a12]   [b0 b4 b8 b12]
[a1 a5 a9 a13]   [a5 a9 a13 a1]   [b5 b9 b13 b1]
[a2 a6 a10 a14]  [a10 a14 a2 a6]  [b10 b14 b2 b6]
[a3 a7 a11 a15]  [a15 a3 a7 a11]  [b15 b3 b7 b11]
```

---

## 10. Modes of Operation — Encrypting Many Blocks

We rarely encrypt a single block; modes choreograph block cipher invocations.

### 10.1 Electronic Codebook (ECB)

- Encrypt each block independently.
- Pros: parallelizable, simple.
- Cons: identical plaintext blocks ⇒ identical ciphertext blocks (e.g., “penguin” image leak).

ASCII caution:

```
P1 --E--> C1
P2 --E--> C2   (if P1 = P2 ⇒ C1 = C2)
```

### 10.2 Cipher Block Chaining (CBC)

- XOR plaintext with previous ciphertext (or IV for first block) before encrypting.
- Pros: hides patterns, proven secure under chosen-plaintext when IV random.
- Cons: sequential dependency ⇒ no parallel encryption; IV must be unpredictable.

Diagram:

```
IV --->⊕--E--> C1
C1 --->⊕--E--> C2
C2 --->⊕--E--> C3
```

### 10.3 Cipher Feedback (CFB)

- Encrypt previous ciphertext, not plaintext; treat cipher as stream generator.
- Pros: self-synchronizing stream mode.
- Cons: still sequential; bit/byte granularity reduces throughput.

### 10.4 Output Feedback (OFB)

- Run block cipher as keystream generator independent of plaintext/ciphertext.
- Pros: precompute keystream; no error propagation.
- Cons: must never reuse IV; sensitive to keystream reuse like OTP.

### 10.5 Counter Mode (CTR)

- Encrypt counter+nonce to produce keystream, XOR with plaintext.
- Pros: fully parallelizable; random access to blocks; simple.
- Cons: catastrophic if nonce/counter reused with same key.

CTR sketch:

```
Nonce || 0  --E--> KS0  --⊕--> C0
Nonce || 1  --E--> KS1  --⊕--> C1
Nonce || 2  --E--> KS2  --⊕--> C2
```

### 10.6 Galois/Counter Mode (GCM)

- Adds built-in authentication (AEAD) by combining CTR encryption with GHASH.
- Ensures integrity of ciphertext and associated data (e.g., headers in network packets).
- Widely deployed: IEEE 802.1AE, WPA3, TLS 1.2/1.3, IPsec, SSH, satellite comms.

High-level flow:

```
CTR keystream → XOR with plaintext → Ciphertext
Ciphertext & AAD → GHASH (with key-dependent H) → Authentication tag
```

---

## 11. Questions of the Day

- How do Feistel networks ensure invertibility regardless of `fi`?
- Why is DES vulnerable to brute force and meet-in-the-middle?
- What properties make AES an SPN rather than Feistel?
- How do different modes trade off throughput, diffusion, and parallelism?
- What breaks if an IV or nonce is reused?

---

## 12. Key Takeaways for Exam Preparation

- Feistel structures allow easy decryption; AES needs distinct inverse steps.
- DES history underscores the importance of sufficient key length; 56 bits is obsolete.
- Meet-in-the-middle drastically reduces security of naive multi-encryption.
- Grover’s algorithm halves the security exponent but does not invalidate large-key AES.
- AES round functions (SubBytes, ShiftRows, MixColumns, AddRoundKey) work together to provide confusion + diffusion.
- Mode selection matters: ECB is only for toy data; prefer CBC/CTR/GCM depending on requirements.
- GCM brings both confidentiality and integrity; tag verification is mandatory before plaintext use.

Study checklist:

- Sketch a Feistel round and prove how decryption recovers plaintext.
- Memorize DES round steps and explain S-box design goals.
- Work through the meet-in-the-middle table for double DES.
- Calculate effective key strength under Grover for DES vs. AES-256.
- Practice mapping AES state bytes through a single round, including MixColumns math.
- Compare ECB, CBC, CFB, OFB, CTR, and GCM in terms of parallelism and error propagation.

Keep consolidating your notes—these primitives form the backbone of later topics like authenticated encryption, key exchange, and protocol design.
