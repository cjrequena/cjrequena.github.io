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
    <span style="color:red;font-weight: bold">"It isn't the events themselves that disturb people, but only their judgements about them."</span> 
    <span style="color:black;font-weight: bold">--Epictetus</span>
</div>
<br>


> **A Technical Reference Guide to Coin Flips and Dice Rolls**

---

## Index

| # | Section | What it covers |
| --- | --- | --- |
| 1 | [Overview](#overview) | The entropy → checksum → words pipeline, and why you might not want to trust a device's RNG. |
| 2 | [BIP39 Entropy Sizes](#entropy-sizes) | Word counts, entropy lengths, checksum lengths, and total bits. |
| 3 | [The BIP39 Word List](#word-list) | Why 2048 words means exactly 11 bits per word, and why the language matters. |
| 4 | [Method 1 — Generating Entropy with a Coin](#method-coin) | Heads/tails as raw bits, how many flips you need, and what to do about a biased coin. |
| 5 | [Coin Flip Conversion Process](#coin-conversion) | Step by step: record, verify, checksum, append, split, map to words — with an offline script. |
| 6 | [Method 2 — Generating Entropy with Dice](#method-dice) | ~2.585 bits per D6 roll, roll counts, and base-6 recording. |
| 7 | [Converting Dice Rolls to Entropy](#dice-to-entropy) | Base-6 conversion versus the recommended SHA-256 of the rolls. |
| 8 | [Warning: Why Naïve Dice Encoding Is Biased](#dice-bias) | Why a 3-bit-per-face mapping skews the bit distribution. |
| 9 | [Method 3 — D8 / D16 / D16 Dice](#method-d8-d16) | Three throws = 11 bits = one word, the index formula, and the checksum-bearing final word. |
| 10 | [Complete BIP39 Pipeline](#pipeline) | End-to-end diagrams for all three methods. |
| 11 | [Security Comparison](#security-comparison) | Coin vs D6 vs D8/D16/D16 across entropy, bias, effort, and error risk. |
| 12 | [From Mnemonic to Wallet Seed](#mnemonic-to-seed) | What BIP39 does after the words: PBKDF2, the optional passphrase, and the 512-bit seed. |
| 13 | [The Role of the Checksum](#checksum-role) | What the checksum does — and what it does not protect. |
| 14 | [Verify Before You Fund the Wallet](#verification) | Restore-testing the backup against a second implementation. |
| 15 | [Best Practices](#best-practices) | Fair physical sources, staying offline, and never reusing entropy. |
| 16 | [Summary](#summary) | The whole process condensed, and how the three methods compare. |

---

## 1. Overview {#overview}

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

### Why bother with coins and dice at all?

A hardware wallet's built-in RNG is almost certainly fine. The reason to do this by hand is that "almost certainly" is a claim you cannot verify: a compromised or subtly broken generator produces output that looks perfectly random to you while being entirely predictable to whoever broke it. This has happened in the field more than once.

Physical randomness moves the trust from the device to a process you can watch. It does not make the wallet *more* random — 256 bits is 256 bits — it makes the randomness **auditable by you**.

---

## 2. BIP39 Entropy Sizes {#entropy-sizes}

BIP39 defines a fixed set of entropy sizes. The entropy must be a multiple of 32 bits, between 128 and 256 bits, and each size pairs with a checksum whose length is `entropy_bits / 32`.

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

> **12 or 24 words?** 128 bits is already far beyond brute force, and no realistic advance changes that. 24 words buys margin against implementation mistakes and partial backup loss, not against an attacker with a bigger computer. Both are fine; 24 costs you roughly twice the physical work described below.

---

## 3. The BIP39 Word List {#word-list}

The BIP39 English dictionary contains exactly **2048 words**, and that number is not arbitrary:

```
2048 = 2^11
```

Because there are `2^11` words, each word encodes exactly **11 bits**. That is why the total bit count always divides evenly by 11:

* `132 bits / 11 = 12 words`
* `264 bits / 11 = 24 words`

Two properties of the list are worth knowing before you start writing words down:

* **The first four letters are unique.** No two words in the English list share their first four letters, so `abandon` can be recorded as `aban` without ambiguity. Hardware wallets rely on this when they let you type only four characters.
* **The language is part of the encoding.** The same index means a different word in the Spanish, French, or Japanese list. A phrase is only recoverable if you know which word list produced it, so unless you have a reason otherwise, use English — it is what every wallet supports.

Use the official list from the BIP39 specification. A "similar" list found elsewhere will silently produce a different wallet.

---

## 4. Method 1 — Generating Entropy with a Coin {#method-coin}

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

### 4.3 If You Do Not Trust Your Coin

Real coins are not perfectly fair. A flipped coin caught in the hand lands the **same side up** it started roughly 50.8% of the time — a physical wobble effect predicted by Diaconis and later confirmed over 350,757 recorded flips. Spinning a coin on a table is far worse, and can be biased by several percent.

A 50.8% bias costs you almost nothing — across all 256 flips it removes less than a **twentieth of one bit** (256 × H(0.508) ≈ 255.95). Two habits shrink it further at no cost: flip properly and let the coin land on the floor rather than catching it, and vary which face starts upward, since the effect is a *same-side* bias rather than a heads bias. If you would rather not rely on any of that, two fixes remove the bias entirely:

**Von Neumann extraction.** Flip in *pairs* and discard the ties:

| Pair       | Output    |
| ---------- | --------- |
| Heads-Tails | `1`      |
| Tails-Heads | `0`      |
| Heads-Heads | discard  |
| Tails-Tails | discard  |

Because `HT` and `TH` have identical probability for *any* fixed bias, the output bits are perfectly uniform. The cost is throughput: at best you keep one bit per four flips, so a 24-word seed needs around **1024 flips** on average. Correct, and tedious.

**Hash the flips.** Record all 256 flips as a string and run it through SHA-256, exactly as the dice method does in [§7.2](#dice-to-entropy). This removes bias without discarding anything, and it is what most tooling does in practice.

Either way, the rule is the same: **do not correct a suspected bias by hand**. Adjusting your own results reintroduces exactly the human non-randomness you were trying to avoid.

---

## 5. Coin Flip Conversion Process {#coin-conversion}

### Step 1 — Record the flips

Using `Heads = 1` and `Tails = 0`, write down every flip in order:

```
1011010011011001...
```

This binary sequence **is** your entropy.

### Step 2 — Verify the length

Count the bits and confirm they match the target exactly — `128` bits for a 12-word mnemonic, `256` bits for a 24-word mnemonic. Do not pad, trim, or "round off" the sequence.

Writing the bits in groups of eight makes the count checkable at a glance: 16 groups for 128 bits, 32 groups for 256 bits.

### Step 3 — Calculate the checksum

Compute the SHA-256 hash of the raw entropy **bytes** (not the text of the bit string):

```
checksum = first (entropy_bits / 32) bits of SHA-256(entropy)
```

* 128-bit entropy → take the **first 4 bits** of the hash.
* 256-bit entropy → take the **first 8 bits** of the hash.

This is the one step you cannot do in your head. Hashing `"1011..."` as ASCII text instead of as bytes is the single most common mistake here, and it produces a phrase that is valid-looking but wrong.

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

### Doing steps 3–6 offline

Steps 3 through 6 are mechanical, and doing them by hand is error-prone. On an **air-gapped machine you will wipe afterwards**, the whole conversion is a few lines:

```python
import hashlib

bits = "1011010011011001..."          # your recorded flips, 128 or 256 of them
assert len(bits) in (128, 160, 192, 224, 256)

entropy  = int(bits, 2).to_bytes(len(bits) // 8, "big")
digest   = hashlib.sha256(entropy).digest()
checksum = format(int.from_bytes(digest, "big"), "0256b")[: len(bits) // 32]
combined = bits + checksum

indices = [int(combined[i:i + 11], 2) for i in range(0, len(combined), 11)]
print(indices)                        # look these up in the BIP39 English word list
```

Never run this — or any BIP39 tool — on a machine that is online, and never paste your bits into a website, however reputable it looks.

---

## 6. Method 2 — Generating Entropy with Dice {#method-dice}

### 6.1 Dice Entropy

A six-sided die has 6 equally likely outcomes, so each roll provides:

```
log2(6) ≈ 2.585 bits
```

That is more than double a coin flip, so you need far fewer physical actions. Round **up**, so that the rolls are guaranteed to carry at least the required number of bits:

| Seed length | Entropy  | Dice rolls (min) |
| ----------- | -------- | ---------------- |
| 12 words    | 128 bits | ~50 rolls        |
| 24 words    | 256 bits | ~100 rolls       |

(`128 / 2.585 ≈ 49.5 → 50`, and `256 / 2.585 ≈ 99.0 → 100`.)

Checking it the other way round confirms the margin: 50 rolls produce `6⁵⁰ ≈ 2¹²⁹` equally likely sequences, and 100 rolls produce `6¹⁰⁰ ≈ 2²⁵⁸·⁵` — comfortably above 128 and 256 bits respectively.

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

## 7. Converting Dice Rolls to Entropy {#dice-to-entropy}

There are two practical approaches. The second is what most hardware wallets actually use.

### 7.1 Base-6 Conversion (conceptual)

Interpret the whole roll sequence as one large base-6 number and convert it to binary. For the small example `251043₆`:

```
2×6⁵ + 5×6⁴ + 1×6³ + 0×6² + 4×6¹ + 3
  = 15552 + 6480 + 216 + 0 + 24 + 3
  = 22275  (decimal)
  = 101011100000011  (binary)
```

This illustrates the idea, but naïvely truncating a base-6 number to a fixed bit length can introduce **slight bias**, because `6ⁿ` is never a power of two.

### 7.2 SHA-256 of the Rolls (recommended)

The clean, unbiased, and widely used method is to **hash the recorded rolls**:

```
entropy = SHA-256("digits from every roll, concatenated")
```

Roll ~50 or ~100 times, write the digits as a single string, hash it with SHA-256, and use the resulting bits as your entropy. Because SHA-256 output is computationally indistinguishable from uniform, this sidesteps base-conversion bias entirely. This is the approach used by tools such as Coldcard and SeedSigner.

Two details matter:

* **For a 12-word seed, take the first 128 bits** of the 256-bit digest. For 24 words, use all 256 bits.
* **Hashing does not create entropy.** SHA-256 always emits 256 bits, but those bits carry only as much randomness as went in. Fifty D6 rolls hashed into a 24-word phrase gives you a 24-word phrase with roughly **129 bits** of real security, not 256. If you want 256 bits, you must roll ~100 times. The output looks identical either way, which is what makes this mistake dangerous.

---

## 8. Warning: Why Naïve Dice Encoding Is Biased {#dice-bias}

A die has six states, and six is **not** a power of two:

```
6 = 2 × 3     →     6 ≠ 2ⁿ
```

This means you **cannot** just assign each face a 3-bit pattern:

```
1 = 000   2 = 001   3 = 010
4 = 011   5 = 100   6 = 101
```

Three bits can represent 8 values, but a die only produces 6. The patterns `110` and `111` never appear, so this mapping **wastes states and skews the bit distribution**: four of the six faces begin with `0`, so the leading bit is `0` two-thirds of the time instead of half. Across a whole seed that is a large, systematic loss — an attacker who knows you used this mapping searches a much smaller space than the word count suggests.

Use an unbiased method instead:

* **SHA-256 of the rolls** (recommended)
* **Base-6 → binary conversion** done carefully
* **Rejection sampling** — read two rolls as a number `0–35`, discard `32–35`, and keep the remaining 32 outcomes as a clean 5 bits
* **Dice whose face count *is* a power of two** — a D8 gives a clean 3 bits and a D16 a clean 4 bits, which is exactly what Method 3 below exploits

---

## 9. Method 3 — D8 / D16 / D16 Dice (Direct Word Indexing) {#method-d8-d16}

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

If you would rather compute it yourself, again on an air-gapped machine:

```python
import hashlib

indices = [ ... ]     # the 11 or 23 words you rolled, as indices 0–2047
last    = 1473        # the provisional final word, exactly as rolled

n_ent  = 128 if len(indices) == 11 else 256
n_keep = n_ent - len(indices) * 11        # 7 bits (12 words) or 3 bits (24 words)
n_free = 11 - n_keep
prefix = format(last, "011b")[:n_keep]

for tail in range(2 ** n_free):
    cand = int(prefix + format(tail, "0" + str(n_free) + "b"), 2)
    bits = "".join(format(i, "011b") for i in indices + [cand])
    ent  = int(bits[:n_ent], 2).to_bytes(n_ent // 8, "big")
    cs   = format(int.from_bytes(hashlib.sha256(ent).digest(), "big"), "0256b")[: n_ent // 32]
    if bits[n_ent:] == cs:
        print("valid final index:", cand)
```

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

## 10. Complete BIP39 Pipeline {#pipeline}

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

## 11. Security Comparison {#security-comparison}

| Property                | Coin      | Dice (D6)                | D8/D16/D16              |
| ----------------------- | --------- | ------------------------ | ----------------------- |
| Entropy per event       | 1 bit     | ~2.585 bits              | 3–4 bits                |
| Events for 24 words     | 256 flips | ~100 rolls               | 72 throws (24 × 3)      |
| Simplicity of encoding  | Excellent | Moderate                 | Excellent               |
| Encoding bias risk      | None      | Real, if done naïvely    | None, by construction   |
| Physical bias risk      | ~0.8%, negligible | Low, with fair dice | Low, with fair dice  |
| Hashing required        | Checksum  | Entropy **and** checksum | Final word only         |
| Recording burden        | 256 bits  | ~100 digits              | 72 digits, self-grouped |
| Chance of human error   | Moderate  | Highest                  | Lowest                  |
| Physical speed          | Slower    | Faster                   | Fastest                 |
| Mathematical complexity | Low       | Higher                   | Low                     |
| Equipment               | Any coin  | Any die                  | Specialist D8/D16 dice  |

All three are secure **when done correctly**. The coin trades speed for simplicity; the D6 trades simplicity for speed; the D8/D16/D16 set buys both back at the cost of needing dice you cannot pick up in any shop.

---

## 12. From Mnemonic to Wallet Seed {#mnemonic-to-seed}

The words are not the wallet. BIP39 defines one more step, which turns the mnemonic into the 512-bit seed that BIP32 uses to derive every key and address:

```
mnemonic words  +  optional passphrase
        ↓
PBKDF2-HMAC-SHA512
password = mnemonic sentence
salt     = "mnemonic" + passphrase
2048 iterations, 64-byte output
        ↓
512-bit seed
        ↓
BIP32 master key  →  accounts  →  addresses
```

Three consequences are worth understanding before you rely on a passphrase:

* **The passphrase is not checksummed.** The mnemonic is; the passphrase is not. Any passphrase is "valid", so a single typo silently derives a completely different, empty wallet — with no error message. If you use one, back it up as carefully as the words, and separately from them.
* **Every passphrase gives a real wallet.** This is what makes hidden or decoy wallets possible: the same 24 words plus different passphrases are different wallets, and nothing in the backup reveals how many exist.
* **The passphrase is not a substitute for entropy.** 2048 PBKDF2 iterations is a very light stretching by modern standards. A guessable passphrase over a stolen mnemonic is brute-forceable; it protects a lost backup only if it is itself high-entropy.

If you skip the passphrase, the salt is simply the string `"mnemonic"` — which is the default every wallet uses.

---

## 13. The Role of the Checksum {#checksum-role}

A valid BIP39 phrase carries built-in error detection. If a single word is wrong, the checksum almost always fails and the wallet rejects the phrase as invalid.

"Almost always" is literal: a 24-word phrase has 8 checksum bits, so a random corruption still passes by chance about `1/256` of the time, and a 12-word phrase (4 bits) about `1/16` of the time. The checksum catches careless mistakes; it is not a guarantee.

It is important to understand what the checksum is **not**: it is not a security feature. It provides only:

* **Typo detection**
* **Phrase validation**

The real security comes entirely from the quality of the **random entropy**. A checksum on predictable entropy protects nothing.

---

## 14. Verify Before You Fund the Wallet {#verification}

A seed phrase you have never restored from is a backup you have never tested. Before sending anything of value:

1. **Check the phrase validates.** Enter it into your offline tool or hardware wallet and confirm the checksum is accepted.
2. **Wipe and restore.** Reset the device and recover it from the words *as you wrote them down* — not from what is still on screen. This is the step that catches a mis-copied word, a wrong order, or an illegible character in your backup.
3. **Confirm the same first address.** After restoring, the first receive address must match what you saw before the wipe. If it does not, check the **derivation path** before panicking — BIP44 (`m/44'/0'/0'`), BIP49, BIP84 (`m/84'/0'/0'`, native SegWit) and BIP86 (Taproot) produce entirely different addresses from the same seed.
4. **Cross-check with a second implementation.** Deriving the first address with an independent offline tool confirms you are not depending on one vendor's bug.
5. **Send a small test amount first**, and only then the rest.

If you used a passphrase, repeat the whole restore *with* it — an untested passphrase is the most common way people lock themselves out permanently.

---

## 15. Best Practices {#best-practices}

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

Never use a website to convert your flips or rolls into words, even one that claims to work offline — you cannot verify what the page you actually loaded is doing. The same applies to phone photos, cloud notes, password managers that sync, and printers with storage.

### Never reuse entropy

Every wallet must have its own unique randomness. Do **not**:

* Reuse a seed across wallets
* Modify or "improve" existing words by hand
* Change words to make them more memorable

Altering a valid phrase by hand almost always breaks the checksum, and in the rare cases it does not, you have quietly reduced your entropy to something an attacker can search.

### Record the backup properly

* Write the words **numbered and in order** — order is part of the secret.
* Prefer a durable medium (stamped steel) over paper for anything long-lived.
* Note the word list language and the derivation path alongside the backup; neither is secret, and both save you later.

---

## 16. Summary {#summary}

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
        ↓
PBKDF2 (+ optional passphrase)
        ↓
512-bit wallet seed
```

A **coin** yields randomness directly as binary — simple, slow, and hard to get wrong. A **D6** yields more entropy per action but requires careful, unbiased conversion (ideally by hashing the rolls with SHA-256). A **D8 plus two D16** skips the conversion entirely: three throws = 11 bits = one word, with the only special case being the checksum-bearing final word.

Performed correctly, all three produce a mnemonic that is every bit as secure as one from a hardware RNG — with the added assurance that *you* watched the randomness happen.
