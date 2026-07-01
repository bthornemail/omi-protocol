# OMI Protocol

## A relation carries its own address.

The Omi-Ring is a ring. 5040 slots. Fixed size. Circular.

Every slot has exactly one relation. Every relation has exactly one slot —
the slot determined by the relation's own structural properties. Not
assigned by an external authority. Not computed from a hash. The relation's
own fields decide where it goes.

This is not a database (no key/value split).
This is not a blockchain (no hash chain).
This is not a hash table (no hash function).
This is an Omi-Ring.

---

A slot IS its relation. There is no pointer from slot to storage. There is
no lookup table. The slot and the data are the same thing.

How position can BE identity: a circular slide rule has no lookup — the
number 3 is at the 3 position because that is where the number 3 belongs.
The position and the value are one. The Omi-Ring generalizes this: a
relation's structural properties determine its position, and that position
IS the relation's identity.

---

## Pipeline

```
notation → cite → validate → record → project
```

1. **Cite** — parse notation into a structured frame
2. **Validate** — check the relation against a deterministic rule
3. **Record** — store the result (accepted or rejected) in the ring
4. **Project** — render the accepted state to a surface

Nothing skips steps. Citation is not acceptance. Projection is not
acceptance. Only validation followed by ring storage equals acceptance.

---

## Address Frame

```
S0-S1-S2-S3/S4/S5/S6/S7?PAYLOAD?MASK@CAR@CDR
```

Eight 16-bit address fields, payload, mask, and CAR/CDR closure.
The frame IS the citation. The citation IS the address.
The address IS the identity.

```
(a . b)
```

is readable notation — a citation with CAR = a and CDR = b.

---

## 5040 Slots

```
fano7 × 720 + role3 × 240 + local240
```

| Component | Range | Determined by |
|-----------|-------|---------------|
| fano7 | 0–6 | structural incidence (Fano line) |
| role3 | 0–2 | diagonal phase (chirality) |
| local240 | 0–239 | quadratic form on the frame |

7 × 3 × 240 = 5040. 240 = 2 × 120. 120 = 5!. Exact — no rounding.

---

## Fano Plane

Seven points, seven lines, three points per line, three lines per point.
Exactly one line through any two points.

This finite geometry provides exactly seven equivalence classes for
routing. Every citation belongs to exactly one Fano line, one chirality
role, and one local position. The relation's own structure decides all
three — no hash function.

---

## Chirality Diagonals

```
D+ = {0, 5, A, F}    XOR = 0, sum = 30
D- = {3, 6, 9, C}    XOR = 0, sum = 30
```

Two sets of four nibbles each, closed under XOR. The alternation between
D+ and D- phases generates the same sign pattern as the Leibniz series
for π:

```
π/4 = 1 − 1/3 + 1/5 − 1/7 + 1/9 − …
```

Chirality determines a citation's orientation in the ring. The same
alternation that routes relations also generates π from geometry.

---

## Four Authorities

```
                    Metatron
             (Projection Authority)
                      ▲
                      │
OMI ◄────────── Omi-Ring ──────────► IMO
(Citation)                          (Carrier)
                      │
                      ▼
              Tetragrammatron
            (Validation Authority)
```

| Authority | Role | Never |
|-----------|------|-------|
| OMI | citation — parses and structures relations | does not validate or project |
| Tetragrammatron | validation — accepts or rejects | does not cite or project |
| Metatron | projection — renders accepted state to surfaces | does not validate or carry |
| IMO | carrier — transports between boundaries | does not validate or project |

No authority combines roles. No authority can accept state alone.

Two axes:
- **Horizontal**: representation — OMI preserves identity, IMO preserves representation
- **Vertical**: interpretation — Tetragrammatron preserves truth, Metatron preserves interpretation

The Omi-Ring sits at the center. Every authority reads from or writes to it.
Nothing else is shared between authorities.

---

## No Hash

Hashing is external. It may be used for transport secrecy, external caching,
or legacy compatibility. It is never used for identity, slot selection,
validation input, or carry-forward proof.

A relation does not need a hash to know where it belongs. Its own structure
tells it.

---

## What Makes This Different

- **Identity is structure, not hash** — the relation's own fields are its identity
- **Address is carried, not assigned** — the relation determines its own slot
- **No indirection** — the slot IS the data, not a pointer to data
- **No stored constants** — φ and π are derived from incidence geometry when needed
- **No central authority** — each peer runs its own ring; agreement emerges from shared structure, not global consensus
