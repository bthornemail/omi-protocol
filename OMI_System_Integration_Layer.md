# OMI System Integration Layer

## Authority Hierarchy, Governance, Agents, Skills, and Mathematical Grounding

---

## Part 0: The Complete Architecture

The OMI system operates on five layers:

```text
Layer 1: Mathematical Foundation
  └─ omi_pi_proof.v (Coq proofs: constants derived from incidence)

Layer 2: OMI-Lisp Protocol
  └─ OMI-Lisp Specification (notation → citation → validation → receipt → projection)

Layer 3: Governance Framework
  └─ REPO.md (Role/Repo-Based Access Control - RRBAC)
  └─ AGENTS.md (Resolver Behavior Specification)
  └─ SKILLS.md (Deterministic Algorithm Registry)
  └─ ADAPTERS.md (Carrier and Adapter Boundaries)

Layer 4: Implementation (C Core)
  └─ omi.c (Citation Authority)
  └─ tetragrammatron.c (Validation Authority)
  └─ metatron.c (Projection Authority)
  └─ imo.c (Carrier Authority)

Layer 5: Viewer Application
  └─ viewer/docs/REPO.md (Viewer-level access control)
  └─ viewer/AGENTS.md (Viewer resolver behavior)
  └─ viewer/SKILLS.md (Viewer algorithm registry)
  └─ viewer/ADAPTERS.md (Viewer adapter boundary)
```

This document describes how these layers integrate.

---

# Part 1: Mathematical Foundation (Layer 1)

## 1.1 OMI Pi Proof System

The Coq proof `omi_pi_proof.v` establishes a fundamental principle:

```text
Constants are not stored.
Constants are derived deterministically from incidence geometry.
```

### Key Theorems in omi_pi_proof.v

**Chirality Diagonals**

```text
D+ = {0, 5, A, F}  (D+ diagonal)
D- = {3, 6, 9, C}  (D- diagonal)
```

Both diagonals:

```text
XOR to 0:  0 XOR 5 XOR A XOR F = 0
SUM to 30: 0 + 5 + A + F = 30

XOR to 0:  3 XOR 6 XOR 9 XOR C = 0
SUM to 30: 3 + 6 + 9 + C = 30
```

These prove that chirality structure is exact and complete.

**Incidence Proofs**

```text
fano_plane_valid:
  7 points
  7 lines
  3 points per line
  3 lines per point
  exactly 1 line through any 2 points
```

This is proven by exhaustive verification in Coq. No approximation.

**π Derivation**

```text
OMI_PI_Equals_Real_PI:
  OMI_PI = PI
```

π is computed as a series:

```text
OMI_PI_partial(n) = 4 * Σ(k=0 to n) [(-1)^k / (2k+1)]
```

This Gregory-Leibniz series converges to π **exactly** (in the limit).

The proof shows:

```text
omi_pi_partial_from_incidence(n) = omi_pi_partial(n)
OMI_PI_from_incidence = PI
```

Meaning: π emerges from the OMI incidence structure, not from stored approximation.

### Why This Matters

In traditional systems:

```text
π ≈ 3.14159...  (stored constant)
```

In OMI:

```text
π = 4 * Σ((-1)^k / (2k+1))  (derived from Fano/incidence)
```

The OMI approach:

1. **Is exact** — no approximation error in theory, only finite computation limits
2. **Is deterministic** — same computation always yields same result
3. **Is computable** — the series converges; we can compute any precision
4. **Is verified** — the proof is in Coq, checkable by machine

This principle applies to all mathematical constants in OMI:

```text
√2 derived from geometric construction
φ (golden ratio) derived from Fibonacci sequence
e derived from convergent series
all other constants derived from incidence or series
```

No magic constants. Everything is geometric or algebraic.

---

# Part 2: OMI-Lisp Protocol (Layer 2)

The OMI-Lisp specification (from Part 1 of the completed specification) defines:

```text
notation          (omi---imo)
citation          (OMI parses frames)
validation        (Tetragrammatron tests)
recording         (ring accepts receipt)
projection        (Metatron renders surfaces)
inspection        (user sees result)
```

The key principle:

```text
Recognition is not acceptance.
Citation is not acceptance.
Closure is not acceptance.
Projection is not acceptance.

Only validation and receipt accept.
```

This protocol is layer-agnostic. It applies equally to:

- **Hardware events** — sensor data becomes citation, validated, recorded, projected
- **User intents** — button clicks become citations, validated, recorded, projected
- **Peer exchanges** — network messages become citations, validated locally, recorded, projected
- **Governance decisions** — policy changes become citations, validated, recorded, projected

---

# Part 3: Governance Framework (Layer 3)

## 3.1 Role/Repo Based Access Control (RRBAC)

From REPO.md: The highest local collaboration authority.

### Core RRBAC Principle

```text
role controls who/what is acting
scope controls where action may occur
skill scope controls what computation may be used
effect class controls what risk the action carries
validation and receipt control what becomes accepted
```

### Scope Model (FS/GS/RS/US)

```text
FS = File Scope
GS = Group Scope
RS = Record Scope
US = Unit Scope
```

Hierarchy:

```text
fs.o/<file>
  /gs.o/<group>
    /rs.o/<record>
      /us.o/<unit>
```

Example:

```text
fs.o/SKILLS.md
  /gs.o/math
    /rs.o/delta16
      /us.o/v1
```

### Permission Model

Three levels:

```text
1. Role defines what the actor is authorized to do
   Example: (agent.role . Resolver)
            (agent.role . Contributor)
            (agent.role . Maintainer)

2. Scope limits where the actor can operate
   Example: (agent.scope . fs.o/src/**)
            (agent.scope . fs.o/docs/*)
            
3. Effect class limits the impact
   Example: (agent.effect . pure)
            (agent.effect . read-only)
            (agent.effect . repo-write)
```

### Effect Classes

```text
pure             — no side effects
read-only        — read from system
local-write      — write to temporary storage
repo-write       — write to repository
network          — send network messages
hardware         — control physical devices
security-sensitive — access credentials or sensitive data
```

A resolver granted `pure` scope cannot invoke `hardware` skills, even if authorized at role level.

### The Foundational Rule

```text
request does not accept
role does not accept
agent does not accept
skill does not accept
file does not accept
patch does not accept

validation and receipt accept
```

## 3.2 Agent Behavior Specification (AGENTS.md)

Defines what resolvers (LLM, script, human, automation) can do.

### Default Resolver Role

```omi-lisp
(agent.role . Resolver)
(agent.must . obey-REPO-md)
(agent.must . obey-AGENTS-md)
(agent.must . use-SKILLS-md-for-algorithms)
(agent.must.not . side-effect-before-receipt)
(agent.must.not . claim-authority)
```

A Resolver:

**MAY:**
- Read repository files
- Cite declarations
- Inspect receipts
- Propose work
- Project candidate previews (marked as "candidate")
- Invoke pure and read-only skills

**MUST NOT:**
- Write canonical files directly
- Merge without validation
- Delete receipts
- Rewrite receipt history
- Claim acceptance authority

### Key Constraint

```text
A Resolver may propose work.
A Resolver MUST NOT accept work.
Only validation and receipt may accept.
```

This prevents a resolver (including LLM agents) from bypassing the validation boundary.

## 3.3 Deterministic Skills Registry (SKILLS.md)

Defines reproducible algorithms.

Each skill declares:

```text
name               (human-readable)
scope              (FS/GS/RS/US address)
version            (v1, v2, etc.)
input contract     (expected input format)
output contract    (guaranteed output format)
effect class       (pure, read-only, hardware, etc.)
deterministic      (true = same input → same output always)
width constraints  (bit widths used)
overflow behavior  (how overflow is handled)
canonical algorithm (reference implementation)
test vectors       (examples with expected outputs)
validation rule    (how to verify correctness)
```

### Skill Declaration Example

```omi-lisp
(skill.delta16.name . "16-bit rotation-XOR law")
(skill.delta16.scope . fs.o/SKILLS.md/gs.o/math/rs.o/delta16/us.o/v1)
(skill.delta16.input . "(x:uint16 c:uint16)")
(skill.delta16.output . "uint16")
(skill.delta16.effect . pure)
(skill.delta16.deterministic . true)
(skill.delta16.formula . "rotl16(x,1) xor rotl16(x,3) xor rotr16(x,2) xor c")
```

### Why Skills Matter

Instead of allowing a resolver to implement arbitrary algorithms:

```text
NOT OK:
  Resolver invents new algorithm
  Resolver implements it
  No validation rule
  No test vectors
  Result is unpredictable
```

With Skills:

```text
OK:
  SKILLS.md defines algorithm
  SKILLS.md provides test vectors
  Resolver invokes skill by name
  Skill is deterministic
  Result is predictable and verifiable
```

This makes:

1. **Auditability** — every algorithm has a canonical definition
2. **Reproducibility** — same input always yields same output
3. **Reviewability** — skills can be peer-reviewed before use
4. **Testability** — test vectors validate behavior

## 3.4 Adapter and Carrier Boundaries (ADAPTERS.md)

Defines how data enters/leaves the system.

### Carrier Principle

```text
.imo files are carriers, not authority.
Carriers transport frames.
Carriers do not validate frames.
Carriers do not accept frames.
```

### Adapter Principle

```text
An adapter translates between formats.
An adapter does not change meaning.
An adapter does not claim acceptance.
```

Example adapters:

```text
JSON → OMI-Lisp            (imo.c parse_sexpr)
S-expression → uint16_t[8] (omi.c parse_omi_addr)
ESP32 serial → citation    (hardware decoder)
HTTP packet → receipt JSON (importer)
DOM event → intent         (browser adapter)
JABCode optical → uint16_t (optical decoder)
```

Each adapter:

1. Decodes the carrier format
2. Produces a structured form
3. Passes to OMI for citation
4. Citation passes to Tetragrammatron for validation
5. Validation produces receipt
6. Receipt is accepted (or rejected)

---

# Part 4: Implementation Layer (C Core)

The four-module authority split from `C_core.md`:

```text
omi.c              Citation Authority      (address parsing, instruction witness)
tetragrammatron.c  Validation Authority    (ring, receipts, acceptance)
metatron.c         Projection Authority    (geometry, surfaces, rendering)
imo.c              Carrier Authority       (files, HTTP, S-expressions, ports)
```

### Data Flow

```text
IMO receives input (file / HTTP / serial / event)
  ↓
decode to native form
  ↓
OMI parses and cites (creates OmiInst)
  ↓
Tetragrammatron validates (tests against rule)
  ↓
if accepted:
  ring_store(slot, receipt)
  ↓
Metatron projects (renders to surfaces)
  ↓
IMO carries output (writes to file / HTTP / DOM / hardware)
```

### Integration with Governance

Each C module respects RRBAC:

```text
omi.c does not validate
  (only OMI's job: cite)

tetragrammatron.c is the only validator
  (only authority to accept)

metatron.c respects effect classes
  (won't project hardware without hardware effect class)

imo.c respects scope and carrier boundaries
  (won't write to protected scopes)
```

When a skill is invoked:

```text
Skill declared in SKILLS.md
  ↓
Resolver checks REPO.md (am I authorized?)
  ↓
Resolver checks effect class (is effect permitted?)
  ↓
Resolver invokes skill (calls appropriate C function)
  ↓
C function executes (deterministic, width-preserving)
  ↓
Result is citation candidate
  ↓
Citation passes to Tetragrammatron
  ↓
Tetragrammatron validates (tests against rule)
  ↓
if accepted: ring_store() writes receipt
  ↓
Metatron projects accepted receipt
```

---

# Part 5: Viewer Application Layer (Layer 5)

The viewer is a reference implementation that demonstrates the OMI system.

### Viewer Authority Order

```text
viewer/docs/REPO.md      viewer collaboration authority (highest)
  ↓
viewer/AGENTS.md         viewer resolver behavior authority
  ↓
viewer/SKILLS.md         viewer algorithm registry
  ↓
viewer/docs/ADAPTERS.md  viewer adapter and carrier boundary
  ↓
*.imo carriers           normalized carrier declarations
  ↓
candidate declarations   proposed changes
  ↓
validation               Tetragrammatron tests
  ↓
receipts                 accepted/rejected results
  ↓
projection               rendered surfaces (DOM/SVG/WebGL)
```

### Viewer-Specific Rules

**Collaboration Roles**

```omi-lisp
(viewer.role . Resolver)       default LLM/script role
(viewer.role . Contributor)    human contributor
(viewer.role . Maintainer)     repository maintainer
(viewer.role . Auditor)        read-only oversight
```

**Viewer Scopes**

```text
fs.o/docs                viewer documentation
fs.o/src                viewer source code
fs.o/tests               viewer test files
fs.o/examples            viewer example material
fs.o/schemas             viewer data schemas
fs.o/receipts            viewer receipt exports
```

**Viewer P2P Policy**

```text
The viewer may display and exchange peer-to-peer receipts.
It does not exchange truth.

Each peer validates locally under its own REPO.md.
Each peer stores its own receipts.
Peers exchange carriers, candidates, and receipts, not truth.

No peer is global authority.
No sender is automatically trusted.
Every peer validates independently.
```

### Multimedia in Viewer

Media is treated as carrier material:

```text
video    → network effect
audio    → network/hardware effect
image    → network effect (can be security-sensitive)
3D model → hardware effect (rendering)
texture  → hardware effect (rendering)
```

Each media type must:

1. Pass carrier validation (format/integrity)
2. Pass content validation (Tetragrammatron)
3. Be recorded in receipt if accepted
4. Be projected only after receipt acceptance

---

# Part 6: Integration Example: A Complete Workflow

This example shows how all five layers integrate.

## 6.1 Scenario: Add a New Math Skill

A contributor wants to add a new skill `skill.bqf32` (binary quadratic form) to the viewer.

### Step 1: Mathematical Grounding (Layer 1)

The contributor reviews the Coq proofs and existing math proofs in omi_pi_proof.v.

They understand:

```text
Width must be preserved (32-bit input → 32-bit output)
Determinism is required (no randomness)
Test vectors must match proven properties
```

### Step 2: Define Skill (Governance - SKILLS.md)

The contributor proposes a new skill entry in the viewer/SKILLS.md:

```omi-lisp
(skill.bqf32.name . "32-bit binary quadratic form")
(skill.bqf32.scope . fs.o/SKILLS.md/gs.o/math/rs.o/bqf32/us.o/v1)
(skill.bqf32.input . "(x:uint32 y:uint32)")
(skill.bqf32.output . "uint32")
(skill.bqf32.effect . pure)
(skill.bqf32.deterministic . true)
(skill.bqf32.formula . "60*x^2 + 16*x*y + 4*y^2")
(skill.bqf32.overflow . mask-to-32)
(skill.bqf32.test-vector . ((x 1 y 2) -> 224))
(skill.bqf32.test-vector . ((x 0 y 0) -> 0))
```

This is marked as **candidate**:

```omi-lisp
(change.status . candidate)
(change.scope . fs.o/SKILLS.md/gs.o/math/rs.o/bqf32)
(change.effect . repo-write)
(change.validation . required)
(change.result . not-accepted)
```

### Step 3: Implement Skill (C Core - Layer 4)

The contributor implements the skill in C:

```c
uint32_t bqf32(uint32_t x, uint32_t y) {
    uint32_t x2 = x * x;
    uint32_t xy = x * y;
    uint32_t y2 = y * y;
    return (60 * x2) + (16 * xy) + (4 * y2);
}
```

Implementation is marked as **candidate** (not yet committed).

### Step 4: Authority Check (AGENTS.md + REPO.md)

The resolver checks permissions:

```text
Role: Contributor
Scope: fs.o/SKILLS.md/gs.o/math/rs.o/bqf32
Effect class requested: repo-write

Check against viewer/docs/REPO.md:
  "Contributor role MAY propose repo-write in fs.o/SKILLS.md"
  ✓ Authorized

Check against viewer/AGENTS.md:
  "Resolver MAY invoke pure-skills"
  "Resolver MUST NOT write canonical files directly"
  "Resolver MAY propose changes with validation required"
  ✓ Behavior permitted
```

### Step 5: Validation (Tetragrammatron - Layer 4)

The maintainer receives the proposal and validates:

1. **Skill definition check**
   - Formula is syntactically correct
   - Test vectors match formula
   - Width constraints are declared (uint32)
   - Overflow behavior is specified

2. **Code check**
   - Implementation matches formula
   - No undefined behavior
   - No side effects
   - Matches test vectors exactly

3. **Ceremony check**
   - Citation is properly formed
   - Scope is correct
   - Effect class matches implementation
   - Documentation is complete

If all checks pass:

```text
Tetragrammatron computes:
  hash = FNV-1a(skill_definition)
  slot5040 = compute_slot5040(hash)
  
  ring[slot5040] = {
    .hash = hash,
    .scope = "fs.o/SKILLS.md/gs.o/math/rs.o/bqf32/us.o/v1",
    .result = "accepted",
    .change_id = proposal_id,
    .timestamp = cycle
  }
```

### Step 6: Projection (Metatron - Layer 4 + DOM/SVG - Layer 5)

Once accepted, the receipt is projected:

```text
Metatron reads receipt:
  scope = fs.o/SKILLS.md/gs.o/math/rs.o/bqf32

Metatron projects:
  Smith chart point (from hash)
  Genaille quantizer position
  Geometry seed

Viewer (Layer 5) projects:
  DOM element: <div data-skill="bqf32" data-status="accepted">
  SVG: skill positioned on visualization
  CSS variables updated with skill metadata
```

### Step 7: Use (AGENTS.md + Layer 4)

Once accepted, any Resolver can invoke the skill:

```omi-lisp
(compute . (bqf32 3 4))
```

Resolver checks:
- Is `bqf32` in SKILLS.md? ✓ Yes
- Is effect class `pure`? ✓ Yes
- Am I authorized for `pure` skills? ✓ Yes

Resolver invokes:

```c
result = bqf32(3, 4);
// result = 60*9 + 16*12 + 4*16 = 540 + 192 + 64 = 796
```

Result becomes citation candidate:

```text
citation = (result-of-bqf32-3-4 . 796)
  ↓
Tetragrammatron validates
  ↓
ring_store(slot, receipt)
  ↓
Metatron projects
  ↓
User sees result: 796 (with receipt proof)
```

---

# Part 7: Deterministic Guarantees

## 7.1 Width Preservation

All OMI operations preserve their declared width:

```text
delta16 : uint16 → uint16
bqf32 : (uint32, uint32) → uint32
rotl16 : uint16 → uint16
```

Proved in Coq:

```coq
Theorem delta16_width_preserving : forall x c : N, delta16 x c < 65536.
```

If an operation claims `uint16` width but is implemented to use `uint32`, validation fails.

## 7.2 Reversibility

All OMI micro-operations use reversible logic:

```text
XOR is self-inverse      (x XOR y XOR y = x)
Rotation is reversible   (rotl + rotr = identity)
Bitwise AND/OR/NOT are closed operations
```

This enables **deterministic replay**:

```text
Receipt R recorded
  ↓
Receipt R moved to different peer
  ↓
Different peer validates R
  ↓
Different peer computes same results
  ↓
Both peers have identical state
```

No randomness. No approximation. Perfect determinism.

## 7.3 Fano Incidence Exactness

The Fano plane incidence structure is **exact**:

```text
7 points, 7 lines
3 points per line
3 lines per point
exactly 1 line through any 2 points
```

This is proven in Coq and used for ring addressing:

```text
slot5040 = fano7 * 720 + role3 * 240 + local240
```

No collisions. No approximation. Perfect addressability.

## 7.4 Constant Derivation

Mathematical constants are derived, not stored:

```text
π = 4 * Σ((-1)^k / (2k+1))  [converges to π exactly]
√2 ≈ convergent series      [computable to arbitrary precision]
φ = (1 + √5) / 2            [exact algebraic number]
e = Σ(1/n!)                 [converges exactly]
```

Proved in Coq:

```coq
Theorem OMI_PI_Equals_Real_PI : OMI_PI = PI.
```

## 7.5 Lazy Evaluation Safety

No side effects before receipt:

```text
(relay . on)  →  declaration (not action)
  ↓
OMI citation (not action)
  ↓
Tetragrammatron validation (not action)
  ↓
ring_store(receipt) (still not action)
  ↓
Metatron projection (now applies action)
```

The hardware rule:

```text
Hardware does not act because syntax appeared.
Hardware acts only after Tetragrammatron writes receipt.
```

This is enforced in metatron.c:

```c
if (receipt_exists(slot) && receipt_accepted(slot)) {
    apply_hardware_action(receipt);
}
```

---

# Part 8: Peer-to-Peer Synchronization

## 8.1 Receipt Exchange

Multiple peers can synchronize state without central authority:

```text
Peer A: receipt ring with N slots
  ↓ exports receipts
  ↓
Peer B: imports receipts
  ↓ validates each receipt locally
  ↓ writes accepted receipts to own ring
  ↓
Both peers now have same accepted receipts
  ↓
Both peers project identical world state
```

Key principle:

```text
Send does not accept.
Receive does not accept.
Validation accepts.
Receipt records.
```

## 8.2 No Global Authority

```text
Peer A REPO.md ≠ Peer B REPO.md

Example:
  Peer A may accept recipe changes
  Peer B may reject recipe changes
  
  Both are correct within their own authority.
  Disagreement triggers investigation, not corruption.
```

## 8.3 Skill Distribution

Peers can share and distribute skills:

```text
Peer A has skill.audio.mix
Peer B requests skill.audio.mix computation

Peer B:
  (peer.request . skill.audio.mix)
  
Peer A:
  (peer.result . mix-result)
  
Peer B validates result:
  - Check against SKILLS.md formula
  - Check test vectors
  - Accept or reject receipt
  
Peer B:
  (receipt.skill . audio.mix)
  (receipt.result . accepted-or-rejected)
```

---

# Part 9: Error Handling

Errors are not exceptions that halt execution. Instead:

```text
Error → cited as rejection → receipt records rejection
```

### Error Receipt

```json
{
  "hash": "error-hash",
  "citation": "malformed-input",
  "error": "citation-error",
  "reason": "invalid frame shape",
  "scope": "fs.o/example",
  "result": "rejected",
  "timestamp": 12345
}
```

### Error Types

```text
citation-error      invalid address frame
validation-error    fails Q(S)=0 or rule
ring-error          ring corruption
width-error         delta16 invoked with wrong width
permission-error    authorization check fails
device-error        hardware device not found
timeout-error       operation exceeds budget
```

Each error has:

```text
Citation (what was attempted)
Reason (why it failed)
Scope (where it failed)
Receipt (recorded in ring)
```

Users and applications inspect error receipts like any other receipt.

---

# Part 10: Unified View

## 10.1 What Each Layer Provides

**Layer 1: Mathematical Foundation (omi_pi_proof.v)**

```text
Provides: Deterministic constants from incidence geometry
Proves: width preservation, exactness, reversibility
Enables: reproducible computation without approximation
```

**Layer 2: OMI-Lisp Protocol**

```text
Provides: notation → citation → validation → receipt → projection
Proves: receipt is shared invariant across all surfaces
Enables: lazy evaluation, pseudo-persistent worlds, deterministic replay
```

**Layer 3: Governance Framework (REPO/AGENTS/SKILLS)**

```text
Provides: role/scope/effect authorization, skill registry, adapter boundaries
Proves: access control, behavior specification, algorithm reproducibility
Enables: collaborative development, auditable decision-making, skill distribution
```

**Layer 4: C Core Implementation**

```text
Provides: four-module authority split (OMI/Tetra/Metatron/IMO)
Proves: separation of concerns, lazy evaluation enforcement, deterministic computation
Enables: secure composition, verifiable execution, finite receipt recording
```

**Layer 5: Viewer Application**

```text
Provides: web-based UI, P2P receipt exchange, multimedia support, developer tools
Proves: multi-surface projection, browser integration, collaborative interface
Enables: user-facing inspection, visual proof, interactive collaboration
```

## 10.2 The Complete Invariant

```text
Code is data               (OMI carries source without execution)
File is port              (carriers move frames across surfaces)
Notation is citation      (readable syntax parsed to OmiInst)
Canon validates           (Tetragrammatron is only validator)
Cosmology projects        (Metatron renders to surfaces)
Receipt accepts           (only validation writes to ring)

Recognition ≠ acceptance    (arrive doesn't mean accept)
Citation ≠ acceptance       (cite doesn't mean accept)
Closure ≠ acceptance        (close doesn't mean accept)
Projection ≠ acceptance     (render doesn't mean accept)

Only validation and receipt accept.
```

## 10.3 From Theory to Practice

```text
omi_pi_proof.v defines what deterministic means
  ↓
OMI-Lisp specifies how determinism is preserved
  ↓
REPO.md + AGENTS.md + SKILLS.md authorize what deterministic code may do
  ↓
C core implements deterministic execution with lazy evaluation
  ↓
Viewer demonstrates deterministic state exchange in P2P world
```

---

# Part 11: The Unified One-Sentence Statement

**OMI is a deterministic receipt-ruler protocol where mathematical constants are derived from incidence geometry (not stored), OMI-Lisp notation is parsed to citations, citations are validated by a finite governor, receipts are recorded in a 5040-slot Fano-addressed ring, and multiple peers can synchronize identical world state through receipt exchange and local validation, all while preserving width-safe, reversible computation and lazy evaluation safety through four-authority separation (OMI citation, Tetragrammatron validation, Metatron projection, IMO carrier), unified governance (REPO/AGENTS/SKILLS), and mathematical proof (Coq) that constants, incidence, and determinism are exact.**

---

# Appendix A: Authority Stack Summary

```text
OMI-Lisp Parse
  ↓
OMI Citation Authority      parse_omi_addr() → OmiInst
  ├─ FNV-1a hashing
  ├─ delta16 / rotl16 / rotr16
  ├─ BQF32 projection
  ├─ Nibble CPU execution
  └─ Instruction witness
  
  ↓
  
Tetragrammatron Validation Authority
  ├─ Shape checking
  ├─ Q(S)=0 validation
  ├─ Fano line incidence
  ├─ Polybius/ququart routing
  ├─ slot5040 computation
  ├─ Ring storage (5040 slots)
  ├─ Ring witnesses (XOR, SUM, ROT)
  ├─ Chirality selection (D+/D-)
  └─ Acceptance/rejection decision
  
  ↓
  
Metatron Projection Authority
  ├─ Receipt reading
  ├─ Smith chart computation
  ├─ Genaille quantization
  ├─ Gnomonic sphere→plane
  ├─ Geometry database lookup
  ├─ Quaternion rotation
  ├─ JSON/OBJ/glTF/SVG rendering
  └─ CSS variable writing
  
  ↓
  
IMO Carrier Authority
  ├─ Receipt export/import
  ├─ S-expression parsing
  ├─ OMI lane compilation
  ├─ Ring file persistence
  ├─ HTTP/SSE/WebSocket
  ├─ BOOT_ROM delivery
  └─ Static file serving
  
  ↓
  
Receipt → Durable Record
```

---

# Appendix B: Key Equations

```text
delta16(x, c) = rotl16(x,1) ⊕ rotl16(x,3) ⊕ rotr16(x,2) ⊕ c

bqf32(x, y) = 60x² + 16xy + 4y²

slot5040(f, r, l) = (f mod 7) * 720 + (r mod 3) * 240 + (l mod 240)
                  = fano7 * 720 + role3 * 240 + local240

OMI_PI = 4 * Σ(k=0 to ∞) [(-1)^k / (2k+1)]

D+ = {0, 5, A, F}  →  XOR = 0, SUM = 30
D- = {3, 6, 9, C}  →  XOR = 0, SUM = 30

Fano(7 points, 7 lines, 3|3|1)  →  slot5040 = 7! = 5040
```

---

# Appendix C: File Authority Order

```text
1. omi_pi_proof.v          (mathematical proofs - Coq)
2. OMI-Lisp Specification  (protocol definition)
3. viewer/docs/REPO.md     (governance authority)
4. viewer/AGENTS.md        (resolver behavior)
5. viewer/SKILLS.md        (algorithm registry)
6. viewer/docs/ADAPTERS.md (carrier boundaries)
7. omi.c                   (citation implementation)
8. tetragrammatron.c       (validation implementation)
9. metatron.c              (projection implementation)
10. imo.c                  (carrier implementation)
11. viewer/index.html      (application UI)
12. receipts.imo           (accepted state)
```

Files higher in the list override files lower in the list.

For example:

```text
viewer/docs/REPO.md rules about what Resolvers may do
  override
viewer/AGENTS.md rules about resolver behavior
  override
omi.c implementation details
```

If there is a conflict, read upward in the list.

---

# Conclusion

The OMI system is complete. It encompasses:

1. **Mathematical foundation** — constants derived from geometry, proven in Coq
2. **Protocol specification** — OMI-Lisp with citation/validation/receipt/projection
3. **Governance framework** — RRBAC with roles, scopes, skills, and effect control
4. **Implementation** — four-authority C core with lazy evaluation and determinism
5. **Application** — viewer with P2P synchronization, multimedia, and developer tools

All five layers integrate around one principle:

```text
Only validation and receipt accept.
Everything else is candidate, carrier, or projection.
```

This completes the OMI system specification.
