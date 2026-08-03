---
layout: post
title: "[BITCOIN] BIP39 Seed Phrase Generation from Physical Randomness"
author: "Carlos Requena"
lang: en
ref: bip39-seed-phrase-generation-from-physical-randomness
tags:
   -  "Bitcoin"
---

<div style="text-align:center">
    <span style="color:red;font-weight: bold">"It isn't the events themselves that disturb people, but only their judgements about them..” </span> 
    <span style="color:black;font-weight: bold">--Epictetus</span>
</div>
<br>


> **A Technical Reference Guide to Coin Flips and Dice Rolls**

---

## 1. Overview

A **BIP39 mnemonic phrase** (commonly called a *seed phrase*) is a human-readable representation of cryptographic entropy. Instead of memorizing 256 random bits, you memorize a short list of ordinary words that encode exactly those bits.

The pipeline that turns physical randomness into a mnemonic is:

```
Physical randomness
        ↓
Binary entropy
        ↓
Checksum (SHA-256)
        ↓
Entropy + checksum
        ↓
Split into 11-bit groups
        ↓
BIP39 word lookup
        ↓
Mnemonic phrase
```

The physical randomness can come from:

* **Coin flips**
* **Dice rolls**
* **Hardware random number generators**

The single most important requirement is:

> The initial entropy must be **unpredictable** and **unbiased**.

Everything else in this guide is bookkeeping. If the entropy is weak, no amount of correct encoding will save the wallet.

---

## 2. BIP39 Entropy Sizes

BIP39 defines a fixed set of entropy sizes. Each size pairs an amount of entropy with a checksum whose length is `entropy_bits / 32`.

| Words | Entropy (ENT) | Checksum (CS) | Total Bits |
| ----- | ------------- | ------------- | ---------- |
| 12    | 128 bits      | 4 bits        | 132 bits   |
| 15    | 160 bits      | 5 bits        | 165 bits   |
| 18    | 192 bits      | 6 bits        | 198 bits   |
| 21    | 224 bits      | 7 bits        | 231 bits   |
| 24    | 256 bits      | 8 bits        | 264 bits   |

The checksum length follows directly from the entropy length:

```
checksum_bits = entropy_bits / 32
```

For example, `128 / 32 = 4` and `256 / 32 = 8`.

---

## 3. BIP39 Word List

The BIP39 English dictionary contains exactly **2048 words**, and that number is not arbitrary:

```
2048 = 2^11
```

Because there are `2^11` words, each word encodes exactly **11 bits**. That is why the total bit count always divides evenly by 11:

* `132 bits / 11 = 12 words`
* `264 bits / 11 = 24 words`

---

## 4. Method 1 — Generating Entropy with a Coin

### 4.1 Why a Coin Works

A fair coin has two equally likely outcomes, which maps perfectly onto a single binary digit:

| Outcome | Bit |
| ------- | --- |
| Heads   | `1` |
| Tails   | `0` |

Each flip therefore provides exactly **1 bit of entropy** — clean, unbiased, and with no conversion math required. This directness is what makes the coin the easiest source to reason about.

### 4.2 How Many Flips You Need

Since each flip is one bit, the number of flips equals the entropy size:

| Seed length | Entropy  | Coin flips |
| ----------- | -------- | ---------- |
| 12 words    | 128 bits | 128 flips  |
| 24 words    | 256 bits | 256 flips  |

A sequence such as `H T H H T T H T ...` becomes the binary string `1 0 1 1 0 0 1 0 ...`.

---

## 5. Coin Flip Conversion Process

### Step 1 — Record the flips

Using `Heads = 1` and `Tails = 0`, write down every flip in order:

```
1011010011011001...
```

This binary sequence **is** your entropy.

### Step 2 — Verify the length

Count the bits and confirm they match the target exactly — `128` bits for a 12-word mnemonic, `256` bits for a 24-word mnemonic. Do not pad, trim, or "round off" the sequence.

### Step 3 — Calculate the checksum

Compute the SHA-256 hash of the raw entropy **bytes** (not the text of the bit string):

```
checksum = first (entropy_bits / 32) bits of SHA-256(entropy)
```

* 128-bit entropy → take the **first 4 bits** of the hash.
* 256-bit entropy → take the **first 8 bits** of the hash.

### Step 4 — Append the checksum

Concatenate the checksum bits onto the end of the entropy:

```
Entropy   : 1011010011011001…   (128 or 256 bits)
Checksum  : 0110                 (4 or 8 bits)
Combined  : 1011010011011001…0110
```

The result is `132` bits (12 words) or `264` bits (24 words). *(The short strings above are illustrative — real entropy is 128 or 256 bits long.)*

### Step 5 — Split into 11-bit groups

Chop the combined sequence into groups of 11 bits:

```
10110100110  →  group 1
11001010101  →  group 2
...
```

Each 11-bit group is a number in the range `0` (`00000000000`) to `2047` (`11111111111`).

### Step 6 — Map numbers to words

Each number indexes into the BIP39 word list:

* Index `0` → the **first** word (`abandon`)
* Index `2047` → the **last** word (`zoo`)

Read the words out in order and you have your mnemonic.

---

## 6. Method 2 — Generating Entropy with Dice

### 6.1 Dice Entropy

A six-sided die has 6 equally likely outcomes, so each roll provides:

```
log2(6) ≈ 2.585 bits
```

That is more than double a coin flip, so you need far fewer physical actions. Because you must **round up** to guarantee at least the required number of bits:

| Seed length | Entropy  | Dice rolls (min) |
| ----------- | -------- | ---------------- |
| 12 words    | 128 bits | ~50 rolls        |
| 24 words    | 256 bits | ~100 rolls       |

(`128 / 2.585 ≈ 49.5 → 50`, and `256 / 2.585 ≈ 99.0 → 100`.)

### 6.2 Recording the Rolls

Map each face to a base-6 digit (`0`–`5`) so the results can be treated as one large number:

| Die face | Base-6 digit |
| -------- | ------------ |
| 1        | `0`          |
| 2        | `1`          |
| 3        | `2`          |
| 4        | `3`          |
| 5        | `4`          |
| 6        | `5`          |

A roll sequence of `3 6 2 1 5 4` becomes the digits `2 5 1 0 4 3`, i.e. the base-6 number `251043₆`.

---

## 7. Converting Dice Rolls to Entropy

There are two practical approaches. The second is what most hardware wallets actually use.

### 7.1 Base-6 Conversion (conceptual)

Interpret the whole roll sequence as one large base-6 number and convert it to binary. For the small example `251043₆`:

```
2×6⁵ + 5×6⁴ + 1×6³ + 0×6² + 4×6¹ + 3
  = 19035  (decimal)
  = 100101001011011  (binary)
```

This illustrates the idea, but naïvely truncating a base-6 number to a fixed bit length can introduce **slight bias**, because `6ⁿ` is never a power of two.

### 7.2 SHA-256 of the Rolls (recommended)

The clean, unbiased, and widely used method is to **hash the recorded rolls**:

```
entropy = SHA-256("digits from every roll, concatenated")
```

Roll ~50 or ~100 times, write the digits as a single string, hash it with SHA-256, and use the resulting bits as your entropy. Because SHA-256 output is uniformly distributed, this sidesteps base-conversion bias entirely. This is the approach used by tools such as Coldcard and SeedSigner.

---

## 8. Warning: Why Naïve Dice Encoding Is Biased

A die has six states, and six is **not** a power of two:

```
6 = 2 × 3     →     6 ≠ 2ⁿ
```

This means you **cannot** just assign each face a 3-bit pattern:

```
1 = 000   2 = 001   3 = 010
4 = 011   5 = 100   6 = 101
```

Three bits can represent 8 values, but a die only produces 6. The patterns `110` and `111` never appear, so this mapping **wastes states and skews the bit distribution** — the leading bit is far more likely to be `0` than `1`.

Use an unbiased method instead:

* **SHA-256 of the rolls** (recommended)
* **Base-6 → binary conversion** done carefully
* **Rejection sampling**

---

## 9. Complete BIP39 Pipeline

### Coin Method

```
256 coin flips
        ↓
256-bit entropy
        ↓
SHA-256  →  8 checksum bits
        ↓
264-bit sequence
        ↓
24 groups of 11 bits
        ↓
24 BIP39 words
```

### Dice Method

```
~100 dice rolls
        ↓
SHA-256(rolls)  →  256-bit entropy
        ↓
SHA-256  →  8 checksum bits
        ↓
264-bit sequence
        ↓
24 groups of 11 bits
        ↓
24 BIP39 words
```

---

## 10. Security Comparison

| Property                | Coin        | Dice          |
| ----------------------- | ----------- | ------------- |
| Entropy per event       | 1 bit       | ~2.585 bits   |
| Simplicity of encoding  | Excellent   | Moderate      |
| Chance of human error   | Lower       | Higher        |
| Physical speed          | Slower      | Faster        |
| Mathematical complexity | Low         | Higher        |

Both are secure **when done correctly**. The coin trades speed for simplicity; the die trades simplicity for speed.

---

## 11. Best Practices

### Use a fair physical source

**Good:**

* A quality, balanced coin
* Casino-grade dice
* Any genuinely random physical process

**Avoid:**

* Human-chosen numbers
* Recognizable patterns
* Dates, phone numbers, or "keyboard mashing"

Humans are notoriously bad at being random. The whole point of a physical source is to remove your brain from the loop.

### Keep entropy offline

Generate the entropy:

* **Offline**, on a trusted air-gapped device
* Away from **cameras** and onlookers
* With **no network access**

### Never reuse entropy

Every wallet must have its own unique randomness. Do **not**:

* Reuse a seed across wallets
* Modify or "improve" existing words by hand
* Change words to make them more memorable

---

## 12. The Role of the Checksum

A valid BIP39 phrase carries built-in error detection. If a single word is wrong, the checksum almost always fails and the wallet rejects the phrase as invalid.

It is important to understand what the checksum is **not**: it is not a security feature. It provides only:

* **Typo detection**
* **Phrase validation**

The real security comes entirely from the quality of the **random entropy**. A checksum on predictable entropy protects nothing.

---

## 13. Summary

The entire process, end to end:

```
Physical randomness
        ↓
Entropy extraction
        ↓
SHA-256 checksum
        ↓
Append checksum
        ↓
Split into 11-bit numbers
        ↓
Map numbers 0–2047 to words
        ↓
BIP39 mnemonic phrase
```

A **coin** yields randomness directly as binary — simple, slow, and hard to get wrong. A **die** yields more entropy per action but requires careful, unbiased conversion (ideally by hashing the rolls with SHA-256).

Performed correctly, both produce a mnemonic that is every bit as secure as one from a hardware RNG — with the added assurance that *you* watched the randomness happen.
