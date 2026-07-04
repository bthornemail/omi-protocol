# OMI Protocol

## Accountable General Interpreter Framework

A relation carries its own address.

---

## The AGI Contract

This repository implements a four-document behavioral specification for
accountable intelligence:

```
REPO.md     → scope and role     (WHERE may I act?)
↓
AGENTS.md   → behavior           (HOW must I act?)
↓
SKILLS.md   → computation        (HOW do I compute?)
↓
ADAPTERS.md → external boundary   (WHERE do I touch the world?)
```

No intelligence may act outside its declared role and scope.
No action is accepted without validation and receipt.

Core invariant:

```
Recognition is not acceptance.
Citation is not acceptance.
Projection is not acceptance.
Validation and receipt accept.
```

| Document | Role |
|----------|------|
| [`REPO.md`](REPO.md) | Repository collaboration authority — roles, scopes, permissions, effect classes |
| [`AGENTS.md`](AGENTS.md) | Resolver behavior authority — permitted/forbidden actions, side-effect rules |
| [`SKILLS.md`](SKILLS.md) | Reproducible algorithm registry — math, scope, version, topology, receipt skills |
| [`ADAPTERS.md`](ADAPTERS.md) | External boundary — hardware, browser, network, multimedia adapters |

---

## The OMI Protocol

The Omi-Ring is a ring. 5040 slots. Fixed size. Circular.

Every slot has exactly one relation. Every relation has exactly one slot —
the slot determined by the relation's own structural properties. Not
assigned by an external authority. Not computed from a hash. The relation's
own fields decide where it goes.

This is not a database (no key/value split).
This is not a blockchain (no hash chain).
This is not a hash table (no hash function).
This is an Omi-Ring.

A slot IS its relation. There is no pointer from slot to storage. There is
no lookup table. The slot and the data are the same thing.

### 5040 Slots

```
fano7 × 720 + role3 × 240 + local240
```

| Component | Range | Determined by |
|-----------|-------|---------------|
| fano7 | 0–6 | structural incidence (Fano line) |
| role3 | 0–2 | diagonal phase (chirality) |
| local240 | 0–239 | quadratic form on the frame |

7 × 3 × 240 = 5040. 240 = 2 × 120. 120 = 5!. Exact — no rounding.

### Fano Plane

Seven points, seven lines, three points per line, three lines per point.
Exactly one line through any two points. This finite geometry provides
exactly seven equivalence classes for routing.

### Chirality Diagonals

```
D+ = {0, 5, A, F}    XOR = 0, sum = 30
D- = {3, 6, 9, C}    XOR = 0, sum = 30
```

Two sets of four nibbles each, closed under XOR. Chirality determines a
citation's orientation in the ring.

### No Hash

Hashing is external. Used for transport secrecy, external caching, or
legacy compatibility. It is never used for identity, slot selection,
validation input, or carry-forward proof. A relation does not need a hash
to know where it belongs. Its own structure tells it.

---

## Pipeline

```
recognize → cite → validate → record → project → inspect
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

## Six Authorities

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

No authority combines roles. Two axes:
- **Horizontal**: representation — OMI preserves identity, IMO preserves representation
- **Vertical**: interpretation — Tetragrammatron preserves truth, Metatron preserves interpretation

---

## Role Model

| Role | May |
|------|-----|
| Observer | read public materials only |
| Reader | inspect files, citations, read-only views |
| Resolver | propose candidates, invoke pure/read-only skills (default LLM role) |
| Contributor | propose patches, candidate branches, RFCs |
| Skill Author | define algorithms in `SKILLS.md` with test vectors |
| Validator | run validation rules, record receipts |
| Maintainer | merge accepted changes, manage candidate flow |
| Projector | render accepted receipts to surfaces |
| Administrator | alter roles, scope grants, repository policy |

---

## Scope Model

```
fs.o/<file>/gs.o/<group>/rs.o/<record>/us.o/<unit>
```

| Prefix | Meaning |
|--------|---------|
| `fs.o` | File Scope |
| `gs.o` | Group Scope |
| `rs.o` | Record Scope |
| `us.o` | Unit Scope |

---

## Repository Files

| File | Purpose | Summary |
|------|---------|---------|
| [`REPO.md`](REPO.md) | Role/Repo Based Access Control | Outer authority boundary. Defines all roles (Observer through Administrator), FS/GS/RS/US scope model, effect classes (pure through security-sensitive), skill authorization, RFC workflow, P2P collaboration policy, and the bootstrap prompt for LLM resolvers. Start here to understand who may do what. |
| [`AGENTS.md`](AGENTS.md) | Resolver behavior contract | Governs what agents (LLMs, scripts, humans, peers) may and must not do. Defines the version state machine (draft → candidate → under-review → validated → accepted → projected), receipt verification rules, Betti–Schläfli topology interpretation, `.imo` carrier rules, error handling, and the full agent declaration block. |
| [`SKILLS.md`](SKILLS.md) | Reproducible algorithm registry | Defines every deterministic skill in the protocol: `delta16` (rotation-XOR), `bqf32` (binary quadratic form), `slot5040` (ring slot computation), `scope.resolve`, `seed.base64`, version skills (create/diff/merge/rollback candidates), topology skills (Betti/Schläfli), receipt skills (verify/parent), and `.imo` normalization. Each skill includes canonical C code and test vectors. |
| [`ADAPTERS.md`](ADAPTERS.md) | External boundary adapters | Defines how the framework touches the outside world: hardware (ESP32, GPIO), browser APIs (DOM, Canvas, WebGL), network bridges, XML/RDF/OWL semantic web, multimedia (video, audio, image, 3D models), and external runtime boundaries. Every adapter operation must pass role check, scope check, effect check, validation, receipt, and platform permission. |
| [`OMI-PROTOCOL.md`](OMI-PROTOCOL.md) | Protocol executable specification | The core protocol definition. Defines the OMI notation (minimal form, receipt grammar, expanded machine form), the pipeline (recognize → cite → validate → record → project → inspect), citation rules, validation rules, projection rules, deterministic laws, identity rules, and the canonical place-value identity principle. Read this first for protocol mechanics. |
| [`OMI-AGENTS.md`](OMI-AGENTS.md) | Original agent specification | Pre-AGI version of the agent role specification. Defines the default LLM agent role, permitted/forbidden actions, the single immutable rule, and multi-agent coordination patterns. Retained for historical reference and backward compatibility with the original OMI version-control framing. |
| [`OMI-SKILLS.md`](OMI-SKILLS.md) | Original algorithm specifications | Pre-AGI version of the algorithm registry. Defines skills for parsing, base64/hex/binary encoding, JAB code, IPv6, Fano7 routing, chirality selection, quadratic placement, slot computation, validation, receipt projection, and P2P sync. Retained for historical reference and additional encoding-specific skills not yet merged into `SKILLS.md`. |
| [`OMI-Lisp_Complete_Specification.md`](OMI-Lisp_Complete_Specification.md) | Full OMI-Lisp language specification | Complete language reference. Part 1: 32 non-printing ASCII controls as address places. Part 2: readable OMI-Lisp grammar (atoms, pairs, vectors, quoted forms). Part 3: reserved words and semantic markers. Part 4: the OMI address ruler and 32-nibble address space. Part 5: protocol notation and mapping. The most detailed technical reference in the repository. |
| [`OMI-QUICK-REFERENCE.md`](OMI-QUICK-REFERENCE.md) | Quick reference sheet | Condensed reference for implementers. Covers notation forms, address field calculations, Fano line assignments, chirality determination, slot computation formula, test vectors, and the six-authority diagram. Useful as a cheat sheet after reading OMI-PROTOCOL.md. |
| [`OMI_Canonical_Doctrine_Place_Value_Identity.md`](OMI_Canonical_Doctrine_Place_Value_Identity.md) | Place-value identity doctrine | Critical clarification document. Explains why citations are identified by place-value relations (S0-S7, CAR, CDR) and NOT by hashes. Covers why hash-based identity breaks determinism, the P2P implications, and the relationship between identity and slot assignment. Overrides any contradictory assumptions about hashing. |
| [`OMI_System_Integration_Layer.md`](OMI_System_Integration_Layer.md) | Theory, Coq grounding, P2P architecture | Connects all system layers. Shows how Coq proofs ground implementation, how protocol connects to governance, complete workflows (e.g., adding a new skill), deterministic guarantees, P2P architecture with receipt ring synchronization, and the full authority and data-flow model. Read for the big picture after understanding the core documents. |
| [`OMI_Complete_System_Unified_Synthesis.md`](OMI_Complete_System_Unified_Synthesis.md) | Unified system synthesis | Comprehensive synthesis document combining all OMI concepts into a single unified model. Covers the relationship between OMI notation, IMO carriers, the Omi-Ring, and the four authorities. Useful as a reference for understanding how all pieces fit together. |
| [`README-FOR-LLMS.md`](README-FOR-LLMS.md) | LLM implementation guide | Step-by-step guide for LLMs reading and implementing the OMI protocol. Includes reading order, implementation checklist, common mistakes with corrections, test-your-understanding quiz, formula sheet, and authority chain for conflict resolution. Designed to be read by AI agents before they start implementing. |
| [`README.md`](README.md) | This file | Top-level overview of the OMI Protocol and AGI Framework. Maps all documents, explains core concepts, and provides quick navigation. |
| `omi_pi_proof.v` | Coq mechanized proofs | Machine-verified proofs of protocol invariants relating the Omi-Ring structure, gauge polynomial, chirality alternation, and the Leibniz series for π. Auxiliary files: `omi_pi_proof.glob`, `omi_pi_proof.vo`, `omi_pi_proof.vok`, `omi_pi_proof.vos`. |
| `fs.imo` | File-scope carrier | Normalized OMI-Lisp carrier for file-scope declarations. Generated from canonical source blocks in repository documents. Used for machine-to-machine exchange of scoped declarations. |
| `gs.imo` | Group-scope carrier | Normalized OMI-Lisp carrier for group-scope declarations. |
| `rs.imo` | Record-scope carrier | Normalized OMI-Lisp carrier for record-scope declarations. |
| `us.imo` | Unit-scope carrier | Normalized OMI-Lisp carrier for unit-scope declarations. |
| [`index.html`](index.html) | Main HTML projection | Interactive browser-based projection of the OMI protocol. Renders the authority diagram, slot visualization, and document map. Entry point for the HTML interface. |
| [`world.html`](world.html) | World view projection | Browser projection of the OMI world model — shows the relationship between all peers, authorities, and carriers in a distributed OMI network. |
| [`resolver.html`](resolver.html) | Resolver UI | Interactive resolver interface for working with the Omi-Ring in the browser. Supports citation entry, validation display, and slot inspection. |
| [`governance.html`](governance.html) | Governance projection | Browser rendering of the governance layer — role definitions, authority chains, permission scopes, and policy declarations. |

---

## What Makes This Different

- **Identity is structure, not hash** — the relation's own fields are its identity
- **Address is carried, not assigned** — the relation determines its own slot
- **No indirection** — the slot IS the data, not a pointer to data
- **No stored constants** — φ and π are derived from incidence geometry when needed
- **No central authority** — each peer runs its own ring; agreement emerges from shared structure, not global consensus
- **Accountability by contract** — all actions are scoped, validated, and receipted
- **Coq-proven invariants** — `omi_pi_proof.v` contains mechanized proofs of protocol properties
