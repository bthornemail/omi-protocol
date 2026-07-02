# SKILLS.md

## Accountable General Interpreter — Algorithm Registry

Version: `0.1.0`  
Status: Draft  
Authority: Algorithm Reproducibility Boundary  
Depends on: `REPO.md`, `AGENTS.md`, receipt validation

---

## §0. AGI Contract — Computation

`SKILLS.md` defines how computation is performed within the
four-file Accountable General Interpreter specification.

```text
REPO.md     → scope and role     (WHERE may I act?)
AGENTS.md   → behavior           (HOW must I act?)
SKILLS.md   → computation        (HOW do I compute?)
ADAPTERS.md → external boundary   (WHERE do I touch the world?)
```

A skill is a reproducible, verifiable, testable algorithm.
An Accountable General Interpreter does not "know" facts —
it invokes skills. The result of a skill is a candidate
until validated and recorded as a receipt.

```text
A skill computes.
A skill does not authorize itself.
A skill does not accept its own output.
```

Every skill in this file:
- declares an effect class (pure, read-only, local-write, etc.)
- declares an input and output contract
- provides test vectors
- is independently reproducible

Any computation that cannot be declared as a skill with
test vectors is outside the AGI contract.

---

## §0.1. Original Purpose (preserved below)

This file was originally written as the OMI Version-Control
Algorithm Registry. That framing remains valid as one
application of the AGI contract above.

---

## 0. Original Purpose

```text
AGENTS.md authorizes which skills a resolver may invoke.
REPO.md controls repository role and scope permission.
SKILLS.md defines how computation is performed.
Receipts determine which results are accepted.
```

Each deterministic skill SHOULD define:

```text
name
scope address
version
input contract
output contract
effect class
purity
width constraints
endianness
overflow behavior
canonical algorithm
test vectors
validation rule
```

---

## 1. Normative Language

The key words:

```text
MUST
MUST NOT
REQUIRED
SHALL
SHALL NOT
SHOULD
SHOULD NOT
RECOMMENDED
MAY
OPTIONAL
```

are to be interpreted as normative requirement levels.

---

## 2. Skill Declaration Contract

Every skill declaration SHOULD use this structure:

```omi-lisp
(skill.<name>.name . "Human readable skill name")
(skill.<name>.scope . fs.o/SKILLS.md/gs.o/<group>/rs.o/<record>/us.o/<unit>)
(skill.<name>.version . v1)
(skill.<name>.input . input-contract)
(skill.<name>.output . output-contract)
(skill.<name>.effect . pure)
(skill.<name>.deterministic . true)
(skill.<name>.validation . test-vector-set)
```

A skill that mutates repository state MUST NOT be declared as `pure`.

A skill with side effects MUST declare its effect class explicitly.

---

## 3. Effect Classes

Skills MUST declare one effect class:

```text
pure
read-only
local-write
repo-write
network
hardware
security-sensitive
```

Unless explicitly stated otherwise, all skills in this file are `pure`.

Pure skills MUST NOT:

```text
write files
send network messages
modify repository state
access credentials
actuate hardware
depend on wall-clock time
depend on randomness
```

---

# 4. Core Math Skills

## 4.1 `skill.delta16`

```omi-lisp
(skill.delta16.name . "16-bit rotation-XOR law")
(skill.delta16.scope . fs.o/SKILLS.md/gs.o/math/rs.o/delta16/us.o/v1)
(skill.delta16.version . v1)
(skill.delta16.input . "(x:uint16 c:uint16)")
(skill.delta16.output . "uint16")
(skill.delta16.effect . pure)
(skill.delta16.width . 16)
(skill.delta16.overflow . "mask-to-16")
(skill.delta16.formula . "rotl16(x,1) xor rotl16(x,3) xor rotr16(x,2) xor c")
```

Canonical C:

```c
#include <stdint.h>

static uint16_t omi_rotl16(uint16_t x, unsigned n) {
    n &= 15u;
    return (uint16_t)((uint16_t)(x << n) | (uint16_t)(x >> ((16u - n) & 15u)));
}

static uint16_t omi_rotr16(uint16_t x, unsigned n) {
    n &= 15u;
    return (uint16_t)((uint16_t)(x >> n) | (uint16_t)(x << ((16u - n) & 15u)));
}

static uint16_t omi_delta16(uint16_t x, uint16_t c) {
    return (uint16_t)(
        omi_rotl16(x, 1u) ^
        omi_rotl16(x, 3u) ^
        omi_rotr16(x, 2u) ^
        c
    );
}
```

Test vectors:

```omi-lisp
(test.delta16.zero.input . "(0x0000 0x0000)")
(test.delta16.zero.expect . 0x0000)

(test.delta16.constant.input . "(0x0000 0x0001)")
(test.delta16.constant.expect . 0x0001)

(test.delta16.width.input . "(0xffff 0x0000)")
(test.delta16.width.expect . 0xffff)
```

---

## 4.2 `skill.bqf32`

```omi-lisp
(skill.bqf32.name . "Binary quadratic form")
(skill.bqf32.scope . fs.o/SKILLS.md/gs.o/math/rs.o/bqf32/us.o/v1)
(skill.bqf32.version . v1)
(skill.bqf32.input . "(x:uint32 y:uint32)")
(skill.bqf32.output . "uint32")
(skill.bqf32.effect . pure)
(skill.bqf32.formula . "60*x*x + 16*x*y + 4*y*y")
```

Canonical C:

```c
#include <stdint.h>

static uint32_t omi_bqf32(uint32_t x, uint32_t y) {
    return (uint32_t)(
        (uint32_t)(60u * x * x) +
        (uint32_t)(16u * x * y) +
        (uint32_t)(4u  * y * y)
    );
}
```

Test vectors:

```omi-lisp
(test.bqf32.zero.input . "(0 0)")
(test.bqf32.zero.expect . 0)

(test.bqf32.one-zero.input . "(1 0)")
(test.bqf32.one-zero.expect . 60)

(test.bqf32.zero-one.input . "(0 1)")
(test.bqf32.zero-one.expect . 4)

(test.bqf32.one-one.input . "(1 1)")
(test.bqf32.one-one.expect . 80)
```

---

## 4.3 `skill.slot5040`

```omi-lisp
(skill.slot5040.name . "Fano-role-local ring slot")
(skill.slot5040.scope . fs.o/SKILLS.md/gs.o/ring/rs.o/slot5040/us.o/v1)
(skill.slot5040.version . v1)
(skill.slot5040.input . "(fano7:uint32 role3:uint32 local240:uint32)")
(skill.slot5040.output . "slot:uint32 in 0..5039")
(skill.slot5040.effect . pure)
(skill.slot5040.formula . "(fano7 mod 7)*720 + (role3 mod 3)*240 + (local240 mod 240)")
```

Canonical C:

```c
#include <stdint.h>

static uint32_t omi_slot5040(uint32_t fano7,
                             uint32_t role3,
                             uint32_t local240) {
    return (uint32_t)(
        (fano7 % 7u) * 720u +
        (role3 % 3u) * 240u +
        (local240 % 240u)
    );
}
```

Test vectors:

```omi-lisp
(test.slot5040.zero.input . "(0 0 0)")
(test.slot5040.zero.expect . 0)

(test.slot5040.example.input . "(3 2 180)")
(test.slot5040.example.expect . 2820)

(test.slot5040.max.input . "(6 2 239)")
(test.slot5040.max.expect . 5039)
```

---

# 5. Scope Skills

## 5.1 `skill.scope.resolve`

```omi-lisp
(skill.scope.resolve.name . "Resolve FS/GS/RS/US scope")
(skill.scope.resolve.scope . fs.o/SKILLS.md/gs.o/scope/rs.o/resolve/us.o/v1)
(skill.scope.resolve.version . v1)
(skill.scope.resolve.input . "scope-string")
(skill.scope.resolve.output . "(fs gs rs us)")
(skill.scope.resolve.effect . pure)
```

Scope format:

```text
fs.o/<file>/gs.o/<group>/rs.o/<record>/us.o/<unit>
```

Resolution rule:

```text
The resolver MUST read path markers in order.
Missing scopes MUST be returned as empty.
Unknown markers MUST cause rejection.
```

Reference pseudocode:

```c
/*
Input:
  fs.o/SKILLS.md/gs.o/encoding/rs.o/base64/us.o/decode

Output:
  fs = SKILLS.md
  gs = encoding
  rs = base64
  us = decode
*/
```

Test vectors:

```omi-lisp
(test.scope.resolve.full.input . "fs.o/SKILLS.md/gs.o/encoding/rs.o/base64/us.o/decode")
(test.scope.resolve.full.expect . "(SKILLS.md encoding base64 decode)")

(test.scope.resolve.partial.input . "fs.o/SKILLS.md/gs.o/encoding")
(test.scope.resolve.partial.expect . "(SKILLS.md encoding  )")
```

---

# 6. Seed and Route Skills

## 6.1 `skill.seed.base64`

```omi-lisp
(skill.seed.base64.name . "Base64 neutral seed object")
(skill.seed.base64.scope . fs.o/SKILLS.md/gs.o/encoding/rs.o/base64/us.o/seed)
(skill.seed.base64.version . v1)
(skill.seed.base64.input . none)
(skill.seed.base64.output . o.base64.o)
(skill.seed.base64.effect . pure)

(seed.base64 . o.base64.o)
(seed.base64.origin . o.o)
(seed.base64.route . omi-base-64-imo)
(seed.base64.kind . carrier-encoding)
(seed.base64.zero . polynomial.zero)
(seed.base64.alphabet . "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/")
(seed.base64.padding . "=")
```

Validation rule:

```text
The Base64 seed declares interpretation scope only.
It MUST NOT imply payload acceptance.
Payload instances MUST validate separately.
```

---

# 7. Version Skills

## 7.1 `skill.version.create-candidate`

```omi-lisp
(skill.version.create-candidate.name . "Create version candidate")
(skill.version.create-candidate.scope . fs.o/SKILLS.md/gs.o/version/rs.o/create/us.o/candidate)
(skill.version.create-candidate.version . v1)
(skill.version.create-candidate.input . version-declaration)
(skill.version.create-candidate.output . version-candidate)
(skill.version.create-candidate.effect . pure)
```

Required candidate fields:

```text
parent
scope
carrier
effect
validation
```

Recommended candidate fields:

```text
seed
route
algorithm
topology
tests
```

Validation rule:

```text
A version candidate is valid only if required fields are present.
A valid candidate is not accepted until receipt.
```

Example:

```omi-lisp
(version.status . candidate)
(version.parent . receipt-041)
(version.scope . fs.o/SKILLS.md/gs.o/encoding/rs.o/base64)
(version.seed . o.base64.o)
(version.route . omi-base-64-imo)
(version.carrier . SKILLS.md)
(version.algorithm . skill.seed.base64)
(version.effect . pure)
(version.validation . test.base64.seed)
```

---

## 7.2 `skill.version.diff`

```omi-lisp
(skill.version.diff.name . "Compare version declarations")
(skill.version.diff.scope . fs.o/SKILLS.md/gs.o/version/rs.o/diff/us.o/v1)
(skill.version.diff.version . v1)
(skill.version.diff.input . "(left-version right-version)")
(skill.version.diff.output . diff-report)
(skill.version.diff.effect . pure)
```

A diff report SHOULD include:

```text
carrier delta
declaration delta
scope delta
seed delta
route delta
skill delta
algorithm delta
topology delta
receipt delta
projection delta
```

A diff MUST NOT imply acceptance.

---

## 7.3 `skill.version.merge-candidate`

```omi-lisp
(skill.version.merge-candidate.name . "Create merge candidate")
(skill.version.merge-candidate.scope . fs.o/SKILLS.md/gs.o/version/rs.o/merge/us.o/candidate)
(skill.version.merge-candidate.version . v1)
(skill.version.merge-candidate.input . "(left right scope)")
(skill.version.merge-candidate.output . merge-candidate)
(skill.version.merge-candidate.effect . pure)
```

A merge candidate MUST include:

```text
left parent
right parent
merge scope
conflict classification
topology check
validation rule
```

A merge candidate MUST NOT be considered accepted until receipt.

---

## 7.4 `skill.version.rollback-candidate`

```omi-lisp
(skill.version.rollback-candidate.name . "Create rollback candidate")
(skill.version.rollback-candidate.scope . fs.o/SKILLS.md/gs.o/version/rs.o/rollback/us.o/candidate)
(skill.version.rollback-candidate.version . v1)
(skill.version.rollback-candidate.input . "(target-receipt reason scope)")
(skill.version.rollback-candidate.output . rollback-candidate)
(skill.version.rollback-candidate.effect . pure)
```

Rollback rule:

```text
Rollback MUST create a new candidate relation.
Rollback MUST NOT erase historical receipts.
```

---

# 8. Topology Skills

## 8.1 `skill.topology.betti`

```omi-lisp
(skill.topology.betti.name . "Compute dependency Betti signature")
(skill.topology.betti.scope . fs.o/SKILLS.md/gs.o/topology/rs.o/betti/us.o/v1)
(skill.topology.betti.version . v1)
(skill.topology.betti.input . dependency-graph)
(skill.topology.betti.output . "(b0 b1 b2)")
(skill.topology.betti.effect . pure)
```

Interpretation:

```text
b0 = connected components
b1 = independent cycles
b2 = unresolved enclosed dependency voids
```

Minimum deterministic rule:

```text
b0 MUST be the number of connected components.
b1 SHOULD be computed as E - V + b0 for an undirected dependency graph.
b2 MAY be zero unless the repository defines enclosed dependency void detection.
```

Test vectors:

```omi-lisp
(test.topology.betti.tree.input . "V=3 E=2 components=1")
(test.topology.betti.tree.expect . "(1 0 0)")

(test.topology.betti.loop.input . "V=3 E=3 components=1")
(test.topology.betti.loop.expect . "(1 1 0)")

(test.topology.betti.disconnected.input . "V=4 E=2 components=2")
(test.topology.betti.disconnected.expect . "(2 0 0)")
```

---

## 8.2 `skill.topology.schlafli`

```omi-lisp
(skill.topology.schlafli.name . "Project topology to Schläfli label")
(skill.topology.schlafli.scope . fs.o/SKILLS.md/gs.o/topology/rs.o/schlafli/us.o/v1)
(skill.topology.schlafli.version . v1)
(skill.topology.schlafli.input . "(b0 b1 b2)")
(skill.topology.schlafli.output . schlafli-label)
(skill.topology.schlafli.effect . pure)
```

Projection rule:

```text
(1 0 0) SHOULD project as {3,3} minimal tetrahedral seed.
(1 1 0) SHOULD project as {4,3} cubical loop lattice.
(1 0 1) SHOULD project as {5,3} enclosed semantic shell.
Other signatures MAY project as {3,5} complex reciprocal shell.
```

Test vectors:

```omi-lisp
(test.topology.schlafli.seed.input . "(1 0 0)")
(test.topology.schlafli.seed.expect . "{3,3}")

(test.topology.schlafli.loop.input . "(1 1 0)")
(test.topology.schlafli.loop.expect . "{4,3}")

(test.topology.schlafli.void.input . "(1 0 1)")
(test.topology.schlafli.void.expect . "{5,3}")
```

---

# 9. Receipt Skills

## 9.1 `skill.receipt.verify`

```omi-lisp
(skill.receipt.verify.name . "Verify receipt")
(skill.receipt.verify.scope . fs.o/SKILLS.md/gs.o/receipt/rs.o/verify/us.o/v1)
(skill.receipt.verify.version . v1)
(skill.receipt.verify.input . receipt-handle)
(skill.receipt.verify.output . "(valid hash scope result)")
(skill.receipt.verify.effect . pure)
```

Verification MUST check:

```text
receipt exists
receipt hash is nonzero
receipt scope matches claim
receipt result is accepted or rejected
receipt parent is consistent when parent is declared
```

A valid receipt MAY be accepted or rejected.

The verifier MUST report the result type.

---

## 9.2 `skill.receipt.parent`

```omi-lisp
(skill.receipt.parent.name . "Read receipt parent")
(skill.receipt.parent.scope . fs.o/SKILLS.md/gs.o/receipt/rs.o/parent/us.o/v1)
(skill.receipt.parent.version . v1)
(skill.receipt.parent.input . receipt-handle)
(skill.receipt.parent.output . parent-receipt-handle)
(skill.receipt.parent.effect . pure)
```

A missing parent MUST be represented explicitly as `none`, not as an ambiguous zero unless zero is declared as root.

---

# 10. `.imo` Carrier Skills

## 10.1 `skill.imo.normalize`

```omi-lisp
(skill.imo.normalize.name . "Normalize OMI-Lisp blocks to .imo carrier")
(skill.imo.normalize.scope . fs.o/SKILLS.md/gs.o/imo/rs.o/normalize/us.o/v1)
(skill.imo.normalize.version . v1)
(skill.imo.normalize.input . markdown-text)
(skill.imo.normalize.output . normalized-imo-text)
(skill.imo.normalize.effect . pure)
```

Normalization rule:

```text
The normalizer MUST extract fenced omi-lisp blocks.
The normalizer MUST preserve declaration order unless a canonical sort is declared.
The normalizer MUST NOT evaluate declarations.
The normalizer MUST NOT accept declarations.
The normalizer MUST output carrier text only.
```

Test vector:

```omi-lisp
(test.imo.normalize.simple.input . "```omi-lisp\n(version.seed . o.base64.o)\n```")
(test.imo.normalize.simple.expect . "(version.seed . o.base64.o)")
```

---

# 11. Validation Skills

## 11.1 `skill.version.validate-candidate`

```omi-lisp
(skill.version.validate-candidate.name . "Validate version candidate")
(skill.version.validate-candidate.scope . fs.o/SKILLS.md/gs.o/version/rs.o/validate/us.o/candidate)
(skill.version.validate-candidate.version . v1)
(skill.version.validate-candidate.input . version-candidate)
(skill.version.validate-candidate.output . validation-result)
(skill.version.validate-candidate.effect . pure)
```

Validation MUST check:

```text
scope exists
carrier is declared
effect class is declared
required skill exists
required validation exists
seed exists when seed is required
route exists when route is required
parent exists when parent is required
```

Validation result MUST be one of:

```text
valid
invalid
blocked
incomplete
```

Validation result MUST NOT be called accepted unless receipt is recorded.

---

# 12. Skill Registry Declaration

```omi-lisp
(skill.registry.name . "OMI Version Control Skills")
(skill.registry.version . "0.1.0")
(skill.registry.scope . fs.o/SKILLS.md)

(skill.delta16 . fs.o/SKILLS.md/gs.o/math/rs.o/delta16/us.o/v1)
(skill.bqf32 . fs.o/SKILLS.md/gs.o/math/rs.o/bqf32/us.o/v1)
(skill.slot5040 . fs.o/SKILLS.md/gs.o/ring/rs.o/slot5040/us.o/v1)

(skill.scope.resolve . fs.o/SKILLS.md/gs.o/scope/rs.o/resolve/us.o/v1)

(skill.seed.base64 . fs.o/SKILLS.md/gs.o/encoding/rs.o/base64/us.o/seed)

(skill.version.create-candidate . fs.o/SKILLS.md/gs.o/version/rs.o/create/us.o/candidate)
(skill.version.diff . fs.o/SKILLS.md/gs.o/version/rs.o/diff/us.o/v1)
(skill.version.merge-candidate . fs.o/SKILLS.md/gs.o/version/rs.o/merge/us.o/candidate)
(skill.version.rollback-candidate . fs.o/SKILLS.md/gs.o/version/rs.o/rollback/us.o/candidate)
(skill.version.validate-candidate . fs.o/SKILLS.md/gs.o/version/rs.o/validate/us.o/candidate)

(skill.topology.betti . fs.o/SKILLS.md/gs.o/topology/rs.o/betti/us.o/v1)
(skill.topology.schlafli . fs.o/SKILLS.md/gs.o/topology/rs.o/schlafli/us.o/v1)

(skill.receipt.verify . fs.o/SKILLS.md/gs.o/receipt/rs.o/verify/us.o/v1)
(skill.receipt.parent . fs.o/SKILLS.md/gs.o/receipt/rs.o/parent/us.o/v1)

(skill.imo.normalize . fs.o/SKILLS.md/gs.o/imo/rs.o/normalize/us.o/v1)
```

---

# 13. Canon Statement

This `SKILLS.md` defines reproducible algorithms for OMI version control.

Each skill defines scope, version, input contract, output contract, effect class, deterministic rule, and validation expectation.

Skills compute candidates.

Skills do not authorize themselves.

Skills do not accept results.

A skill result becomes accepted only through validation and receipt.

---

# 14. One-Sentence Version

`SKILLS.md` is the algorithm registry for OMI version control, defining reproducible deterministic skills for math, scope resolution, seed interpretation, version candidates, diffs, merges, rollback candidates, topology signatures, receipt verification, and `.imo` normalization, while leaving behavior authorization to `AGENTS.md` and acceptance to receipts.
