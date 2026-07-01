# OMI-PROTOCOL.md

## The OMI Protocol as Executable Declaration

This document is a machine-readable and LLM-readable specification of the OMI protocol. It defines:
- What the protocol is
- How to parse it
- How to validate it
- How to project it
- What invariants must hold

An LLM reading this document should be able to implement and use the OMI protocol correctly.

---

## Protocol Definition

### Name
OMI (Omicron/Object Memory Interface)

### Version
1.0 (place-value identity)

### Core Purpose
Deterministic notation-citation-validation-receipt-projection cycle for pseudo-persistent, peer-synchronized, decentralized state.

---

## The Notation

### Minimal Form
```
omi---imo
```

This is an atomic relation. It means: the OMI citation side meets the IMO carrier side across a shared ruler.

### Receipt Grammar
```
o---o/---/?---?@---@
```

Breaking down each section:
- `o---o` = open relation (forward citation span)
- `/---/` = carrier/inverse side
- `?---?` = unresolved witness (query/payload)
- `@---@` = CAR/CDR closure

### Expanded Machine Form
```
o-S0-S1-S2-S3/S4/S5/S6/S7?PAYLOAD?MASK@CAR@CDR
```

Where:
- `S0, S1, S2, S3` = forward citation fields (uint16 each)
- `S4, S5, S6, S7` = inverse/return fields (uint16 each)
- `PAYLOAD` = unresolved value
- `MASK` = boundary/interpretation filter
- `CAR` = forward relation side
- `CDR` = continuation relation side

### Dot Notation (Readable Form)
```
(a . b)
```

This is the visible projection of CAR/CDR closure. It means:
- `a` is cited on the CAR side
- `b` is cited on the CDR side
- The pair is declarative until validated

### Example Notations
```lisp
(user.intent . open-door)
(gpio12 . high)
(button0 . pressed)
(lamp . on)
(car . cdr)
```

---

## The Address Cascade

### Place-Value Hierarchy
```
Nibble (4 bits)      → one hex digit
Hextet (16 bits)     → S0-S7 segments (8 segments = 128 bits)
Address Frame        → S0-S1-S2-S3/S4/S5/S6/S7
Place-Value Cascade  → o-S0-S1-S2-S3/S4/S5/S6/S7?P?M@C@D
```

### Scope Addressing (FS/GS/RS/US)
```
fs.o/<file>
  /gs.o/<group>
    /rs.o/<record>
      /us.o/<unit>
```

Fully scoped notation:
```
fs.o/SKILLS.md/gs.o/math/rs.o/delta16/us.o/v1
```

### Scope Identity
The identity of a notation is its scope path, NOT a hash.

```
CORRECT:   scope = fs.o/SKILLS.md/gs.o/math/rs.o/delta16
WRONG:     identity = hash(scope)
```

---

## The Protocol Pipeline

### Complete Cycle
```
1. recognize   (input arrives)
2. cite        (OMI parses and creates citation)
3. validate    (Tetragrammatron tests)
4. record      (receipt written to ring)
5. project     (Metatron renders to surfaces)
6. inspect     (user/application sees result)
```

### Key Invariant: No Authority Before Receipt
```
Recognition ≠ acceptance
Citation ≠ acceptance
Closure ≠ acceptance
Projection ≠ acceptance

ONLY validation + receipt = acceptance
```

---

## Citation Rules

### Rule: Parse to OmiInst
**Input:** Readable notation (dot notation, scoped path, or expanded address)

**Process:**
```
"(user.intent . open-door)" 
  → parse
  → OmiInst {
      S0: hash("user"),
      S1: hash("intent"),
      S2: hash("open-door"),
      S3: 0x0002,           // opcode: intent
      S4: timestamp,
      S5: 0,
      S6: 0,
      S7: 0,
      CAR: hash("user"),
      CDR: hash("intent"),
      PAYLOAD: "open-door",
      MASK: 0
    }
```

**Output:** OmiInst (structured citation object)

**State After:** Not yet accepted (candidate)

### Rule: Compute Instruction Witness
**Input:** OmiInst

**Process:**
```
witness = delta16(S4, S3) ⊕ CAR ⊕ CDR
```

Where `delta16(x, c) = rotl16(x,1) ⊕ rotl16(x,3) ⊕ rotr16(x,2) ⊕ c`

**Output:** 16-bit witness

**Use:** For debugging, inspection, but NOT for protocol identity

---

## Validation Rules

### Rule: Compute Slot5040
**Input:** Citation (OmiInst or hash of witness)

**Process:**
```
hash = FNV-1a(witness)    // for route computation only, not identity
fano7 = (hash >> 28) mod 7
role3 = (hash >> 24) mod 3
local240 = Q(S4, S5) mod 240

where Q(x, y) = 60x² + 16xy + 4y²

slot5040 = fano7 * 720 + role3 * 240 + local240
```

**Output:** Integer in [0, 5040)

**Constraint:** 7 × 3 × 240 = 5040 (exact Fano incidence)

### Rule: Validate Shape
**Input:** OmiInst

**Process:**
```
if (S0 through S7 all in range [0, 65536))
  and (PAYLOAD is well-formed)
  and (MASK fits boundary)
  and (CAR and CDR are coherent)
  then VALID
  else INVALID
```

**Output:** bool

### Rule: Validate Against Deterministic Rule
**Input:** OmiInst, deterministic rule (e.g., Q(S)=0)

**Process:**
```
if (rule_check(OmiInst)) then
  ACCEPT
else
  REJECT
```

**Output:** accept | reject

### Rule: Record Receipt
**Input:** OmiInst, acceptance decision, slot5040

**Process:**
```
if (ACCEPT) then
  ring[slot5040] = {
    citation: OmiInst,
    scope: extract_scope(OmiInst),
    timestamp: current_cycle,
    result: "accepted"
  }
  return Receipt { slot: slot5040, status: "recorded" }
else
  ring[slot5040] = {
    citation: OmiInst,
    scope: extract_scope(OmiInst),
    timestamp: current_cycle,
    result: "rejected"
  }
  return Receipt { slot: slot5040, status: "rejected" }
```

**Output:** Receipt

**State After:** Durable (written to ring)

---

## Projection Rules

### Rule: Resolve Smith Coordinates
**Input:** Receipt from ring

**Process:**
```
slot = receipt.slot
rho = (slot / 5040)        // 0 to 1, impedance magnitude
theta = (slot mod 720) / 720 * 2π  // phase angle

// Compute Smith chart impedance
z_real = (1 - rho*cos(theta)) / (1 + rho - 2*rho*cos(theta))
z_imag = (rho*sin(theta)) / (1 + rho - 2*rho*cos(theta))
```

**Output:** { rho, theta, z_real, z_imag }

### Rule: Resolve Geometry
**Input:** slot5040, SHAPE_DB (geometry database)

**Process:**
```
geometry = SHAPE_DB[slot mod SHAPE_DB.length]
vertex = geometry_vertex(slot / SHAPE_DB.length)
orientation = rotation_quaternion(rho, theta)

projected_vertex = apply_quaternion(vertex, orientation)
```

**Output:** Projected 3D vertex or 2D coordinate

### Rule: Render to Surface
**Input:** Projected coordinate, surface type

**Process:**
```
if surface == JSON then
  output = { "slot": slot, "rho": rho, "theta": theta, "geometry": name }
else if surface == SVG then
  output = <circle cx="..." cy="..." r="..."/>
else if surface == OBJ then
  output = "v " + x + " " + y + " " + z
else if surface == DOM then
  output = { "data-omi-slot": slot, "data-omi-rho": rho, ... }
```

**Output:** Surface representation

**Constraint:** Projection is read-only. It does NOT validate or accept.

---

## Lazy Evaluation Rule

### Rule: No Side Effects Before Receipt
**Input:** Declaration (e.g., `(relay2 . on)`)

**Process:**
```
declare(relay2 . on)           // stage 1: declaration only
  → cite(relay2 . on)          // stage 2: citation, no effect
  → validate(relay2 . on)      // stage 3: validation, no effect
  → record_receipt(slot)       // stage 4: recording, still no effect
  → project_to_hardware(slot)  // stage 5: NOW side effect occurs
  → apply_hardware_action()    // stage 6: physical actuator fires
  → record_result_receipt()    // stage 7: result recorded
```

**Constraint:** Hardware actuation happens ONLY after ring recording.

**Why:** If validation fails, no side effect. If validation passes, effect is recorded before occurrence.

---

## Deterministic Laws

### Law: delta16 Rotation-XOR
```
delta16(x, c) = rotl16(x, 1) ⊕ rotl16(x, 3) ⊕ rotr16(x, 2) ⊕ c

Properties:
- Input: uint16, uint16
- Output: uint16
- Deterministic: same input → same output always
- Reversible: bitwise operations are self-inverse
- Width-preserving: 16-bit in, 16-bit out
```

**Test Vectors:**
```
delta16(0x0000, 0x0001) = 0x0001
delta16(0xFFFF, 0x0000) = 0xFFFF
delta16(0x1234, 0x5678) = <computed deterministically>
```

### Law: BQF32 Binary Quadratic Form
```
Q(x, y) = 60x² + 16xy + 4y²

Properties:
- Input: uint32, uint32
- Output: uint32
- Deterministic
- Symmetric in x-y plane
- Used for slot computation (mod 240)
```

**Test Vectors:**
```
Q(0, 0) = 0
Q(1, 0) = 60
Q(0, 1) = 4
Q(1, 1) = 80
```

### Law: Fano Plane Incidence
```
7 points, 7 lines, 3 points per line, 3 lines per point

Points: {0, 1, 2, 3, 4, 5, 6}

Lines:
  {0, 1, 3}
  {1, 2, 4}
  {2, 3, 5}
  {3, 4, 6}
  {4, 5, 0}
  {5, 6, 1}
  {6, 0, 2}

Property: Exactly 1 line through any 2 points
```

### Law: Factorial Addressing
```
Ring size = 7! = 5040 (not approximate, exact)
Ring address = fano7 * 720 + role3 * 240 + local240
  where fano7 ∈ [0, 7), role3 ∈ [0, 3), local240 ∈ [0, 240)
  
7! = 7 × 6 × 5 × 4 × 3 × 2 × 1 = 5040
6! = 720
5! = 120
240 = 2 × 5!
```

### Law: Chirality Diagonals
```
D+ = {0x0, 0x5, 0xA, 0xF}
D- = {0x3, 0x6, 0x9, 0xC}

D+ properties:
  XOR: 0 ⊕ 5 ⊕ A ⊕ F = 0
  SUM: 0 + 5 + 10 + 15 = 30

D- properties:
  XOR: 3 ⊕ 6 ⊕ 9 ⊕ C = 0
  SUM: 3 + 6 + 9 + 12 = 30

Both diagonals are closed (complete) structures.
```

---

## Identity Rules

### Rule: Place-Value Identity (Canonical)
```
CORRECT:
  citation_identity = S0, S1, S2, S3, S4, S5, S6, S7, CAR, CDR, PAYLOAD, MASK
  (the full structured relation)

WRONG:
  citation_identity = hash(citation)
  (surrogates are NOT protocol identity)
```

**Why:** Hashing collapses structure and breaks:
- Composition (a • b ≠ hash(a) ⊕ hash(b))
- Carry-forward (cannot re-resolve)
- Reversibility (hash is one-way)

### Rule: Scope Identity
```
CORRECT:
  scope_identity = fs.o/<file>/gs.o/<group>/rs.o/<record>/us.o/<unit>

WRONG:
  scope_identity = hash(scope)
```

### Rule: Receipt Identity
```
CORRECT:
  receipt_identity = ring[slot5040] = validated OmiInst

WRONG:
  receipt_identity = hash_of_receipt
```

Receipts are identified by their ring slot (computed from place-value), not by a hash.

---

## Peer-to-Peer Rules

### Rule: Receipt Exchange
```
Peer A has receipts in ring

export() → list of (slot, receipt_data)

Send to Peer B

Peer B receives (slot, receipt_data)

For each receipt:
  validate_locally(receipt_data)  // re-run Tetragrammatron
  if valid then ring[slot] = receipt_data
  else reject_receipt()

Both peers now have identical validated receipts
Both peers project identical state
```

**Invariant:** Send ≠ accept. Only local validation accepts.

### Rule: No Global Authority
```
Peer A may have different REPO.md than Peer B

Peer A accepts recipe changes
Peer B rejects recipe changes

Both are correct within their own authority.

Disagreement is not corruption.

If peers need to agree, they negotiate and update their own REPO.md.
```

---

## Error Handling

### Rule: Errors Are Receipts
```
Errors are not exceptions that halt execution.

Errors are citations that failed validation.

Example:
  (citation "malformed input") → validate() → REJECT
  ring[slot] = { citation: "malformed input", result: "rejected" }
  User inspects error receipt like any other receipt
```

### Error Types
```
citation-error       invalid address frame structure
validation-error     fails validation rule (Q(S)≠0, etc.)
ring-error           ring storage/access error
width-error          delta16 invoked with wrong width
permission-error     authorization check fails
device-error         hardware device not found
timeout-error        operation exceeds time budget
```

Each error is recorded with:
```
{ citation, reason, scope, result: "rejected", timestamp }
```

---

## Covariant Rule (The Foundation)

All behavior is derived from:

```
Code is data.
File is port.
Notation is citation.
Canon validates.
Cosmology projects.
Receipt accepts.
```

Meaning:

```
Code may be executed, but it is carried as data until validated.
Files are transport boundaries (ports), not authority.
Notation is a citation candidate (not yet accepted).
Only the canonical validator (Tetragrammatron) may accept.
Projection is display only (not authority).
Receipt is the only authoritative record.
```

---

## Implementation Checklist for LLMs

When implementing OMI, ensure:

- [ ] Parse notation to OmiInst (not directly to hash)
- [ ] Compute instruction witness for inspection, not protocol identity
- [ ] Validate against deterministic rule, not hash match
- [ ] Compute slot5040 from place-value (fano7, role3, local240)
- [ ] Write receipts to ring by slot, not by hash lookup
- [ ] Project only from validated receipts, not from hashes
- [ ] Enforce lazy evaluation (no hardware action before receipt)
- [ ] Keep hashing in carrier layer only, never in protocol core
- [ ] Mark all hash fields as `CARRIER_METADATA_ONLY`
- [ ] Use place-value scope for identity everywhere
- [ ] Separate canon (validator) from cosmology (projector)
- [ ] Ensure determinism (same input → same output always)
- [ ] Record errors as receipts, not exceptions
- [ ] Implement P2P as local validation + receipt exchange
- [ ] Document which authority owns which layer

---

## Quick Reference

### Parsing
```
text notation → parse_omi_addr() → OmiInst
```

### Citation
```
OmiInst → compute_witness() → uint16 (for inspection only)
```

### Validation
```
OmiInst + rule → validate() → accept | reject
```

### Recording
```
OmiInst + slot5040 → ring_store() → Receipt
```

### Projection
```
Receipt → metatron() → {Smith, Geometry, JSON, SVG, DOM, ...}
```

### Lookup
```
slot5040 → ring[slot] → Receipt (NOT hash lookup)
```

---

## What This Document Is Not

- Not a tutorial (see README for tutorials)
- Not an explanation (see Specification documents for reasoning)
- Not a philosophy (see Theory documents for foundations)

## What This Document Is

- An executable specification
- A reference for implementation
- A test specification (with test vectors)
- A behavior contract
- Something an LLM can read and implement directly

---

## Authority

This document is governed by:

```
omi_pi_proof.v           (Coq proofs - immutable)
OMI-Lisp Specification   (protocol rules)
This document (executable behaviors)
```

If there is a conflict, read in that order.
