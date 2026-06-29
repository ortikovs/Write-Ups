# 🏺 Mysteries of the Tomb — CTF Write-Up

> *"In the shadowed depths of the Valley of Kings, a pyramid of secrets awaits those who dare to read between the stones."*

---

## 📋 Challenge Overview

| Field       | Details                         |
|-------------|----------------------------------|
| **Name**    | Mysteries of the Tomb            |
| **Points**  | 100                              |
| **Author**  | Riley Grimwood                   |
| **Category**| Cryptography / Encoding          |
| **Flag**    | `pecan{TH3C1F3ROF4NCH}`         |

---

## 🗂️ Given Files

Two artifacts accompany the challenge:

- **`flag.txt`** — A mysterious pyramid of Egyptian hieroglyphs etched into a stone tablet.
- **`notes.txt`** — A scholar's field notes mapping each hieroglyph to a Latin letter or digit.

And an inscription from the tablet's apex:

> *"To unveil the truth, read the stones at dawn's first light, tracing from the summit down to the shifting sands."*

---

## 🔍 Step 1 — Examining the Tablet

Opening `flag.txt` reveals a pyramid of ancient glyphs, beautifully structured in five descending rows:

```
        𓏏
       𓉔 𓏼
      ☥ 𓏺 𓆑
     𓏼 𓂋 𓅱 𓆑
    𓏽 𓈖 ☥ 𓉔
```

Five rows. Fifteen glyphs total. A pyramid — classically significant in Egyptian lore, narrow at the apex and wide at the base, like a pharaoh's monument piercing the sky.

---

## 📜 Step 2 — Building the Cipher Key

`notes.txt` serves as a Rosetta Stone, pairing each glyph with a character from our modern alphabet or numeral system. Extracting only the mappings that matter:

| Glyph | Value | Notes Entry                                       |
|-------|-------|---------------------------------------------------|
| `𓏏`  | `T`   | Shaped like bread — offerings or truth            |
| `𓉔`  | `H`   | Appears in royal names — heritage or house        |
| `𓏼`  | `3`   | Triple tally — seen in timekeeping artifacts      |
| `☥`   | `C`   | The ankh — commonly misread as simply "life"      |
| `𓏺`  | `1`   | A single tally — simple, yet powerful             |
| `𓆑`  | `F`   | Seemingly a viper with horns                      |
| `𓂋`  | `R`   | Appears near temple inscriptions                  |
| `𓅱`  | `O`   | Waterfowl — found near agricultural records       |
| `𓏽`  | `4`   | Quad marks — seasons or pillars                   |
| `𓈖`  | `N`   | Curving like the Nile — tied to water             |

> **Key Observation:** The ☥ (ankh) maps to `C` — not the `A` one might naively assume. This is the central misdirection of the puzzle, warned about in the notes themselves: *"commonly misread."*

---

## 🔓 Step 3 — Decoding the Pyramid

The inscription's guidance is the final key:

> *"tracing from the summit down to the shifting sands"*

This tells us to read **top-to-bottom**, then **left-to-right** within each row — as natural as reading sand dunes from peak to base.

Applying the cipher:

| Row | Glyphs          | Decoded Characters |
|-----|-----------------|---------------------|
| 1   | `𓏏`            | `T`                |
| 2   | `𓉔` `𓏼`      | `H` `3`            |
| 3   | `☥` `𓏺` `𓆑` | `C` `1` `F`        |
| 4   | `𓏼` `𓂋` `𓅱` `𓆑` | `3` `R` `O` `F` |
| 5   | `𓏽` `𓈖` `☥` `𓉔` | `4` `N` `C` `H` |

Concatenating all characters from apex to base:

```
T H 3 C 1 F 3 R O F 4 N C H
```

---

## 💡 Step 4 — Reading the Message

The decoded string `TH3C1F3ROF4NCH` is written in **leet speak**, with two layers of encoding at play:

- **Numeric substitution:** `3 = E`, `1 = I`, `4 = A`
- **Phonetic substitution:** `F = PH` (a known phonetic equivalence — F sounds like PH in English)

Translating:

| Leet Token | Plain Text |
|------------|------------|
| `TH3`      | `THE`      |
| `C1F3R`    | `CIPHER`   |
| `OF`       | `OF`       |
| `4NCH`     | `ANCH`     |

> **The full message: "THE CIPHER OF ANCH"**

The ankh (`☥`) — the Egyptian symbol of life — is the very key used to encode the message. The cipher is *of* the ankh. It's poetic, elegant, and hiding in plain sight the entire time.

---

## 🏁 Flag

```
pecan{TH3C1F3ROF4NCH}
```

---

## 🧠 Key Takeaways

- **Don't assume obvious mappings.** The ankh `☥` maps to `C`, not `A` — the notes explicitly warn about misreading it.
- **The story is the hint.** "Dawn's first light, tracing from summit to sands" isn't flavour text — it's the reading order instruction.
- **Leet speak + phonetics.** `F` as `PH` is a subtle twist that rewards solvers who think phonetically, not just visually.
- **Elimination is powerful.** The notes contain 28 entries but only 10 glyphs appear in the pyramid. Cross-referencing the two files immediately narrows the search space.

---

*Solved with a keen eye, a scholar's notes, and the wisdom to read as the ancients wrote — from the heavens downward.*

🏺 *The pharaoh's secret is yours now.*
