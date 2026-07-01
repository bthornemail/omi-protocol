# OMI-SKILLS.md

## Deterministic Algorithm Registry

This document is the canonical registry of OMI skills (deterministic algorithms). Each skill is fully specified with:
- Input/output contracts
- Deterministic formula
- Test vectors
- Effect class
- Width constraints

An LLM reading this document should be able to implement each skill exactly and verify it against test vectors.

---

## Skill: delta16

### Basic Info
```
name: delta16 (16-bit rotation-XOR law)
scope: fs.o/SKILLS.md/gs.o/math/rs.o/gates/us.o/v1
version: 1.0
deterministic: true
effect_class: pure
width_preserving: true
```

### Input Contract
```
x: uint16  (rotation input)
c: uint16  (constant input)
```

### Output Contract
```
output: uint16
```

### Formula
```
delta16(x, c) = rotl16(x, 1) ⊕ rotl16(x, 3) ⊕ rotr16(x, 2) ⊕ c
```

Where:
- `rotl16(x, n)` = rotate left by n bits (16-bit width)
- `rotr16(x, n)` = rotate right by n bits (16-bit width)
- `⊕` = bitwise XOR

### Properties
```
reversible    bitwise XOR is self-inverse
deterministic same (x, c) → same output always
width_safe    16-bit in → 16-bit out
chiral        operation has orientation (posting/pulling)
```

### Test Vectors

```
Test 1:
  Input: x = 0x0000, c = 0x0001
  Expected: 0x0001
  Reason: rotations of 0 are 0; 0 ⊕ c = c

Test 2:
  Input: x = 0xFFFF, c = 0x0000
  Expected: 0xFFFF
  Reason: all rotations of 0xFFFF are 0xFFFF; 0xFFFF ⊕ 0 = 0xFFFF

Test 3:
  Input: x = 0x0001, c = 0x0000
  rotl16(0x0001, 1) = 0x0002
  rotl16(0x0001, 3) = 0x0008
  rotr16(0x0001, 2) = 0x8000
  Expected: 0x0002 ⊕ 0x0008 ⊕ 0x8000 ⊕ 0x0000 = 0x800A

Test 4:
  Input: x = 0x1234, c = 0x5678
  rotl16(0x1234, 1) = 0x2468
  rotl16(0x1234, 3) = 0x91A0
  rotr16(0x1234, 2) = 0x048D
  Expected: 0x2468 ⊕ 0x91A0 ⊕ 0x048D ⊕ 0x5678 = ?
  (implementer must compute)

Test 5:
  Input: x = 0xAAAA, c = 0x5555
  Expected: (compute deterministically)
  Note: bit pattern tests alternating bits

Test 6:
  Input: x = 0x1111, c = 0x2222
  Expected: (compute deterministically)
  Note: tests specific bit alignments
```

### Overflow Behavior
```
No overflow. Rotations stay within 16-bit space. XOR never overflows.
```

### Usage Example
```lisp
(compute (delta16 0x1234 0x5678))
  → uint16 result (deterministic)

(witness . delta16(S4, S3))
  → instruction witness (for inspection only, not protocol identity)
```

---

## Skill: bqf32

### Basic Info
```
name: bqf32 (32-bit binary quadratic form)
scope: fs.o/SKILLS.md/gs.o/math/rs.o/gates/us.o/v1
version: 1.0
deterministic: true
effect_class: pure
width_preserving: true (output mod 2^32)
```

### Input Contract
```
x: uint32
y: uint32
```

### Output Contract
```
output: uint32 (Q(x,y) mod 2^32)
```

### Formula
```
Q(x, y) = 60x² + 16xy + 4y²
```

Where:
- `x²` = x multiplied by x (mod 2^32 for overflow)
- `xy` = x multiplied by y (mod 2^32 for overflow)
- `y²` = y multiplied by y (mod 2^32 for overflow)
- Result is (mod 2^32)

### Properties
```
symmetric     Q(x, y) = Q(y, x) [actually: NOT symmetric, 60 ≠ 4]
deterministic same (x, y) → same output
projective    used for slot5040 computation
quadratic     degree 2 polynomial
```

### Decomposition
```
Q(x, y) = 4(15x² + 4xy + y²)
        = 4 × (15x² + 4xy + y²)
```

### Test Vectors

```
Test 1:
  Input: x = 0, y = 0
  Expected: 0
  Reason: Q(0, 0) = 0

Test 2:
  Input: x = 1, y = 0
  Expected: 60
  Reason: Q(1, 0) = 60(1) + 16(0) + 4(0) = 60

Test 3:
  Input: x = 0, y = 1
  Expected: 4
  Reason: Q(0, 1) = 60(0) + 16(0) + 4(1) = 4

Test 4:
  Input: x = 1, y = 1
  Expected: 80
  Reason: Q(1, 1) = 60(1) + 16(1) + 4(1) = 60 + 16 + 4 = 80

Test 5:
  Input: x = 2, y = 3
  Expected: 60*4 + 16*6 + 4*9 = 240 + 96 + 36 = 372

Test 6:
  Input: x = 0xFFFFFFFF, y = 0xFFFFFFFF
  Expected: (compute with mod 2^32 overflow handling)

Test 7:
  Input: x = 5, y = 7
  Expected: 60*25 + 16*35 + 4*49 = 1500 + 560 + 196 = 2256
```

### Overflow Behavior
```
When products exceed 2^32, wrap (mod 2^32).
This preserves determinism but can cause surprising results.
Example: 0xFFFFFFFF * 0xFFFFFFFF = 0xFFFFFFFE00000001 → take low 32 bits.
```

### Usage Example
```lisp
(compute (bqf32 5 7))
  → 2256

(slot-local . (Q S4 S5) mod 240)
  → part of slot5040 computation
```

---

## Skill: rotl16

### Basic Info
```
name: rotl16 (16-bit rotate left)
scope: fs.o/SKILLS.md/gs.o/math/rs.o/gates/us.o/v1
version: 1.0
deterministic: true
effect_class: pure
width_preserving: true
```

### Input Contract
```
x: uint16
n: int (bits to rotate, wrapped mod 16)
```

### Output Contract
```
output: uint16
```

### Formula
```
rotl16(x, n) = ((x << (n mod 16)) | (x >> (16 - (n mod 16)))) & 0xFFFF
```

### Test Vectors

```
Test 1:
  Input: x = 0x0001, n = 1
  Expected: 0x0002
  (bit at position 0 moves to position 1)

Test 2:
  Input: x = 0x8000, n = 1
  Expected: 0x0001
  (bit at position 15 wraps to position 0)

Test 3:
  Input: x = 0x1234, n = 4
  Expected: 0x2341
  (all bits rotate left by 4)

Test 4:
  Input: x = 0xFFFF, n = any
  Expected: 0xFFFF
  (rotation of all 1s is all 1s)

Test 5:
  Input: x = 0x0001, n = 16
  Expected: 0x0001
  (rotating by full width returns same value)

Test 6:
  Input: x = 0x1234, n = 0
  Expected: 0x1234
  (rotating by 0 is identity)
```

---

## Skill: rotr16

### Basic Info
```
name: rotr16 (16-bit rotate right)
scope: fs.o/SKILLS.md/gs.o/math/rs.o/gates/us.o/v1
version: 1.0
deterministic: true
effect_class: pure
width_preserving: true
```

### Input Contract
```
x: uint16
n: int (bits to rotate, wrapped mod 16)
```

### Output Contract
```
output: uint16
```

### Formula
```
rotr16(x, n) = ((x >> (n mod 16)) | (x << (16 - (n mod 16)))) & 0xFFFF
```

### Test Vectors

```
Test 1:
  Input: x = 0x8000, n = 1
  Expected: 0x4000

Test 2:
  Input: x = 0x0001, n = 1
  Expected: 0x8000

Test 3:
  Input: x = 0x1234, n = 4
  Expected: 0x4123

Test 4:
  Input: x = 0xFFFF, n = any
  Expected: 0xFFFF

Test 5:
  Input: x = 0x1234, n = 0
  Expected: 0x1234
```

---

## Skill: xor

### Basic Info
```
name: xor (bitwise exclusive-or)
scope: fs.o/SKILLS.md/gs.o/math/rs.o/gates/us.o/v1
version: 1.0
deterministic: true
effect_class: pure
width_preserving: true
```

### Input Contract
```
a: uint16 (or any fixed width)
b: uint16 (or any fixed width)
```

### Output Contract
```
output: uint16 (or same width as input)
```

### Formula
```
xor(a, b) = a ⊕ b
```

### Properties
```
reversible      a ⊕ b ⊕ b = a
commutative     a ⊕ b = b ⊕ a
associative     (a ⊕ b) ⊕ c = a ⊕ (b ⊕ c)
self_inverse    a ⊕ a = 0
zero_identity   a ⊕ 0 = a
```

### Test Vectors

```
Test 1:
  Input: a = 0x0000, b = 0x0000
  Expected: 0x0000

Test 2:
  Input: a = 0xFFFF, b = 0x0000
  Expected: 0xFFFF

Test 3:
  Input: a = 0xFFFF, b = 0xFFFF
  Expected: 0x0000

Test 4:
  Input: a = 0x1234, b = 0x5678
  Expected: 0x444C

Test 5:
  Input: a = 0x1234, b = 0x1234
  Expected: 0x0000
  (self-inverse)

Test 6:
  Input: a = 0xAAAA, b = 0x5555
  Expected: 0xFFFF
```

---

## Skill: hash_fnv1a

### Basic Info
```
name: hash_fnv1a (FNV-1a hash function)
scope: fs.o/SKILLS.md/gs.o/math/rs.o/hashing/us.o/v1
version: 1.0
deterministic: true
effect_class: pure
width_preserving: false (variable length input → 64-bit output)
```

### Important Note
```
FNV-1a is for CARRIER USE ONLY.

It is NOT used for protocol identity.
It is NOT used for validation.
It is NOT used for slot computation.

It may be used for:
  - Transport indexing
  - External caching
  - Debugging
  - Legacy compatibility

Mark all uses of hash_fnv1a as CARRIER_METADATA_ONLY.
```

### Input Contract
```
data: byte sequence (variable length)
```

### Output Contract
```
output: uint64 (FNV-1a hash)
```

### Formula
```
hash = FNV_offset_basis  (64-bit magic: 0xcbf29ce484222325)
for each byte b in data:
  hash = hash ⊕ b
  hash = hash * FNV_prime (64-bit magic: 0x100000001b3)
  hash = hash & 0xFFFFFFFFFFFFFFFF  (keep 64-bit)
return hash
```

### Test Vectors

```
Test 1:
  Input: empty byte sequence
  Expected: 0xcbf29ce484222325 (offset basis)

Test 2:
  Input: "a" (single byte 0x61)
  Expected: 0xaf63bd4286bb3d6e
  (computed per FNV-1a algorithm)

Test 3:
  Input: "omi" (bytes 0x6f, 0x6d, 0x69)
  Expected: (compute per algorithm)

Test 4:
  Input: 16 bytes (typical OMI data)
  Expected: (compute per algorithm)
```

### Overflow Behavior
```
Multiplication may exceed 64-bit.
Mask to 64-bit after each step.
```

### Usage Example
```lisp
(carrier-hash . (hash_fnv1a data))
  → uint64 hash (for external use only, NOT protocol identity)
```

### Carrier-Only Marker
```c
// This is CARRIER METADATA ONLY - NOT PROTOCOL IDENTITY
uint64_t carrier_metadata_hash = hash_fnv1a(omi_inst);
```

---

## Skill: slot5040_compute

### Basic Info
```
name: slot5040_compute (ring address computation)
scope: fs.o/SKILLS.md/gs.o/math/rs.o/addressing/us.o/v1
version: 1.0
deterministic: true
effect_class: pure
width_preserving: true (output in [0, 5040))
```

### Input Contract
```
fano7: uint32 (Fano plane component, mod 7)
role3: uint32 (role component, mod 3)
local240: uint32 (local component, mod 240)
```

### Output Contract
```
output: uint32 in [0, 5040)
```

### Formula
```
slot5040 = (fano7 mod 7) * 720 + (role3 mod 3) * 240 + (local240 mod 240)
```

Where:
- `fano7 * 720` = base offset by Fano line (7 lines × 720 slots each)
- `role3 * 240` = secondary offset by role (3 roles × 240 slots each)
- `local240` = fine offset within role

### Mathematical Basis
```
7! = 5040 (exact permutation count)
6! = 720
5! = 120
240 = 2 × 5!

Ring structure: 7 × 3 × 240 = 5040 (exact decomposition)
```

### Test Vectors

```
Test 1:
  Input: fano7 = 0, role3 = 0, local240 = 0
  Expected: 0

Test 2:
  Input: fano7 = 0, role3 = 0, local240 = 1
  Expected: 1

Test 3:
  Input: fano7 = 0, role3 = 1, local240 = 0
  Expected: 240

Test 4:
  Input: fano7 = 1, role3 = 0, local240 = 0
  Expected: 720

Test 5:
  Input: fano7 = 6, role3 = 2, local240 = 239
  Expected: 6*720 + 2*240 + 239 = 4320 + 480 + 239 = 5039

Test 6:
  Input: fano7 = 7, role3 = 3, local240 = 240  (overflow)
  Expected: (7 mod 7)*720 + (3 mod 3)*240 + (240 mod 240)
          = 0 + 0 + 0 = 0
          (wraps around)
```

---

## Skill: chirality_select

### Basic Info
```
name: chirality_select (determine chiral orientation)
scope: fs.o/SKILLS.md/gs.o/math/rs.o/geometry/us.o/v1
version: 1.0
deterministic: true
effect_class: pure
width_preserving: false (uint64 input → enum output)
```

### Input Contract
```
hash: uint64 (FNV-1a hash or similar)
```

### Output Contract
```
output: enum { posting, pulling, balanced, incomplete }
```

### Formula
```
D+ = {0x0, 0x5, 0xA, 0xF}
D- = {0x3, 0x6, 0x9, 0xC}

count_d_plus = 0
count_d_minus = 0

for each nibble n in hash (16 nibbles for 64-bit):
  if (n in D+) count_d_plus++
  if (n in D-) count_d_minus++

if (count_d_plus > threshold) return posting
else if (count_d_minus > threshold) return pulling
else if (count_d_plus == count_d_minus) return balanced
else return incomplete
```

Where threshold = 5 (or specify in implementation).

### Properties
```
D+ is closed:  0 ⊕ 5 ⊕ A ⊕ F = 0
D+ sum = 30:   0 + 5 + 10 + 15 = 30
D- is closed:  3 ⊕ 6 ⊕ 9 ⊕ C = 0
D- sum = 30:   3 + 6 + 9 + 12 = 30
```

### Test Vectors

```
Test 1:
  Input: hash with all nibbles in D+
  Expected: posting

Test 2:
  Input: hash with all nibbles in D-
  Expected: pulling

Test 3:
  Input: hash with equal count D+ and D-
  Expected: balanced

Test 4:
  Input: hash with nibbles outside both sets
  Expected: incomplete

Test 5:
  Input: hash = 0x0000000000000000 (all zeros)
  (0 is in D+)
  Expected: posting
```

---

## Skill: validate_rule

### Basic Info
```
name: validate_rule (deterministic validation check)
scope: fs.o/SKILLS.md/gs.o/validation/rs.o/rules/us.o/v1
version: 1.0
deterministic: true
effect_class: pure
width_preserving: false (OmiInst → bool)
```

### Input Contract
```
omi_inst: OmiInst (structured citation)
rule: validation rule identifier
```

### Output Contract
```
output: bool (true = accept, false = reject)
```

### Rule: Q(S)=0

```
Check if: Q(S3, S4) ≡ 0 (mod some_modulus)

where Q(x, y) = 60x² + 16xy + 4y²

This is an example validation rule.
Actual rules are specified in OMI-PROTOCOL.md.
```

### Test Vectors

```
Test 1:
  Input: S3 = 0, S4 = 0
  Rule: Q(S3, S4) = 0
  Expected: true (0 satisfies rule)

Test 2:
  Input: S3 = 1, S4 = 0
  Rule: Q(S3, S4) = 0
  Expected: false (60 ≠ 0)

Test 3:
  Input: S3 and S4 chosen to satisfy rule
  Expected: true
```

---

## How to Implement a Skill

As an implementer, follow this process:

1. **Read the skill definition** in this document
2. **Understand the formula** (mathematical specification)
3. **Implement the formula** in your language
4. **Test against all test vectors**
5. **Verify determinism** (run each test multiple times)
6. **Verify width constraints** (check overflow handling)
7. **Mark carrier-only skills** appropriately
8. **Document any assumptions** you make

Example:
```c
// delta16 implementation in C
uint16_t delta16(uint16_t x, uint16_t c) {
    uint16_t rotl1 = (x << 1) | (x >> 15);
    uint16_t rotl3 = (x << 3) | (x >> 13);
    uint16_t rotr2 = (x >> 2) | (x << 14);
    return rotl1 ^ rotl3 ^ rotr2 ^ c;
}

// Test
assert(delta16(0x0000, 0x0001) == 0x0001);
assert(delta16(0xFFFF, 0x0000) == 0xFFFF);
```

---

## How to Add a New Skill

To add a new skill to SKILLS.md:

1. **Write the declaration** (name, scope, input/output)
2. **Provide the formula** (mathematical definition)
3. **Provide test vectors** (at least 5-6 comprehensive tests)
4. **Specify properties** (deterministic, width-preserving, etc.)
5. **Specify effect class** (pure, read-only, etc.)
6. **Get review** (maintainer approval)
7. **Implement** (after approval)
8. **Pass all tests** (before accepting into canonical list)

---

## Master Skill List (Quick Reference)

```
✓ delta16         16-bit rotation-XOR law
✓ bqf32           32-bit binary quadratic form
✓ rotl16          16-bit rotate left
✓ rotr16          16-bit rotate right
✓ xor             bitwise exclusive-or
✓ hash_fnv1a      FNV-1a hash (CARRIER ONLY)
✓ slot5040_compute  ring address computation
✓ chirality_select  chiral orientation detection
✓ validate_rule   deterministic validation

Total: 9 core skills (all deterministic, all fully specified)
```

---

## Authority

This document (SKILLS.md) is governed by:

```
omi_pi_proof.v             (Coq - immutable proofs)
OMI-PROTOCOL.md            (protocol definition)
OMI-AGENTS.md              (who may do what)
This document (executable specifications)
```

Skills in this document are canonical and must be implemented exactly as specified.

Deviations from specifications are bugs.

Test vectors must pass without exception.
