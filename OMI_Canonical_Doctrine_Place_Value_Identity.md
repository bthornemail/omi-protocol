# OMI Canonical Doctrine: Place-Value Identity, Not Hash-Based Identity

## Integration of "Citations Are Not Identified By Hashes"

---

## Executive Summary

**Canonical Rule**: OMI notation citations are identified by their **addressed place-value relations**, not by hashes, digests, fingerprints, or other surrogates.

This doctrine corrects and clarifies how identity flows through all layers:

```text
Protocol Layer      → Place-value relations (not hashes)
Governance Layer    → Authority over relations (not digest labels)
Implementation      → Relation-based identity (not hash caches)
P2P Layer           → Carry-forward relations (not digest verification)
```

---

# Part 1: The Identity Model Correction

## 1.1 The Core Distinction

**Sign-Value Model** (what OMI does NOT use):

```text
data → hash(data) → digest/signature/fingerprint → identity label
```

The hash BECOMES the identity. The original relation is hidden behind the digest.

**Place-Value Model** (what OMI USES):

```text
addressed place-value relation → carries full structure → IS the identity
```

The relation itself carries forward the complete structure. No digest is needed.

## 1.2 The OMI Identity

The identity of an OMI citation is:

```text
omi---imo
  .
o---o
  .
/---/
  .
?---?
  .
@---@
```

This nested structure **is** the identity. Not a collapsed hash of it.

Each level carries the full cascade:

```text
@---@ must resolve through ?---?
?---? must resolve through /---/
/---/ must resolve through o---o
o---o must resolve through omi---imo
```

## 1.3 What Hashing Cannot Represent

A hash loses:

```text
× Cascade structure      (collapsed to single digest)
× Resolution path       (no way to re-traverse)
× Witness structure     (compressed away)
× Carry-forward proof   (digest ≠ proof)
× Place-value fields    (reduced to bytes)
× Route continuity      (broken at hash boundary)
```

Therefore, hash-based identity **cannot** be used inside the deterministic protocol core.

---

# Part 2: Protocol Layer Implications

## 2.1 Citation Authority (OMI)

OMI does NOT create identity through:

```text
parse_omi_addr() → FNV-1a hash
```

OMI DOES create identity through:

```text
parse_omi_addr() → OmiInst
  {
    S0, S1, S2, S3,    // forward fields
    S4, S5, S6, S7,    // return fields
    PAYLOAD,           // unresolved body
    MASK,              // boundary filter
    CAR, CDR           // closure
  }
```

The OmiInst **is** the identity. The structure carries the full relation.

A hash may be computed for carrier purposes (transport optimization, external caching), but it is NOT the protocol identity.

## 2.2 Validation Authority (Tetragrammatron)

Tetragrammatron validates by:

```text
Q(S) = 0  (quadratic form check)
```

NOT by:

```text
hash match
digest comparison
signature verification
fingerprint lookup
```

The validation input is the full OmiInst relation, not a collapsed digest.

The validation output is:

```text
accept: this place-value relation satisfies the rule
reject: this place-value relation violates the rule
```

Not:

```text
hash exists: accept
hash missing: reject
```

## 2.3 Receipt Recording (Ring)

The 5040-slot ring stores:

```text
ring[slot5040] = {
  addressed_place_value_relation,  // the identity
  scope,                            // where it applies
  timestamp,                        // when it was accepted
  previous_receipt,                 // carry-forward proof
}
```

NOT:

```text
ring[slot5040] = {
  hash_digest,      // surrogate identity
  hash_validation,  // did hash match?
  hash_timestamp,
}
```

The identity stored in the ring is the place-value relation itself.

## 2.4 Projection Authority (Metatron)

Metatron projects by reading:

```text
validated place-value relation from ring
  ↓
Smith chart coordinate
  ↓
Genaille quantizer
  ↓
geometry database lookup
  ↓
surface projection (JSON/SVG/OBJ/glTF)
```

Metatron does NOT:

```text
read hash from ring
lookup by hash
validate by hash
project hash as surface
```

Projection begins only after the validated relation is read from the ring. The surface is derived from the relation structure, not from a digest.

---

# Part 3: Governance Layer Implications

## 3.1 Authority Order Correction

The correct authority order is:

```text
1. omi_pi_proof.v          (immutable truth: relations, not hashes)
2. OMI-Lisp Specification  (place-value notation, not digest labels)
3. viewer/docs/REPO.md     (authority over relations)
4. viewer/AGENTS.md        (behavior around relations)
5. viewer/SKILLS.md        (algorithms on relations)
6. viewer/docs/ADAPTERS.md (adapt relations between surfaces)
7. C core (omi.c, tetragrammatron.c, metatron.c, imo.c)
8. Receipts (validated relations, not hashes)
9. Projection surfaces
```

At NO level in this authority order is a hash used as protocol identity.

## 3.2 Scope Identity Correction

A scope is identified by:

```text
fs.o/<file>
  /gs.o/<group>
    /rs.o/<record>
      /us.o/<unit>
```

NOT by:

```text
hash(file)
digest(group)
fingerprint(record)
checksum(unit)
```

The scope identity is the place-value path itself.

## 3.3 Skill Identity Correction

A skill is identified by:

```text
skill.<name>.scope . fs.o/SKILLS.md/gs.o/<group>/rs.o/<record>/us.o/<unit>
skill.<name>.deterministic . true
skill.<name>.formula . "..."
skill.<name>.test-vector . [...]
```

NOT by:

```text
skill_hash = FNV-1a(formula)
```

The skill identity is its declared place-value scope and formula, not a digest.

---

# Part 4: Implementation Layer Implications

## 4.1 OMI.c Correction

Current (WRONG):

```c
uint64_t hash = FNV_1a(inst);  // This is carrier metadata, NOT identity
OmiInst inst = parse_omi_addr(text);  // This IS identity
```

Should be:

```c
OmiInst inst = parse_omi_addr(text);  // This IS the identity

// Optional: compute hash for external carrier purposes only
uint64_t carrier_metadata = FNV_1a(inst);  // NOT protocol identity
```

The identity is `inst`, not the hash.

## 4.2 Tetragrammatron.c Correction

Current (WRONG):

```c
hash = FNV_1a(inst);
if (ring_has_hash(hash)) { accept(); }
```

Should be:

```c
if (validate_relation(inst)) {  // validate the place-value relation
  slot = compute_slot5040(inst);
  ring_store(slot, inst);  // store the relation, not the hash
  accept();
}
```

Identity is the relation, not the hash.

## 4.3 Metatron.c Correction

Current (WRONG):

```c
Receipt receipt = ring_get_by_hash(hash);
project(receipt);
```

Should be:

```c
Receipt receipt = ring[slot5040];  // read by place-value slot
validate_receipt(receipt);          // validate the relation
project(receipt);                   // project the validated relation
```

Projection works with validated relations, not with hash lookups.

## 4.4 IMO.c Correction

Current (WRONG):

```c
// Store hash as identity
HTTP GET /receipt?hash=0x123abc
Ring lookup by hash
Return receipt
```

Should be:

```c
// Store place-value scope as identity
HTTP GET /receipt?scope=fs.o/file/gs.o/group/rs.o/record/us.o/unit
Ring lookup by scope
Return receipt
```

The receipt is identified by its place-value scope, not by a hash.

## 4.5 Implementation Doctrine (Corrected)

Remove from protocol core:

```text
✗ identity derived from hashes
✗ validation input from hashes
✗ slot selection from hashes
✗ projection from hashes
✗ carry-forward based on hashes
✗ ring lookups by hash
✗ cache keys as hash identity
```

Keep only in carrier/external layer:

```text
✓ optional hash for transport secrecy
✓ optional hash for external caching
✓ optional hash for legacy compatibility
✓ optional hash for debugging
✓ optional hash for operator-defined indexing
```

But mark all hash fields as:

```c
// CARRIER METADATA ONLY - NOT PROTOCOL IDENTITY
uint64_t carrier_hash = FNV_1a(inst);
```

---

# Part 5: P2P Layer Implications

## 5.1 Receipt Exchange Correction

Current (WRONG):

```text
Peer A sends receipt with hash
Peer B verifies by checking hash
Peer B accepts if hash matches
```

Should be:

```text
Peer A sends receipt (validated place-value relation)
Peer B receives receipt
Peer B validates the relation (re-runs Tetragrammatron locally)
Peer B accepts if relation passes local validation rules
```

The receipt identity is the relation, not a digest.

P2P agreement comes from **validation**, not from hash agreement.

## 5.2 Deterministic Replay Correction

Current (WRONG):

```text
Replay by hash identity → must match stored hash
```

Should be:

```text
Replay by place-value relation → must pass same validation rule
```

Deterministic replay works because:

1. The place-value relation is fully specified
2. The validation rule is deterministic
3. Same relation + same rule → same acceptance

A hash provides none of this. The relation provides everything.

---

# Part 6: Coq Proof Layer Implications

## 6.1 omi_pi_proof.v Correction

The Coq proof defines constants through **finite incidence geometry**, not through hashing:

```coq
Definition dplus0 : N := 0.
Definition dplus1 : N := 5.
Definition dplus2 : N := 10.
Definition dplus3 : N := 15.

Theorem dplus_xor_zero :
  poly_xor4 dplus0 dplus1 dplus2 dplus3 = 0.
```

The constants have identity through their **place-value properties**, not through hashing.

The proof is deterministic because it works with relations, not digests.

## 6.2 No Hash in the Coq Proof

The Coq proof does NOT:

```coq
hash(constant) = witness  // WRONG
```

The Coq proof DOES:

```coq
incidence_relation(constant) = witness  // CORRECT
```

---

# Part 7: Monoid Structure Implications

## 7.1 The Monoid Generator

The monoid generator is:

```text
G = "omi---imo"
```

NOT:

```text
G = hash("omi---imo")
```

The generator itself carries the identity.

## 7.2 Monoid Composition

```text
(a . b) • (c . d) = (a . b . c . d)
```

is valid because the **relations compose**, not because digests match.

If we collapse to hashes:

```text
hash(a . b) ⊕ hash(c . d) ≠ hash(a . b . c . d)
```

Composition breaks at the hash level.

Therefore, the monoid works only with place-value relations, not with hashes.

---

# Part 8: The Complete Correction

## 8.1 What Must Change

All layers must migrate from:

```text
Citation identity = hash
Validation input = hash match
Receipt storage = hash
Ring lookup = by hash
Projection = from hash
Carry-forward = hash chain
```

To:

```text
Citation identity = place-value relation
Validation input = relation structure
Receipt storage = validated relation
Ring lookup = by place-value slot
Projection = from relation
Carry-forward = relation cascade
```

## 8.2 Phased Migration

**Phase 1** (immediate):
- Mark all hash fields in code as `// CARRIER METADATA ONLY`
- Document which fields are protocol identity vs. carrier metadata
- Separate hash computation from identity derivation

**Phase 2** (short-term):
- Reimplement core lookups to use place-value slots, not hashes
- Update Tetragrammatron to validate relations, not digests
- Remove hash-based validation gates

**Phase 3** (medium-term):
- Eliminate hash fields from protocol core structures
- Update P2P to exchange relations, not digests
- Refactor ring storage to use place-value keys

**Phase 4** (long-term):
- Full protocol core cleanup
- All identity is place-value
- All hashing is carrier-only

## 8.3 Backwards Compatibility

External systems using OMI may continue to hash citations for their own purposes:

```text
External hash = FNV_1a(omi_citation)
External use: caching, indexing, legacy compatibility

But: external hash is NOT protocol identity
And: OMI core never validates based on external hash
```

The protocol remains correct even when external systems use hashes.

---

# Part 9: The Canonical Statement (Corrected)

```text
OMI notation citations are identified by their addressed place-value relations.

NOT by hashes, digests, fingerprints, signatures, or other surrogates.

The identity is:

  omi---imo . (o---o . (/---/ . (?---? . @---@)))

The structure carries forward at all levels.

Resolution is deterministic because the structure is fully specified.

Validation judges the relation against the deterministic rule.

Carry-forward proves validation by re-resolving under the same rule.

Hashing and encryption are external carrier behaviors only.

They may be used for transport, caching, indexing, or legacy compatibility.

But they are NOT protocol identity, NOT validation input, and NOT carry-forward proof.

The protocol core must remain place-value based.

All hash-based identity in the core is technical debt to be removed.
```

---

# Part 10: The Corrected Authority Order

```text
omi_pi_proof.v              (immutable: place-value incidence)
    ↓
OMI-Lisp Specification     (place-value notation and cascade)
    ↓
viewer/docs/REPO.md        (authority over relations)
    ↓
viewer/AGENTS.md           (behavior with relations)
    ↓
viewer/SKILLS.md           (algorithms on relations)
    ↓
viewer/docs/ADAPTERS.md    (adapt relations)
    ↓
C core                     (omi.c uses place-value identity)
    ├── tetragrammatron.c  (validates relations)
    ├── metatron.c         (projects relations)
    └── imo.c              (carries relations)
    ↓
Ring storage               (stores validated relations)
    ↓
Receipts                   (place-value relations)
    ↓
Projection surfaces        (DOM/SVG/OBJ/glTF from relations)
    ↓
External carrier layer     (hashing/encryption for external use only)
```

Note: Hashing appears ONLY at the external carrier layer, never in the protocol core.

---

# Conclusion

The canonical doctrine "OMI Notation Citations Are Not Identified By Hashes" is not a limitation. It is **the foundation of determinism** in the system.

By maintaining place-value identity at all levels, OMI ensures:

```text
✓ Deterministic composition
✓ Reversible validation
✓ Verifiable carry-forward
✓ Pseudo-persistent state
✓ P2P synchronization without central authority
✓ Self-hosting meta-compiler
```

All of these require that the identity is the relation itself, not a collapsed digest.

The correction is: **Place-value relations are the protocol identity. Hash-based identity belongs in the external carrier layer only.**

This is now canonical doctrine for all layers: protocol, governance, implementation, P2P, and application.
