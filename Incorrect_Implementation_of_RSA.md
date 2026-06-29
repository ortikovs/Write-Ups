# 🔐 Incorrect Implementation of RSA

> **Category:** Cryptography  
> **Platform:** [practice.icoa2026.au](https://practice.icoa2026.au/)  
> **Difficulty:** Easy  
> **Flag:** `pecan{oh_i_really_thought_i_had_that_done_c0rr3ctly_11629}`

---

## 📜 Challenge Description

> *"I just learnt about Rivest–Shamir–Adleman, and so I created my own custom implementation. Here's the message:"*

```
n: 16537241065399537261146800802060451995107796665337288928060948677362154976656429797729550619497788311160926523026781503470362013597201944839389519773564618679827061417896265475971561610333659217333638238386907603525565178455941971399130722191602944445714002268747028340120907894781607422707823554701443768586256913491149809410232167277063066105859165079765281480076330718726350243973636606134346374770537701812923215229226027759112780757449828410180237267791126609342382918352166823253106960191346933601235547281

e: 5

ciphertext: [17623416832, 10510100501, 9509900499, 8587340257, 16105100000,
28153056843, 16850581551, 12166529024, 7737809375, 12762815625, 7737809375,
19254145824, 10510100501, 8587340257, 14693280768, 14693280768, 25937424601,
7737809375, 21003416576, 12166529024, 16850581551, 21924480357, 11592740743,
12166529024, 21003416576, 7737809375, 12762815625, 7737809375, 12166529024,
8587340257, 10000000000, 7737809375, 21003416576, 12166529024, 8587340257,
21003416576, 7737809375, 10000000000, 16850581551, 16105100000, 10510100501,
7737809375, 9509900499, 254803968, 19254145824, 19254145824, 345025251,
9509900499, 21003416576, 14693280768, 25937424601, 7737809375, 282475249,
282475249, 459165024, 312500000, 601692057, 30517578125]
```

---

## 🔍 Initial Observations

The title says it all — this isn't about breaking RSA through factoring. The bug is **in the implementation itself**.

Two things immediately stood out:

1. **The ciphertext values are suspiciously tiny.** The modulus `n` is a 2048-bit number, yet the ciphertext values are in the range of tens of millions — nowhere near `n`'s magnitude.
2. **The public exponent is `e = 5`**, which is small.

These two clues point to a classic textbook vulnerability.

---

## 💡 The Vulnerability

In standard RSA, encryption works as:

```
c = m^e mod n
```

The `mod n` step is what makes RSA secure — it scrambles the value. But what happens if `m` is so small that `m^e` never exceeds `n`?

```
If m^e < n  →  m^e mod n = m^e
```

The modulo operation becomes a no-op. The ciphertext is just the plaintext raised to the power of `e` — **completely reversible** without knowing any private key.

For an ASCII character like `p` (decimal `112`):

```
112^5 = 17,623,416,832
```

And indeed — `17623416832` is the very first value in the ciphertext list. 🎯

The implementer forgot (or didn't realise) that encrypting characters **byte by byte** with a small exponent completely breaks RSA. Each character is encrypted independently, and since ASCII values max out at 127, `127^5 ≈ 3.3 × 10^10` — a tiny fraction of `n`.

---

## 🛠️ Solution

The fix is simple: compute the **5th integer root** of each ciphertext value to recover the original ASCII byte.

```python
ciphertext = [
    17623416832, 10510100501, 9509900499, 8587340257, 16105100000,
    28153056843, 16850581551, 12166529024, 7737809375, 12762815625,
    7737809375,  19254145824, 10510100501, 8587340257, 14693280768,
    14693280768, 25937424601, 7737809375,  21003416576, 12166529024,
    16850581551, 21924480357, 11592740743, 12166529024, 21003416576,
    7737809375,  12762815625, 7737809375,  12166529024, 8587340257,
    10000000000, 7737809375,  21003416576, 12166529024, 8587340257,
    21003416576, 7737809375,  10000000000, 16850581551, 16105100000,
    10510100501, 7737809375,  9509900499,  254803968,   19254145824,
    19254145824, 345025251,   9509900499,  21003416576, 14693280768,
    25937424601, 7737809375,  282475249,   282475249,   459165024,
    312500000,   601692057,   30517578125
]

def integer_nth_root(x, n):
    """Binary search for the exact integer nth root of x."""
    low, high = 0, x
    while low <= high:
        mid = (low + high) // 2
        power = mid ** n
        if power == x:
            return mid
        elif power < x:
            low = mid + 1
        else:
            high = mid - 1
    return high

plaintext = "".join(chr(integer_nth_root(c, 5)) for c in ciphertext)
print(plaintext)
```

**Output:**
```
pecan{oh_i_really_thought_i_had_that_done_c0rr3ctly_11629}
```

---

## 🏁 Flag

```
pecan{oh_i_really_thought_i_had_that_done_c0rr3ctly_11629}
```

---

## 📚 Key Takeaways

| Mistake | Why It's Dangerous |
|---|---|
| Encrypting each byte independently | Leaks character frequency; trivially reversible |
| Small `e` with small `m` | `m^e < n` means modulo never fires |
| No padding (e.g. OAEP) | Even "correct" RSA needs padding to be secure |

> **Lesson:** Never roll your own crypto. RSA is only secure when implemented with proper padding schemes (like PKCS#1 v1.5 or OAEP) and when plaintext is not smaller than the modulus in raw exponent form.
