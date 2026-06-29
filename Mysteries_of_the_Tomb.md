# Mysteries of the Tomb — CTF Write-Up

**Points:** 100  
**Flag:** `pecan{TH3C1F3ROF4NCH}`

---

We're given two files: `flag.txt` which contains a pyramid of Egyptian hieroglyphs, and `notes.txt` which maps each glyph to a letter or number.

The tablet inscription hints at the reading order:

> "read the stones at dawn's first light, tracing from the summit down to the shifting sands."

So we just read the pyramid top to bottom, left to right.

```
        𓏏          →  T
       𓉔 𓏼        →  H 3
      ☥ 𓏺 𓆑       →  C 1 F
     𓏼 𓂋 𓅱 𓆑    →  3 R O F
    𓏽 𓈖 ☥ 𓉔      →  4 N C H
```

That gives us `TH3C1F3ROF4NCH`.

One thing worth noting — the ankh `☥` maps to `C`, not `A`. The notes even warn about this: *"commonly misread."* Easy trap to fall into.

Reading it as leet speak, `F` doubles as `PH` phonetically, so `C1F3R = CIPHER`. The full message is **"THE CIPHER OF ANCH"** — the ankh being the cipher key itself, which is a nice touch.

Flag: `pecan{TH3C1F3ROF4NCH}`
