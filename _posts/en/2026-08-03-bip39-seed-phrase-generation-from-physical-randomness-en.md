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
* **Dice whose face count *is* a power of two** — a D8 gives a clean 3 bits and a D16 a clean 4 bits, which is exactly what Method 3 below exploits

---

## 9. Method 3 — D8 / D16 / D16 Dice (Direct Word Indexing)

### 9.1 Why This Combination Works

The whole problem with a D6 is that `6` is not a power of two. That problem disappears entirely when every die **is** a power of two:

| Die   | Faces     | Bits per roll |
| ----- | --------- | ------------- |
| D8    | `8 = 2³`  | 3 bits        |
| D16   | `16 = 2⁴` | 4 bits        |

And:

```
3 + 4 + 4 = 11 bits  →  exactly one BIP39 word
```

So **one D8 and two D16 dice, rolled together, select one word directly**. No base conversion, no SHA-256 over the rolls, no rejection sampling, no wasted states. Every outcome maps to exactly one of the 2048 words, and every word is equally likely.

This is the method documented by Blockstream for generating a recovery phrase offline:
<https://help.blockstream.com/generate-recovery-phrase-offline-d8-d16-d16>

### 9.2 The Dice

Label the three dice and **always read them in the same order**:

| Position | Die  | Range  |
| -------- | ---- | ------ |
| First    | D8   | 1–8    |
| Second   | D16  | 1–16   |
| Third    | D16  | 1–16   |

Use three visually distinct dice (different colours) so you can throw all three at once and still read them unambiguously. Order matters — swapping *Second* and *Third* gives a different word.

### 9.3 The Index Formula

```
index = (First − 1) × 2⁸  +  (Second − 1) × 2⁴  +  (Third − 1)
      = (First − 1) × 256 +  (Second − 1) × 16  +  (Third − 1)
```

The range is exactly right, with nothing left over:

```
minimum:  (1,1,1)     →  0×256 + 0×16 + 0   =    0    (abandon)
maximum:  (8,16,16)   →  7×256 + 15×16 + 15 = 2047    (zoo)
```

> **Dice numbered from zero.** Some D16 (hex) dice are labelled `0–F` and some D8 are labelled `0–7`. If a die already starts at `0`, use its face value directly and drop the `− 1` for that die.

Seen as bits, the three dice simply fill the 11-bit word index from left to right:

```
First  (D8)  → 3 bits   b10 b9 b8
Second (D16) → 4 bits   b7 b6 b5 b4
Third  (D16) → 4 bits   b3 b2 b1 b0
```

**Worked example** — you roll `First = 6`, `Second = 13`, `Third = 2`:

```
(6 − 1) × 256  +  (13 − 1) × 16  +  (2 − 1)
= 1280 + 192 + 1
= 1473

binary:  101  1100  0001   →   10111000001
```

Index `1473` is the **1474th** word in the BIP39 list, because index `0` is the first word (`abandon`).

### 9.4 How Many Rolls You Need

| Seed length | Words | Roll sets    | Total die throws |
| ----------- | ----- | ------------ | ---------------- |
| 12 words    | 12    | 12 sets of 3 | 36               |
| 24 words    | 24    | 24 sets of 3 | 72               |

Compare that with `256` coin flips or `~100` D6 rolls plus a hand-verified SHA-256. Each set of three is also **independently checkable**: if you mis-record one word, only that word is wrong, and the rest of the phrase is unaffected.

### 9.5 The Last Word Is Not Free

This is the one catch, and it is easy to get wrong.

Because this method produces words *directly*, it bypasses the "entropy → checksum → split" pipeline. Words `1…11` (or `1…23`) are pure entropy and can be taken exactly as rolled. The **final** word cannot, because part of it is the checksum:

| Seed length | Total bits | Final word = entropy bits + checksum bits |
| ----------- | ---------- | ----------------------------------------- |
| 12 words    | 132        | **7** entropy bits + **4** checksum bits  |
| 24 words    | 264        | **3** entropy bits + **8** checksum bits  |

So roll the last set normally, then keep only the leading bits of that provisional word and let the checksum determine the rest:

* **12 words** — keep the **first 7 bits** of the provisional 12th word. `2⁴ = 16` words share that prefix; exactly **one** of them has a valid checksum.
* **24 words** — keep the **first 3 bits** of the provisional 24th word. `2⁸ = 256` words share that prefix; exactly **one** of them has a valid checksum.

Those leading bits are not decoration — they are real entropy, and they are what completes the count:

```
12 words:  11 words × 11 bits + 7 bits = 121 + 7 = 128 bits
24 words:  23 words × 11 bits + 3 bits = 253 + 3 = 256 bits
```

Use an **offline, trusted** BIP39 tool or hardware wallet to work out which candidate is valid. Most hardware wallets do this for you: you type the first 11 (or 23) words and the device only offers valid final words — pick the one matching your rolled prefix.

> **Do not just re-roll until the last word happens to be valid.** It is technically unbiased (`2⁷ = 128` of the 2048 words are valid last words for a 12-word phrase, `2³ = 8` for a 24-word phrase), but it needs an average of 16 re-rolls for 12 words and **256** for 24 words — and you still need a checksum tool to know when to stop.

### 9.6 Why This Method Is Worth the Extra Dice

| | |
| --- | --- |
| **No bias, by construction** | 8 and 16 are powers of two, so no state is ever wasted or rejected. |
| **No hashing of entropy** | SHA-256 is only needed for the single final word, not for the entropy itself. |
| **Fewest physical actions** | 72 throws for a 24-word seed, versus ~100 rolls or 256 flips. |
| **Auditable** | Each word traces back to three recorded numbers you can re-check one at a time. |
| **Trade-off** | You need specialist dice, and a fair D16 is harder to source and verify than a fair coin. |

---

## 10. Complete BIP39 Pipeline

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

### D8/D16/D16 Method

```
24 sets of (D8, D16, D16)
        ↓
index = (D8−1)×256 + (D16−1)×16 + (D16−1)
        ↓
24 word indices, 0–2047
        ↓
words 1–23 kept as rolled
        ↓
word 24: keep first 3 bits,
last 8 bits = SHA-256 checksum
        ↓
24 BIP39 words
```

---

## 11. Security Comparison

| Property                | Coin      | Dice (D6)                | D8/D16/D16              |
| ----------------------- | --------- | ------------------------ | ----------------------- |
| Entropy per event       | 1 bit     | ~2.585 bits              | 3–4 bits                |
| Events for 24 words     | 256 flips | ~100 rolls               | 72 throws (24 × 3)      |
| Simplicity of encoding  | Excellent | Moderate                 | Excellent               |
| Bias risk               | None      | Real, if done naïvely    | None, by construction   |
| Hashing required        | Checksum  | Entropy **and** checksum | Final word only         |
| Chance of human error   | Lower     | Higher                   | Low                     |
| Physical speed          | Slower    | Faster                   | Fastest                 |
| Mathematical complexity | Low       | Higher                   | Low                     |
| Equipment               | Any coin  | Any die                  | Specialist D8/D16 dice  |

All three are secure **when done correctly**. The coin trades speed for simplicity; the D6 trades simplicity for speed; the D8/D16/D16 set buys both back at the cost of needing dice you cannot pick up in any shop.

---

## 12. Best Practices

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

## 13. The Role of the Checksum

A valid BIP39 phrase carries built-in error detection. If a single word is wrong, the checksum almost always fails and the wallet rejects the phrase as invalid.

It is important to understand what the checksum is **not**: it is not a security feature. It provides only:

* **Typo detection**
* **Phrase validation**

The real security comes entirely from the quality of the **random entropy**. A checksum on predictable entropy protects nothing.

---

## 14. Summary

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

A **coin** yields randomness directly as binary — simple, slow, and hard to get wrong. A **D6** yields more entropy per action but requires careful, unbiased conversion (ideally by hashing the rolls with SHA-256). A **D8 plus two D16** skips the conversion entirely: three throws are 11 bits are one word, with the only special case being the checksum-bearing final word.

Performed correctly, both produce a mnemonic that is every bit as secure as one from a hardware RNG — with the added assurance that *you* watched the randomness happen.
