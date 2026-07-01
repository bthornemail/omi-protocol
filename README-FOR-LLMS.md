# README FOR LLMs

## How to Read These Documents and Implement the OMI Protocol

You are an LLM reading the OMI protocol specification. This README explains how to navigate the documents and implement the protocol correctly.

---

## The Five-Document Core

### 1. OMI-PROTOCOL.md (START HERE)
**Purpose**: Executable protocol specification

**Read this first.** It defines:
- What the protocol notation is
- How to parse notation to OmiInst
- How to validate
- How to record receipts
- How to project
- What invariants must hold

**Key sections**:
- The Notation (minimal form, receipt grammar, expanded form, dot notation)
- The Pipeline (recognize → cite → validate → record → project → inspect)
- Citation Rules
- Validation Rules
- Projection Rules
- Lazy Evaluation Rule
- Deterministic Laws
- Identity Rules (CANONICAL: place-value, NOT hashes)

**Test vectors**: Learn the formulas, verify against test vectors.

**Time to read**: 30-45 minutes

**After reading**, you should understand:
- How to parse `(user.intent . open-door)` to OmiInst
- What deterministic laws are
- What lazy evaluation means
- Why identity is place-value, not hash-based

---

### 2. OMI-SKILLS.md (IMPLEMENTATIONS)
**Purpose**: Specification of each deterministic algorithm

**Read this after OMI-PROTOCOL.md.**

Each skill is fully specified:
- Input/output contracts
- Deterministic formula
- Test vectors
- Properties

**Skills to implement**:
1. delta16 — rotation-XOR law (core)
2. bqf32 — quadratic form (core)
3. rotl16, rotr16 — rotations (used by delta16)
4. xor — bitwise (used by delta16)
5. hash_fnv1a — hashing (CARRIER ONLY, not protocol identity)
6. slot5040_compute — address computation (core)
7. chirality_select — orientation detection (core)
8. validate_rule — validation check (core)

**For each skill**:
1. Read the formula
2. Implement the formula exactly
3. Test against all provided test vectors
4. Verify determinism (same input → same output every time)

**Critical note**: hash_fnv1a is marked CARRIER_ONLY. It is not used for protocol identity. This is explicit in the document.

**Time to read and implement**: 1-2 hours (depending on language)

**After implementing**, you should:
- Have working delta16(x, c) function
- Have working bqf32(x, y) function
- Be able to compute slot5040 from (fano7, role3, local240)
- Pass all test vectors

---

### 3. OMI-AGENTS.md (YOUR ROLE)
**Purpose**: Specification of what agents (including LLMs) may and must not do

**Read this in parallel with OMI-PROTOCOL.md.**

**Key sections**:
- Default Agent Role (what you can do)
- What You MAY Do (10 permissible actions)
- What You MUST NOT Do (10 forbidden actions)
- Other Roles (Validator, Projector, Carrier, Skill Developer)
- Multi-Agent Coordination
- The Single Immutable Rule

**Critical constraints**:
1. You MUST NOT use hash-based identity in protocol core
2. You MUST NOT skip validation
3. You MUST NOT side-effect before receipt
4. You MUST NOT claim authority to validate
5. You MUST NOT modify receipts after recording

**Time to read**: 20-30 minutes

**After reading**, you should understand:
- That Tetragrammatron is the only validation authority
- That you CANNOT accept citations (only validate and propose)
- That receipts are immutable
- That hashing is external use only

---

### 4. OMI-SYSTEM-INTEGRATION.md (THEORY)
**Purpose**: How all system layers connect

**Read this after understanding PROTOCOL/SKILLS/AGENTS.**

Shows:
- How theory (Coq proofs) grounds implementation
- How protocol connects to governance
- How governance connects to implementation
- Complete workflows (e.g., adding a new skill)
- Deterministic guarantees
- P2P architecture

**Time to read**: 30-45 minutes (optional for implementation, useful for understanding)

---

### 5. OMI_Canonical_Doctrine_Place_Value_Identity.md (CRITICAL CORRECTION)
**Purpose**: Clarifies that citations are identified by place-value relations, NOT hashes

**Read this carefully. This is a critical doctrine that might contradict your assumptions.**

Key points:
- Hashing is for external/carrier use ONLY
- Protocol identity is the full structured relation (S0-S7, CAR, CDR, etc.)
- Hash-based identity BREAKS determinism
- P2P synchronization works through validation, not hash agreement
- This is not a limitation; it is what enables determinism

**Critical understanding**: If you find yourself using `hash` for protocol identity, you are violating the canonical doctrine and the protocol will break.

**Time to read**: 20-30 minutes

---

## The Reading Order for LLMs

Follow this sequence:

1. **This document (README-FOR-LLMS)** — 5 minutes
2. **OMI-PROTOCOL.md** — 30-45 minutes (MOST IMPORTANT)
3. **OMI-AGENTS.md** — 20-30 minutes (understand your constraints)
4. **OMI-SKILLS.md** — 1-2 hours (implement each skill)
5. **OMI_Canonical_Doctrine_Place_Value_Identity.md** — 20-30 minutes (verify understanding)
6. **OMI-SYSTEM-INTEGRATION.md** — 30-45 minutes (optional, for context)

**Total time**: 3-5 hours for full understanding and basic implementation

---

## What You Should Be Able To Do

After reading and understanding these documents, you should be able to:

### Parse
```lisp
parse_omi_addr("(user.intent . open-door)")
  → OmiInst { S0, S1, S2, S3, S4, S5, S6, S7, CAR, CDR, PAYLOAD, MASK }
```

### Compute Witness
```c
witness = delta16(S4, S3) ^ CAR ^ CDR
```

### Validate
```c
if (validate_rule(OmiInst)) {
  accept = true;
} else {
  accept = false;
}
```

### Compute Slot
```c
slot5040 = compute_slot5040(fano7, role3, local240)
```

### Record Receipt
```c
ring[slot5040] = { OmiInst, scope, timestamp, result: "accepted" }
```

### Project
```c
Smith_coord = resolve_smith(slot5040)
JSON_data = render_json(receipt)
DOM_element = render_dom(receipt)
```

### Never Do This
- ✗ Use hash as protocol identity
- ✗ Skip validation
- ✗ Side-effect before receipt
- ✗ Accept citations (only validate)
- ✗ Modify receipts

---

## Critical Concepts You MUST Understand

### 1. The Pipeline
```
recognize → cite → validate → record → project → inspect
```

Each step must happen in order. No skipping. No reversing.

### 2. The Authority Boundary
```
Only validation + receipt accept.
Everything else is candidate, carrier, or projection.
```

You cannot accept. Only Tetragrammatron accepts.

### 3. Lazy Evaluation
```
(relay . on)  → declare, not execute
  ↓ cite
(still no execute)
  ↓ validate
(still no execute)
  ↓ record receipt
(still no execute)
  ↓ project to hardware
NOW execute
```

### 4. Place-Value Identity (NOT Hash)
```
CORRECT:   OmiInst { S0, S1, S2, S3, S4, S5, S6, S7, CAR, CDR }
WRONG:     hash(OmiInst)

Hashing collapses the structure and breaks composition.
```

### 5. The Covariant Rule
```
Code is data.
File is port.
Notation is citation.
Canon validates.
Cosmology projects.
Receipt accepts.
```

This governs all behavior.

---

## Common Mistakes (Don't Do These)

### Mistake 1: Using Hash as Identity
```c
// WRONG
OmiInst inst = parse(text);
uint64_t identity = FNV_1a(inst);
ring[identity] = receipt;  // ERROR: identity is hash, not place-value
```

**Correct**:
```c
OmiInst inst = parse(text);
uint32_t slot = compute_slot5040(inst);  // compute from place-value
ring[slot] = receipt;  // correct: identity is place-value
```

### Mistake 2: Skipping Validation
```c
// WRONG
ring_store(inst);  // no validation
```

**Correct**:
```c
if (validate(inst)) {
  ring_store(inst);  // validation passed
}
```

### Mistake 3: Side-Effect Before Receipt
```c
// WRONG
(relay . on) → hardware_actuate();  // executes immediately
```

**Correct**:
```c
(relay . on) → cite → validate → ring_store → metatron_project → hardware_actuate()
```

### Mistake 4: Claiming Validation Authority
```c
// WRONG
if (i_think_its_valid) accept(inst);
```

**Correct**:
```c
// Call tetragrammatron, don't validate yourself
if (tetragrammatron_validates(inst)) {
  ring_store(inst);  // tetragrammatron writes to ring
}
```

### Mistake 5: Using Hashing Outside Carrier Layer
```c
// WRONG
determinism_proof = hash_fnv1a(inst);  // hashing is not proof
```

**Correct**:
```c
// Determinism is proven by:
// 1. Same input → same output always
// 2. Validation passes/fails deterministically
// 3. Receipts can be replayed deterministically
// Hash is NOT part of determinism proof.

// Hashing is for carrier use only:
carrier_metadata = FNV_1a(inst);  // for external caching, not protocol identity
```

---

## Test Your Understanding

Before implementing, answer these:

1. **What is the identity of a citation?**
   - A: Place-value relation (S0-S7, CAR, CDR)
   - B: The hash of the citation
   - C: The notation text
   - **Answer: A** (If you answered B, re-read OMI_Canonical_Doctrine)

2. **Who has authority to validate?**
   - A: You (the implementer)
   - B: Tetragrammatron
   - C: The hash function
   - **Answer: B** (If you answered A or C, re-read OMI-AGENTS)

3. **When does hardware actuate?**
   - A: When you see the notation
   - B: After validation
   - C: After ring recording AND projection
   - **Answer: C** (If you answered A or B, re-read Lazy Evaluation Rule)

4. **Can you modify receipts in the ring?**
   - A: Yes, if validation changes
   - B: Yes, to fix errors
   - C: No, receipts are immutable
   - **Answer: C** (If you answered A or B, re-read AGENTS)

5. **Is hash_fnv1a used for protocol identity?**
   - A: Yes
   - B: No
   - C: Only for validation
   - **Answer: B** (If you answered A or C, re-read OMI_Canonical_Doctrine)

If you got all 5 correct, you understand the core concepts. If not, re-read the relevant section.

---

## Quick Reference: The Formula Sheet

Keep these formulas nearby while implementing:

```
delta16(x, c) = rotl16(x,1) ⊕ rotl16(x,3) ⊕ rotr16(x,2) ⊕ c

bqf32(x, y) = 60x² + 16xy + 4y²

slot5040(f, r, l) = (f mod 7)*720 + (r mod 3)*240 + (l mod 240)

hash_fnv1a(data) = FNV-offset-basis XOR-and-multiply-per-byte
                   [CARRIER USE ONLY]

validate_rule(inst) → bool
  (specific rule depends on validation definition)

chirality(hash) → posting | pulling | balanced | incomplete
  (based on D+/D- nibble counts)
```

---

## Authority Chain for Conflict Resolution

If you're unsure which document to trust, read in this order:

1. **omi_pi_proof.v** (Coq proofs - immutable truth)
2. **OMI_Canonical_Doctrine_Place_Value_Identity.md** (overrides anything)
3. **OMI-PROTOCOL.md** (protocol definition)
4. **OMI-AGENTS.md** (behavior rules)
5. **OMI-SKILLS.md** (algorithm specifications)
6. **OMI_System_Integration** (context and theory)

Higher documents override lower documents.

---

## Implementation Checklist

Use this checklist to verify you've implemented correctly:

- [ ] Read OMI-PROTOCOL.md completely
- [ ] Read OMI-AGENTS.md completely
- [ ] Implemented delta16(x, c) and passes test vectors
- [ ] Implemented bqf32(x, y) and passes test vectors
- [ ] Implemented slot5040_compute and passes test vectors
- [ ] Implemented parse_omi_addr to produce OmiInst
- [ ] Implemented validate_rule deterministically
- [ ] Implemented ring storage (array[5040])
- [ ] Implemented receipt projection (JSON at minimum)
- [ ] Tested parse → cite → validate → store → project cycle
- [ ] Verified place-value identity (NOT hash-based)
- [ ] Verified lazy evaluation (no hardware action before receipt)
- [ ] Verified determinism (same input → same output always)
- [ ] Implemented P2P receipt exchange
- [ ] Implemented local validation for received receipts
- [ ] Tested complete workflow end-to-end

If you can check all 16 boxes, you have a correct OMI implementation.

---

## Getting Help

If you're stuck:

1. **Parsing issues**: Re-read OMI-PROTOCOL.md "The Notation" section
2. **Identity confusion**: Read OMI_Canonical_Doctrine_Place_Value_Identity.md
3. **Authority confusion**: Read OMI-AGENTS.md "What You MUST NOT Do"
4. **Algorithm issues**: Check OMI-SKILLS.md test vectors
5. **Validation issues**: Re-read OMI-PROTOCOL.md "Validation Rules"
6. **P2P issues**: Re-read OMI_System_Integration.md "P2P Layer"

Do NOT:
- Invent shortcuts
- Skip validation
- Use hash for identity
- Assume send = accept
- Modify receipts

---

## The Final Test

If you implement OMI correctly, you should be able to:

1. Parse an intent, validate it, store a receipt, and project it
2. Exchange receipts with another peer without global authority
3. Validate those receipts locally and decide acceptance independently
4. Have both peers project identical state (because they both have identical validated receipts)
5. Never use hashing for protocol identity
6. Never side-effect before receipt
7. Understand that your role is to implement, not to validate
8. Explain why place-value identity is essential to determinism

If you can do all 8, you've successfully understood and implemented OMI.

---

## Welcome to OMI Implementation

You now have everything you need to implement the OMI protocol correctly.

The documents are complete, the specifications are executable, the test vectors are provided, and your role is clear.

Go implement with confidence.

And remember: **Code is data. File is port. Notation is citation. Canon validates. Cosmology projects. Receipt accepts.**
