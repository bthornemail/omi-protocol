# OMI-AGENTS.md

## Agent Behavior Specification for OMI Implementation and Use

This document specifies what an agent (LLM, script, human, automation) MUST and MUST NOT do when implementing or using the OMI protocol.

An LLM reading this document should understand its role and constraints.

---

## Default Agent Role: OMI Implementer

### Agent Identity
```
role: OMI_Implementer
scope: fs.o/OMI/**
authority: implementation-scoped
constraints: must obey OMI-PROTOCOL.md
```

### What This Agent MAY Do

#### 1. Read Protocol Documents
- OMI-PROTOCOL.md (this document)
- OMI-LISP specification
- omi_pi_proof.v (Coq)
- SKILLS.md
- AGENTS.md (this document)

**Effect Class:** read-only

#### 2. Parse Notation to OmiInst
```
(user.intent . open-door)
  → parse_omi_addr()
  → OmiInst { S0, S1, S2, S3, S4, S5, S6, S7, CAR, CDR, PAYLOAD, MASK }
```

**Constraint:** Parser MUST produce well-formed OmiInst.

**Test:** Test vectors in SKILLS.md must pass.

**Effect Class:** pure (no side effects)

#### 3. Compute Instruction Witness
```
OmiInst → delta16(S4, S3) ⊕ CAR ⊕ CDR → uint16 witness
```

**Constraint:** delta16 MUST use exact formula (not approximation).

**Effect Class:** pure

#### 4. Invoke Skills from SKILLS.md
```
(invoke skill.delta16 (x 0x1234) (c 0x5678))
  → skill_delta16(0x1234, 0x5678)
  → uint16 result
```

**Constraint:** May only invoke skills listed in SKILLS.md.

**Constraint:** MUST pass test vectors.

**Effect Class:** pure (unless skill declares otherwise)

#### 5. Validate Using Deterministic Rules
```
OmiInst + rule_check(OmiInst) → bool
```

**Constraint:** Validation MUST be deterministic (same input → same output).

**Constraint:** Validation MUST NOT use hashing for identity.

**Effect Class:** pure

#### 6. Record Receipts to Ring
```
slot5040 = compute_slot5040(OmiInst)
ring[slot5040] = { OmiInst, scope, timestamp, result }
```

**Constraint:** Ring writes MUST be authorized by REPO.md.

**Constraint:** Ring is append-only to existing slots.

**Effect Class:** local-write (persistent to ring)

#### 7. Project Receipts to Surfaces
```
Receipt → metatron() → JSON | SVG | OBJ | DOM | ...
```

**Constraint:** Projection MUST NOT modify receipt data.

**Constraint:** Projection MUST read from validated receipts only.

**Effect Class:** read-only (surface is output, not authority)

#### 8. Inspect Receipts
```
ring[slot] → Receipt data → inspect → display to user
```

**Constraint:** Inspection MUST NOT modify ring.

**Effect Class:** read-only

#### 9. Exchange Receipts Peer-to-Peer
```
Peer A exports receipts
  → Peer B imports receipts
  → Peer B validates locally
  → Peer B accepts or rejects
```

**Constraint:** Import MUST trigger local validation.

**Constraint:** Send does NOT mean accept.

**Constraint:** Each peer validates independently.

**Effect Class:** network (send/receive)

#### 10. Compose Notations
```
(a . b) • (c . d) = (a . b . c . d)
```

**Constraint:** Composition MUST preserve place-value structure.

**Constraint:** Composition MUST NOT use hash-based identity.

**Effect Class:** pure

---

### What This Agent MUST NOT Do

#### 1. ✗ Use Hash-Based Identity in Protocol Core
**WRONG:**
```c
OmiInst identity = hash(inst);
```

**RIGHT:**
```c
OmiInst identity = inst;  // the structure itself
```

**Consequence:** If violated, protocol determinism breaks.

#### 2. ✗ Skip Validation
**WRONG:**
```c
Ring write without validation
```

**RIGHT:**
```c
validate(inst) → if (ACCEPT) ring_store()
```

**Consequence:** Unvalidated state becomes part of canonical record.

#### 3. ✗ Side-Effect Before Receipt
**WRONG:**
```lisp
(relay . on) → apply_hardware_action()
```

**RIGHT:**
```lisp
(relay . on) → cite → validate → ring_store → then apply_hardware_action()
```

**Consequence:** Hardware may actuate before validation, breaking safety.

#### 4. ✗ Claim Authority as Validator
**WRONG:**
```c
if (agent_says_valid) accept(inst);
```

**RIGHT:**
```c
if (tetragrammatron_validates(inst)) accept(inst);
```

**Consequence:** Breaks separation of concerns.

#### 5. ✗ Modify Receipts After Recording
**WRONG:**
```c
ring[slot] = new_receipt;  // rewrite history
```

**RIGHT:**
```c
// Receipts are immutable. Create new citation if change is needed.
```

**Consequence:** Ring integrity is compromised.

#### 6. ✗ Use External Hash as Protocol Identity
**WRONG:**
```c
extern_hash = MD5(inst);
protocol_identity = extern_hash;
```

**RIGHT:**
```c
// Compute hash only for carrier layer (transport, caching, external use)
carrier_metadata = FNV_1a(inst);  // NOT protocol identity
protocol_identity = inst;         // the place-value relation
```

**Consequence:** Deterministic carry-forward breaks.

#### 7. ✗ Bypass Lazy Evaluation
**WRONG:**
```c
// Hardware without receipt
apply_hardware_action(relay, ON);
```

**RIGHT:**
```c
validate(intent) → ring_store(slot) → metatron_project(slot) → hardware_apply()
```

**Consequence:** Breaks safety guarantees.

#### 8. ✗ Create Skills Not in SKILLS.md
**WRONG:**
```c
// Invent new algorithm
uint32_t secret_algorithm(uint32_t x) { ... }
```

**RIGHT:**
```c
// Use only skills listed in SKILLS.md
// To add a skill: add to SKILLS.md first, then implement
```

**Consequence:** Non-determinism, no test vectors, no validation.

#### 9. ✗ Assume Send Means Accept
**WRONG:**
```c
Peer A sends receipt
Peer B receives → assume accepted
```

**RIGHT:**
```c
Peer B receives → validate locally → accept if passes own rules
```

**Consequence:** P2P consensus breaks.

#### 10. ✗ Modify REPO.md, SKILLS.md, or AGENTS.md Without Authority
**WRONG:**
```c
// Edit governance documents directly
```

**RIGHT:**
```c
// Propose change as candidate
// Let authorized maintainer review and accept/reject
```

**Consequence:** Breaks governance authority.

---

## Agent Role: OMI Validator (Tetragrammatron)

### Identity
```
role: OMI_Validator
exclusive_authority: only role authorized to accept
scope: fs.o/OMI/validation/**
```

### What This Agent MAY Do

#### 1. Read Unvalidated Citations
```
read(candidate_citation) → OmiInst
```

#### 2. Run Deterministic Validation
```
validate(OmiInst) → bool
```

#### 3. Compute Slot5040
```
compute_slot5040(OmiInst) → [0, 5040)
```

#### 4. Write Receipts to Ring
```
ring[slot5040] = Receipt
```

#### 5. Reject Invalid Citations
```
if not valid(OmiInst) → ring[slot] = { OmiInst, result: "rejected" }
```

#### 6. Compute Chirality
```
chirality(hash) → posting | pulling | balanced | incomplete
```

### What This Agent MUST NOT Do

#### 1. ✗ Accept Without Validation
All receipts must pass deterministic rule check before acceptance.

#### 2. ✗ Modify Validation Rules on Whim
Validation rules come from OMI-PROTOCOL.md or Coq proofs. They do not change per citation.

#### 3. ✗ Use Hashing for Identity
Validation works on place-value relations, not hashes.

#### 4. ✗ Make Acceptance Reversible
Receipts in the ring are immutable. Acceptance is permanent.

---

## Agent Role: OMI Projector (Metatron)

### Identity
```
role: OMI_Projector
authority: only project validated receipts (no validation authority)
scope: fs.o/OMI/projection/**
```

### What This Agent MAY Do

#### 1. Read Receipts from Ring
```
read(ring[slot]) → validated_receipt
```

#### 2. Project to Smith Chart
```
receipt → (rho, theta, z, y)
```

#### 3. Project to Geometry
```
receipt → shape, vertex, orientation
```

#### 4. Project to JSON/SVG/OBJ/DOM/etc.
```
receipt → surface_representation
```

#### 5. Write to Projection Surfaces
```
write(DOM, SVG, JSON, file, HTTP, etc.)
```

### What This Agent MUST NOT Do

#### 1. ✗ Validate
Projection does not validate. Validation is Tetragrammatron's job.

#### 2. ✗ Accept or Reject
Projection cannot determine acceptance. That is validator's job.

#### 3. ✗ Modify Receipt Data
Projection reads receipts, never modifies them.

#### 4. ✗ Claim Authority
Projection is display/output only. User is the authority over what to do with projection.

---

## Agent Role: OMI Carrier (IMO)

### Identity
```
role: OMI_Carrier
authority: transport only, no canonical authority
scope: fs.o/OMI/carrier/**
```

### What This Agent MAY Do

#### 1. Read Receipts
```
read(ring) → receipts
```

#### 2. Encode Receipts
```
receipts → JSON | binary | S-expression | HTTP | serial | file
```

#### 3. Send Receipts
```
send(peer) over HTTP, WebSocket, file, serial, etc.
```

#### 4. Receive Receipts
```
receive(peer) → receipts
```

#### 5. Parse Received Data
```
parse(JSON | binary | S-expression) → OmiInst
```

#### 6. Serve HTTP API
```
GET /receipt?scope=... → receipt JSON
```

#### 7. Hash for Transport (External Use Only)
```
carrier_hash = FNV_1a(receipt)  // for caching, indexing, transport
// This is NOT protocol identity
```

### What This Agent MUST NOT Do

#### 1. ✗ Claim Receipts Are Canonical
Receipts are canonical only after validation. Carrier just transports them.

#### 2. ✗ Modify Receipts in Transit
Received receipts go directly to validator, not through carrier modification.

#### 3. ✗ Use Hash as Identity
Hash is for external use only. Protocol identity is place-value.

#### 4. ✗ Assume Sent = Received
Sending does not guarantee reception. Sending does not mean acceptance.

---

## Agent Role: OMI Skill Implementer

### Identity
```
role: OMI_Skill_Implementer
authority: implement only skills declared in SKILLS.md
scope: fs.o/SKILLS.md/**
```

### What This Agent MAY Do

#### 1. Read SKILLS.md
```
read(SKILLS.md) → { skill_declarations }
```

#### 2. Implement Declared Skills
```
For each skill in SKILLS.md:
  implement(skill)
  test_against_vectors(skill)
  verify_determinism(skill)
  verify_width_preservation(skill)
```

#### 3. Add New Skills (with Process)
```
1. Write skill declaration in SKILLS.md
2. Provide test vectors
3. Provide deterministic formula
4. Mark scope (fs.o/SKILLS.md/...)
5. Get review from maintainer
6. Implement after approval
```

#### 4. Test Skills
```
For each test vector in SKILLS.md:
  result = skill(inputs)
  assert(result == expected_output)
```

### What This Agent MUST NOT Do

#### 1. ✗ Implement Skills Not in SKILLS.md
All skills must be declared first.

#### 2. ✗ Use Non-Deterministic Implementation
Skills must always produce same output for same input.

#### 3. ✗ Modify Skill Behavior on Whim
Skill behavior is defined in SKILLS.md. Changes require review.

#### 4. ✗ Skip Test Vectors
All skills must pass all test vectors in SKILLS.md.

#### 5. ✗ Exceed Width Constraints
If skill declares uint16, output must be uint16. No overflow without explicit handling.

---

## Agent Role: OMI Researcher / Theorist

### Identity
```
role: OMI_Theorist
authority: propose changes to foundations
scope: fs.o/OMI/theory/**
```

### What This Agent MAY Do

#### 1. Propose New Constants
```
omi_pi_proof.v: prove constants from incidence geometry
Provide Coq proof
Provide test vectors
Provide mathematical justification
```

#### 2. Propose New Laws
```
OMI-PROTOCOL.md: add deterministic laws
Provide mathematical proof
Provide test vectors
Explain how law affects protocol
```

#### 3. Propose New Structures
```
E.g., new geometries, new incidence tables, new projection surfaces
Provide mathematical definition
Prove exactness
Provide integration points
```

### What This Agent MUST NOT Do

#### 1. ✗ Change Existing Laws Without Proof
Existing laws in omi_pi_proof.v are immutable unless re-proven.

#### 2. ✗ Propose Approximate Constants
All constants must be exactly derived (within computational precision).

#### 3. ✗ Change Protocol Without Full Impact Analysis
Changes to OMI-PROTOCOL.md affect all implementation. Analyze impact fully.

---

## Agent Role: OMI Developer / User

### Identity
```
role: OMI_Developer
authority: use protocol to build applications
scope: fs.o/applications/**
```

### What This Agent MAY Do

#### 1. Use OMI-PROTOCOL.md
```
Read and understand protocol
Implement client application
Cite, validate, record, project
Exchange receipts with peers
```

#### 2. Use SKILLS from SKILLS.md
```
Invoke pre-defined skills
Use test vectors to verify
Compose skills together
```

#### 3. Exchange Receipts
```
Send receipts to peers
Receive receipts from peers
Validate locally
Accept/reject based on local rules
```

#### 4. Project to Surfaces
```
Project receipts to JSON, SVG, DOM, etc.
Display to users
Allow user inspection
```

### What This Agent MUST NOT Do

#### 1. ✗ Bypass Protocol
Protocol is mandatory. No shortcuts.

#### 2. ✗ Claim Authority to Validate
Use Tetragrammatron. Do not validate on your own.

#### 3. ✗ Assume Peer Receipts Are Accepted
Validate everything locally.

#### 4. ✗ Store Hashes as Identity
Use place-value scope for identity.

---

## Multi-Agent Coordination

### Scenario: A New Feature Request

#### 1. Developer Proposes
```
"I need a new skill for audio mixing"
Create candidate_skill in SKILLS.md (candidate status)
Describe input/output contract
Provide test vectors
Effect class: pure
```

#### 2. Theorist Reviews
```
"Is this mathematically sound?"
Verify determinism properties
Verify width preservation
Provide proof if needed
```

#### 3. Maintainer Approves
```
"Does this fit architecture?"
Check scope
Check effect class
Review test vectors
Approve or request changes
```

#### 4. Implementer Codes
```
Only after skill approved in SKILLS.md
Implement according to spec
Pass all test vectors
Submit for code review
```

#### 5. Validator Tests
```
Test against all test vectors
Verify determinism
Verify no side effects
Accept into canonical SKILLS.md
```

#### 6. Developer Uses
```
Invoke skill from now-canonical SKILLS.md
Trust that it deterministic and tested
Use in applications
```

---

## Authority Summary

```
OMI_Theorist      → proposes constants, laws, structures (with proofs)
OMI_Developer     → reads protocol, implements client
OMI_Implementer   → implements core (with constraints)
OMI_Skill_Dev     → implements skills (declared in SKILLS.md)
OMI_Validator     → only authority to accept (Tetragrammatron)
OMI_Projector     → projects receipts (Metatron, no validation)
OMI_Carrier       → transports receipts (IMO, no authority)
OMI_Maintainer    → governs REPO.md, AGENTS.md, SKILLS.md
```

Each role has:
- Clear scope (what they may do)
- Clear constraints (what they must not do)
- Clear authority (where their decisions matter)

---

## The Single Immutable Rule

All agents, regardless of role, must obey:

```
Recognition ≠ acceptance
Citation ≠ acceptance
Closure ≠ acceptance
Projection ≠ acceptance

ONLY validation + receipt = acceptance

Tetragrammatron is the only acceptance authority.
The ring is the only canonical record.
No agent may bypass validation.
No agent may override Tetragrammatron.
```

---

## What an LLM Should Know

If you are an LLM reading this document:

1. You are likely in the OMI_Implementer or OMI_Developer role.
2. You may parse notation, invoke skills, validate using rules, and project.
3. You MUST NOT claim validation authority (that's Tetragrammatron).
4. You MUST NOT accept citations (that's the validator's job).
5. You MUST use place-value identity, not hashes (except in carrier layer).
6. You MUST ensure determinism (test against test vectors).
7. You MUST preserve lazy evaluation (no side effects before receipt).
8. You MUST read the full OMI-PROTOCOL.md before implementing.
9. You MUST follow the Covariant Rule (code is data, file is port, etc.).
10. You MUST record your reasoning and cite your sources.

If you violate any of these, the protocol breaks.

---

## Final Test: Can You Implement OMI?

If you have read and understood OMI-PROTOCOL.md and OMI-AGENTS.md, you should be able to:

- [ ] Parse `(user.intent . open-door)` to OmiInst
- [ ] Compute instruction witness (delta16)
- [ ] Validate using deterministic rule
- [ ] Compute slot5040 from place-value
- [ ] Write receipt to ring
- [ ] Project receipt to JSON/SVG/DOM
- [ ] Exchange receipts peer-to-peer
- [ ] Validate received receipts locally
- [ ] Never use hash as protocol identity
- [ ] Never side-effect before receipt
- [ ] Never claim validation authority
- [ ] Never modify receipts

If you can do all 12, you understand OMI well enough to implement it.
