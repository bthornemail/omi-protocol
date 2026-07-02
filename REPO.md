# REPO.md

## Accountable General Interpreter — Scope and Role Authority

Version: `0.1.0`  
Status: Draft  
Authority: Repository Collaboration Boundary  
Protocol: OMI-RRBAC  
Applies to: `REPO.md`, `AGENTS.md`, `SKILLS.md`, `ADAPTERS.md`, `.imo` carriers, receipts, LLM resolvers, human contributors, scripts, peers, browser adapters, and projections

Source lineage:

```text
dev-docs/archive/REPO.md - OMI Request for Collaboration and Role-Repo Based Access Control.md
dev-docs/archive/REPO.md Addendum - Bootstrapping an OMI-Compatible LLM Agent.md
dev-docs/archive/REPO.md Addendum - Emergent P2P and Multimedia Use Cases for OMI.md
```

---

## §0. AGI Contract — Scope and Role

`REPO.md` defines the outermost boundary of the
four-file Accountable General Interpreter specification.

```text
REPO.md     → scope and role     (WHERE may I act?)
↓
AGENTS.md   → behavior           (HOW must I act?)
↓
SKILLS.md   → computation        (HOW do I compute?)
↓
ADAPTERS.md → external boundary   (WHERE do I touch the world?)
```

No intelligence may act outside its declared role and scope.
`REPO.md` answers:

```text
who may act
what domain they may act in
what actions are permitted
what actions are forbidden
what effects are allowed
```

Without `REPO.md`, an agent does not know its jurisdiction.
An agent acting outside scope is not accountable —
it is ungoverned.

---

## §0.1. Original Purpose (preserved below)

This file was originally written as the OMI Request for
Collaboration and Role/Repo Based Access Control. That
framing remains valid as one application of the AGI
contract above.

---

## 0. Original Purpose

`REPO.md` answers:

```text
who may collaborate
which roles exist
which scopes are accessible
which actions are allowed
which actions are forbidden
which skills may be invoked
which effects may be projected
which files are authoritative
which receipts are required
how candidate work becomes accepted
```

This file defines **Role/Repo Based Access Control**, abbreviated as **RRBAC**.

RRBAC means:

```text
role controls who or what is acting
repo scope controls where action may occur
skill scope controls what computation may be used
effect class controls what risk the action carries
receipt controls what becomes accepted
```

This repository uses the invariant:

```text
Recognition is not acceptance.
Citation is not acceptance.
Closure is not acceptance.
Projection is not acceptance.
Validation and receipt accept.
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

In this repository:

```text
MUST / REQUIRED / SHALL
  indicates a mandatory rule.

MUST NOT / SHALL NOT
  indicates a forbidden behavior.

SHOULD / RECOMMENDED
  indicates a strong default rule that may be overridden only by explicit receipt.

MAY / OPTIONAL
  indicates permitted behavior that is not required.
```

---

## 2. Authority Order

Repository authority is applied in this order:

```text
REPO.md
  ↓
AGENTS.md
  ↓
SKILLS.md
  ↓
ADAPTERS.md
  ↓
*.imo carriers
  ↓
candidate declarations
  ↓
validation
  ↓
receipt
  ↓
projection
```

`REPO.md` is the highest local collaboration authority for the
OMI protocol repository.

`AGENTS.md` MUST NOT broaden permissions beyond `REPO.md`.

`SKILLS.md` MUST NOT authorize behavior by itself.

`.imo` files MUST be treated as carriers, not authority.

Receipts are the accepted state boundary.

If files conflict:

```text
REPO.md controls role, scope, permission, and effect policy.
AGENTS.md controls resolver behavior inside REPO.md limits.
SKILLS.md controls deterministic computation.
Receipts control accepted state.
```

---

## 3. Repository Authority Split

The repository SHOULD use this authority split:

```text
REPO.md
  role/repo access authority

AGENTS.md
  resolver behavior authority

SKILLS.md
  reproducible algorithm authority

*.imo
  normalized carrier declarations

receipts.imo
  accepted/rejected receipt export

ADAPTERS.md
  adapter and carrier boundary

LICENSE
  legal permission boundary
```

### 3.1 REPO.md

`REPO.md` MUST define:

```text
roles
scopes
permissions
effect classes
collaboration request rules
acceptance requirements
rejection requirements
receipt policy
network collaboration policy
LLM resolver policy
```

### 3.2 AGENTS.md

`AGENTS.md` SHOULD define:

```text
resolver duties
tool-use behavior
side-effect rules
citation rules
error handling
style/canon rules
inspection behavior
```

`AGENTS.md` MUST NOT override `REPO.md`.

### 3.3 SKILLS.md

`SKILLS.md` SHOULD define:

```text
deterministic algorithms
input contracts
output contracts
test vectors
effect classes
canonical source blocks
validation rules
```

`SKILLS.md` MUST NOT accept its own results.

### 3.4 `.imo` Files

`.imo` files MAY carry:

```text
normalized declarations
scope maps
skill references
seed references
receipt exports
carrier hashes
```

`.imo` files MUST NOT create access or acceptance by themselves.

### 3.5 Receipts

Receipts record accepted or rejected validation results.

A repository MUST NOT claim acceptance without a receipt.

---

## 4. Foundational Rule

The repository collaboration rule is:

```text
request does not accept
role does not accept
agent does not accept
skill does not accept
file does not accept
patch does not accept
bridge word does not accept
projection does not accept
model output does not accept

validation and receipt accept
```

The repository MUST preserve this pipeline:

```text
recognize
  ↓
cite
  ↓
validate
  ↓
record
  ↓
project
  ↓
inspect
```

The repository MUST avoid:

```text
render → believe
```

For LLM-assisted work, the repository MUST also avoid:

```text
generate → believe
```

---

## 5. Scope Model

This repository uses FS/GS/RS/US scope.

```text
FS = File Scope
GS = Group Scope
RS = Record Scope
US = Unit Scope
```

The reduced object forms are:

```text
fs.o
gs.o
rs.o
us.o
o.o
```

A scope SHOULD be written as a CIDR-like prefix:

```text
fs.o/<file>
fs.o/<file>/gs.o/<group>
fs.o/<file>/gs.o/<group>/rs.o/<record>
fs.o/<file>/gs.o/<group>/rs.o/<record>/us.o/<unit>
```

Example:

```text
fs.o/SKILLS.md/gs.o/encoding/rs.o/base64/us.o/decode
```

This means:

```text
file scope:   SKILLS.md
group scope:  encoding
record scope: base64
unit scope:   decode
```

### 5.1 Scope Specificity

More specific scopes MAY narrow a broader permission.

A broader permission MUST NOT be assumed to grant all specific sibling scopes unless explicitly declared.

Example:

```text
fs.o/SKILLS.md/gs.o/encoding/rs.o/base64
```

does not imply write access to:

```text
fs.o/SKILLS.md/gs.o/encoding/rs.o/utf8
```

unless explicitly granted.

### 5.2 Scope Isolation

A role granted access to one scope MUST NOT assume access to sibling scopes.

A role granted read access MUST NOT assume write access.

A role granted proposal access MUST NOT assume acceptance authority.

---

## 6. File Authority

The following files have declared repository meaning:

```text
REPO.md         repository collaboration authority
AGENTS.md       resolver behavior authority
SKILLS.md       deterministic algorithm authority
ADAPTERS.md     adapter and carrier boundary
fs.imo          normalized file-scope carrier
gs.imo          normalized group-scope carrier
rs.imo          normalized record-scope carrier
us.imo          normalized unit-scope carrier
receipts.imo    accepted/rejected receipt export
```

A repository MAY add more `.imo` carrier files.

Additional carrier files MUST be scoped by this `REPO.md`.

---

## 7. Role Model

A repository MUST define collaboration roles.

The following roles are canonical defaults.

---

### 7.1 Observer

An Observer MAY read public materials.

An Observer MUST NOT modify files, execute skills, validate receipts, or project side effects.

Default permissions:

```text
read: public
cite: none
propose: none
validate: none
record: none
project: none
execute: none
```

Declaration:

```omi-lisp
(role.Observer.read . fs.o/README.md)
(role.Observer.write . none)
(role.Observer.execute . none)
```

---

### 7.2 Reader

A Reader MAY inspect repository files, declarations, receipts, and projections.

A Reader MUST NOT accept receipts.

A Reader MUST NOT project side effects.

Default permissions:

```text
read: fs.o/**
cite: fs.o/**
propose: none
validate: none
record: none
project: read-only views
execute: read-only skills only
```

Declaration:

```omi-lisp
(role.Reader.read . fs.o/**)
(role.Reader.cite . fs.o/**)
(role.Reader.execute . read-only)
```

---

### 7.3 Resolver

A Resolver MAY interpret requests, select applicable scopes, cite declarations, invoke permitted pure/read-only skills, and propose candidate actions.

An LLM, script, automation, or peer runtime MUST be treated as a Resolver unless explicitly granted another role.

A Resolver MUST distinguish:

```text
recognized
cited
candidate
validated
accepted
projected
rejected
```

A Resolver MUST NOT accept repository state.

Default permissions:

```text
read: fs.o/**
cite: fs.o/**
propose: candidate-only
validate: request-only
record: rejection/validation candidate only
project: candidate previews and accepted read-only views
execute: pure/read-only skills
```

Declaration:

```omi-lisp
(role.Resolver.read . fs.o/**)
(role.Resolver.cite . fs.o/**)
(role.Resolver.write . candidate-only)
(role.Resolver.execute . pure/read-only)
(role.Resolver.must . cite-scope)
(role.Resolver.must.not . side-effect-before-receipt)
```

---

### 7.4 Contributor

A Contributor MAY propose patches, docs, tests, declarations, skill definitions, and `.imo` carrier updates.

A Contributor MUST submit changes as candidates.

A Contributor MUST NOT mark their own contribution as accepted unless also granted Validator or Maintainer authority.

Default permissions:

```text
read: fs.o/**
cite: fs.o/**
propose: candidate branches
validate: local precheck only
record: candidate receipt
project: preview only
execute: pure/read-only/local-test skills
```

Declaration:

```omi-lisp
(role.Contributor.read . fs.o/**)
(role.Contributor.write . candidate-branch)
(role.Contributor.execute . pure/read-only/local-test)
(role.Contributor.must . submit-rfc)
```

---

### 7.5 Skill Author

A Skill Author MAY define or update algorithms in `SKILLS.md`.

A Skill Author MUST provide reproducibility metadata.

A deterministic skill SHOULD include:

```text
name
scope
version
input contract
output contract
effect class
canonical algorithm
test vectors
validation rule
```

A Skill Author MUST NOT claim skill acceptance without receipt.

Declaration:

```omi-lisp
(role.SkillAuthor.write . fs.o/SKILLS.md)
(role.SkillAuthor.must . provide-test-vectors)
(role.SkillAuthor.must . declare-effect-class)
```

---

### 7.6 Agent Author

An Agent Author MAY edit `AGENTS.md`.

An Agent Author MUST preserve `REPO.md` limits.

An Agent Author MUST NOT grant broader permissions than this file allows.

Declaration:

```omi-lisp
(role.AgentAuthor.write . fs.o/AGENTS.md)
(role.AgentAuthor.must . preserve-REPO-limits)
(role.AgentAuthor.must.not . broaden-permissions)
```

---

### 7.7 Validator

A Validator MAY run validation rules and accept or reject candidates.

A Validator MUST record validation results as receipts.

A Validator MUST NOT modify candidate content while validating it except to annotate rejection reasons.

Default permissions:

```text
read: candidate + canonical scopes
validate: declared validation skills
record: receipts
project: validation report
execute: validation skills
```

Declaration:

```omi-lisp
(role.Validator.read . fs.o/**)
(role.Validator.write . receipts.imo)
(role.Validator.execute . validation-skills)
(role.Validator.must . record-result)
```

---

### 7.8 Maintainer

A Maintainer MAY merge accepted changes, edit approved repository scopes, curate branches, and manage candidate flow.

A Maintainer SHOULD NOT bypass validation.

A Maintainer MUST NOT erase receipt history.

Default permissions:

```text
read: fs.o/**
write: approved scopes
validate: may request or run validation
record: merge receipts
project: accepted state
execute: tests and approved local-write skills
```

Declaration:

```omi-lisp
(role.Maintainer.read . fs.o/**)
(role.Maintainer.write . approved-scopes)
(role.Maintainer.execute . tests)
(role.Maintainer.must . preserve-receipts)
```

---

### 7.9 Projector

A Projector MAY render accepted receipts into documentation, UI, geometry, JSON, SVG, DOM, `.imo`, or other projection surfaces.

A Projector MUST NOT treat projection as acceptance.

A Projector MUST label unaccepted projections as previews.

Declaration:

```omi-lisp
(role.Projector.project . accepted-receipts)
(role.Projector.must.not . projection-as-acceptance)
(role.Projector.must . label-candidates)
```

---

### 7.10 Administrator

An Administrator MAY alter roles, scope grants, delegation, revocation, and repository policy.

An Administrator SHOULD record role changes as receipts.

An Administrator MUST NOT erase receipt history except under explicit archival, privacy, or security policy.

Declaration:

```omi-lisp
(role.Administrator.write . REPO.md)
(role.Administrator.manage . roles/scopes/delegation)
(role.Administrator.must . preserve-receipt-history)
```

---

## 8. Access Classes

Repository actions SHOULD be classified using the following access classes.

### 8.1 Read

Read permits inspection.

Read MUST NOT imply cite, write, execute, validate, record, or project.

### 8.2 Cite

Cite permits creation of a citation candidate.

Cite MUST NOT imply acceptance.

### 8.3 Propose

Propose permits submission of candidate changes.

Candidates MUST remain candidates until validated and receipted.

### 8.4 Validate

Validate permits checking a candidate.

Validation MUST produce an accepted or rejected validation result.

### 8.5 Record

Record permits writing receipts.

Record access MUST be restricted.

### 8.6 Project

Project permits rendering accepted receipts or labeled previews.

Project MUST NOT imply acceptance.

### 8.7 Execute

Execute permits running a skill or tool.

Execution MUST be bounded by effect class.

### 8.8 Side Effect

Side effects include:

```text
file write
git commit
git push
network send
email send
package publish
hardware actuation
credential use
deployment
destructive operation
```

Side effects MUST require explicit role, scope, effect permission, and accepted receipt.

---

## 9. Effect Classes

Every skill or action SHOULD declare an effect class.

The canonical effect classes are:

```text
pure
read-only
local-write
repo-write
network
hardware
security-sensitive
```

### 9.1 Pure

A pure skill reads declared inputs and produces output without side effects.

Pure skills MUST NOT depend on randomness, wall-clock time, hidden state, credentials, network, or hardware.

### 9.2 Read-Only

A read-only skill may inspect repository files or metadata.

Read-only skills MUST NOT modify state.

### 9.3 Local-Write

A local-write skill may write temporary build outputs or generated artifacts.

Local-write skills SHOULD be restricted to declared paths.

### 9.4 Repo-Write

A repo-write skill may modify tracked repository files.

Repo-write skills MUST require role authorization and validation.

### 9.5 Network

A network skill may send or receive data across network boundaries.

Network skills MUST be explicitly authorized.

### 9.6 Hardware

A hardware skill may actuate a physical device.

Hardware skills MUST require accepted receipt before projection.

### 9.7 Security-Sensitive

Security-sensitive actions include credentials, secrets, signing keys, deployment systems, protected infrastructure, and package publishing.

Security-sensitive skills MUST require explicit Administrator or delegated authority.

---

## 10. Skill Authorization

A role MAY invoke only skills allowed by this `REPO.md` and constrained by `AGENTS.md`.

A skill declaration SHOULD include:

```omi-lisp
(skill.name . skill.example)
(skill.scope . fs.o/SKILLS.md/gs.o/example/rs.o/algorithm/us.o/v1)
(skill.effect . pure)
(skill.input . declared-input-type)
(skill.output . declared-output-type)
(skill.validation . required-test-vector)
```

A role grant SHOULD include:

```omi-lisp
(role.Resolver.allowed-skill . skill.example)
(role.Resolver.scope . fs.o/SKILLS.md/gs.o/example)
(role.Resolver.effect-limit . read-only)
```

A repository MUST reject skill execution when:

```text
role is not authorized
scope does not match
effect class exceeds role grant
required validation is missing
carrier is malformed
skill version is unaccepted
```

---

## 11. Default Skill Grants

The default Resolver MAY invoke:

```omi-lisp
(role.Resolver.allowed-skill . skill.scope.resolve)
(role.Resolver.allowed-skill . skill.imo.normalize)
(role.Resolver.allowed-skill . skill.receipt.verify)
(role.Resolver.allowed-skill . skill.receipt.parent)
(role.Resolver.allowed-skill . skill.version.create-candidate)
(role.Resolver.allowed-skill . skill.version.diff)
(role.Resolver.allowed-skill . skill.version.merge-candidate)
(role.Resolver.allowed-skill . skill.version.rollback-candidate)
(role.Resolver.allowed-skill . skill.version.validate-candidate)
(role.Resolver.allowed-skill . skill.topology.betti)
(role.Resolver.allowed-skill . skill.topology.schlafli)
(role.Resolver.allowed-skill . skill.delta16)
(role.Resolver.allowed-skill . skill.bqf32)
(role.Resolver.allowed-skill . skill.slot5040)
```

The default Resolver MUST NOT invoke:

```omi-lisp
(role.Resolver.denied-skill . skill.hardware.project)
(role.Resolver.denied-skill . skill.network.send)
(role.Resolver.denied-skill . skill.credential.read)
(role.Resolver.denied-skill . skill.package.publish)
(role.Resolver.denied-skill . skill.deploy.production)
(role.Resolver.denied-skill . skill.destructive.delete)
```

Denied skills MAY be enabled only by explicit role grant and receipt.

---

## 12. Request for Collaboration

A Request for Collaboration, abbreviated here as RFC, is a scoped request to propose, validate, project, or maintain a repository relation.

This RFC is repository-local. It is not an internet standards RFC.

### 12.1 RFC States

An RFC SHOULD move through:

```text
draft
  ↓
candidate
  ↓
under-review
  ↓
validated
  ↓
accepted
  ↓
projected
```

A rejected RFC SHOULD move through:

```text
candidate
  ↓
under-review
  ↓
rejected
  ↓
rejection receipt
```

### 12.2 Required RFC Fields

An RFC MUST include:

```text
id
title
requester
role requested
scope requested
effect class
files touched
skills invoked
validation required
acceptance criteria
rollback or rejection rule
```

### 12.3 RFC Declaration

Example:

```omi-lisp
(rfc.id . rfc-0001)
(rfc.title . "Add Base64 seed object")
(rfc.requester . contributor.local)
(rfc.role.requested . SkillAuthor)
(rfc.scope . fs.o/SKILLS.md/gs.o/encoding/rs.o/base64)
(rfc.effect . pure)
(rfc.files . "(SKILLS.md fs.imo)")
(rfc.skill . skill.seed.base64)
(rfc.validation . test.base64.seed)
(rfc.status . candidate)
```

### 12.4 RFC Acceptance

An RFC MUST NOT be accepted unless:

```text
requested role is valid
requested scope is valid
effect class is permitted
required skills exist
required tests pass
required validations pass
receipt is recorded
```

---

## 13. Version Control Authority

A version is not merely a file state.

A version is an accepted scoped relation over a carrier file.

A version SHOULD include:

```text
version id
parent receipt
scope prefix
seed object
citation route
carrier file
declaration hash
algorithm hash
validation rule
effect class
receipt handle
projection signature
logical cycle
```

Example:

```omi-lisp
(version.id . receipt-2820)
(version.parent . receipt-2819)
(version.scope . fs.o/SKILLS.md/gs.o/encoding/rs.o/base64/us.o/schema)
(version.seed . o.base64.o)
(version.route . omi-base-64-imo)
(version.carrier . SKILLS.md)
(version.algorithm . skill.seed.base64)
(version.effect . pure)
(version.result . accepted)
```

A repository MUST distinguish:

```text
file diff
relation delta
algorithm delta
scope delta
receipt delta
projection delta
```

A commit MAY carry many candidates.

Only accepted receipts define accepted version state.

---

## 14. Seed Object Policy

A repository MAY define seed objects such as:

```text
o.o
o.base64.o
o.hex.o
o.binary.o
o.utf8.o
o.jabcode.o
o.ipv6.o
```

The neutral seed is:

```text
o.o
```

A scoped seed such as:

```text
o.base64.o
```

SHOULD mean:

```text
Base64-scoped neutral seed object
```

A seed object MUST NOT imply payload acceptance.

A seed object declares interpretation scope.

A payload instance MUST still be validated.

Example:

```omi-lisp
(seed.base64 . o.base64.o)
(seed.base64.route . omi-base-64-imo)
(seed.base64.effect . pure)
(seed.base64.status . declared)
```

---

## 15. `.imo` Carrier Policy

`.imo` files SHOULD be generated from accepted or candidate OMI-Lisp declarations.

`.imo` files MUST be treated as carriers, not authorities.

A `.imo` file MAY contain:

```text
normalized declarations
scope maps
skill references
seed references
receipt exports
carrier hashes
```

A `.imo` file MUST NOT create access by itself.

Access is granted only by `REPO.md` policy and accepted receipts.

---

## 16. Receipt Policy

A receipt MUST record the accepted or rejected result of validation.

A receipt SHOULD include:

```text
receipt id
parent receipt
scope
role
requester
skill
effect class
input hash
output hash
validation result
logical cycle
result
```

A rejection receipt SHOULD include a reason.

Example:

```omi-lisp
(receipt.id . receipt-2820)
(receipt.scope . fs.o/SKILLS.md/gs.o/encoding/rs.o/base64)
(receipt.role . SkillAuthor)
(receipt.skill . skill.seed.base64)
(receipt.effect . pure)
(receipt.result . accepted)
```

A repository MUST NOT claim acceptance without a receipt.

---

## 17. Conflict Policy

A conflict occurs when two or more candidates attempt to modify incompatible relations in the same scope.

Conflicts SHOULD be classified as:

```text
text conflict
scope conflict
role conflict
skill conflict
algorithm conflict
receipt conflict
topology conflict
effect conflict
projection conflict
```

A conflict MUST be resolved before acceptance.

A merge MUST produce a receipt.

If topology signatures are supported, a conflict MAY include Betti information:

```text
b0 = connected components
b1 = loops / cycles
b2 = enclosed unresolved voids
```

Example:

```omi-lisp
(conflict.scope . fs.o/SKILLS.md/gs.o/encoding/rs.o/base64)
(conflict.kind . algorithm-conflict)
(conflict.betti . "(1 1 0)")
(conflict.result . rejected)
```

---

## 18. Delegation Policy

A role MAY delegate authority only if this `REPO.md` permits delegation.

Delegation MUST be narrower than or equal to the delegator's authority.

Delegation MUST specify:

```text
delegate
role
scope
effect class
duration or expiration
revocation rule
```

Example:

```omi-lisp
(delegate.from . Maintainer)
(delegate.to . Contributor)
(delegate.scope . fs.o/SKILLS.md/gs.o/docs)
(delegate.effect . repo-write)
(delegate.expires . receipt-cycle-3000)
```

Delegation SHOULD be recorded as a receipt.

---

## 19. Revocation Policy

A repository MUST support revocation of role grants.

Revocation MUST NOT erase historical receipts.

Revocation SHOULD affect future actions only.

Example:

```omi-lisp
(revoke.role . Contributor)
(revoke.scope . fs.o/SKILLS.md/gs.o/encoding)
(revoke.reason . superseded-permission)
```

---

## 20. LLM Resolver Policy

An LLM acting in this repository MUST be treated as a Resolver unless explicitly granted another role.

An LLM Resolver MUST:

```text
read REPO.md before assuming access
obey AGENTS.md within REPO.md limits
use SKILLS.md for reproducible algorithms
distinguish candidate from accepted
avoid side effects before receipt
cite scope when proposing changes
state uncertainty when validation has not run
```

An LLM Resolver MUST NOT:

```text
invent authority
claim acceptance without validation
claim tests passed without running or receiving results
execute side effects from generated text
treat projection as truth
broaden its own role
ignore scope restrictions
```

An LLM MAY propose:

```text
patches
explanations
tests
declarations
candidate receipts
documentation
scope maps
```

if its role allows.

---

## 21. Network Collaboration Policy

A repository MAY receive collaboration requests from peers.

Peer-provided declarations MUST be treated as candidates.

Peer-provided `.imo` files MUST be validated locally.

Peer-provided receipts MAY be inspected, but MUST NOT be accepted blindly.

A receiving repository MUST validate under local `REPO.md`.

Network rule:

```text
send does not accept
receive does not accept
validation accepts
receipt records
```

---

## 22. Security Policy

A compliant repository MUST protect:

```text
credentials
signing keys
deployment tokens
network write paths
hardware actuation paths
package publishing paths
receipt history
role grants
```

A repository MUST NOT allow an LLM Resolver to access secrets unless explicitly granted by role and scope.

A repository MUST NOT allow candidate declarations to trigger network, file, hardware, credential, deployment, or package-publish side effects before receipt.

A repository SHOULD record rejected security-sensitive attempts as rejection receipts.

---

## 23. Projection Policy

Projection is display or operational output derived from a candidate or accepted receipt.

Projection surfaces MAY include:

```text
markdown
HTML
DOM
CSSOM
JSDOM
JSON
SVG
WebGL
hardware
network
git branch
package output
documentation
```

A projection MUST be labeled as one of:

```text
candidate-preview
accepted-projection
rejected-projection
inspection-only
```

A projection MUST NOT be treated as acceptance.

Hardware, network, deployment, and package-publish projections MUST require accepted receipt and explicit effect permission.

---

## 24. Minimal Machine-Readable Declaration Block

A compliant repository SHOULD include this declaration block or an equivalent one:

```omi-lisp
(repo.name . "omi-protocol")
(repo.protocol . "OMI-RRBAC")
(repo.version . "0.1.0")
(repo.authority . REPO.md)

(repo.scope.root . fs.o)
(repo.scope.group . gs.o)
(repo.scope.record . rs.o)
(repo.scope.unit . us.o)

(repo.file.repo . REPO.md)
(repo.file.agents . AGENTS.md)
(repo.file.skills . SKILLS.md)
(repo.file.receipts . receipts.imo)

(role.default . Observer)
(role.llm.default . Resolver)

(policy.acceptance . receipt-required)
(policy.side-effect . forbidden-before-receipt)
(policy.projection . accepted-receipts-only)
(policy.conflict . receipt-required)
(policy.network . validate-locally)
```

---

## 25. Example Role Grants

```omi-lisp
(role.Observer.read . fs.o/README.md)
(role.Observer.write . none)
(role.Observer.execute . none)

(role.Reader.read . fs.o/**)
(role.Reader.cite . fs.o/**)
(role.Reader.execute . read-only)

(role.Resolver.read . fs.o/**)
(role.Resolver.cite . fs.o/**)
(role.Resolver.write . candidate-only)
(role.Resolver.execute . pure/read-only)
(role.Resolver.must . cite-scope)

(role.Contributor.read . fs.o/**)
(role.Contributor.write . candidate-branch)
(role.Contributor.execute . pure/read-only/local-test)
(role.Contributor.must . submit-rfc)

(role.Validator.read . fs.o/**)
(role.Validator.write . receipts.imo)
(role.Validator.execute . validation-skills)
(role.Validator.must . record-result)

(role.Maintainer.read . fs.o/**)
(role.Maintainer.write . approved-scopes)
(role.Maintainer.execute . tests)
(role.Maintainer.must . preserve-receipts)
```

---

## 26. Example Skill Grants

```omi-lisp
(skill.allowed . skill.scope.resolve)
(skill.allowed.scope . fs.o/SKILLS.md/gs.o/scope)
(skill.allowed.effect . pure)

(skill.allowed . skill.imo.normalize)
(skill.allowed.scope . fs.o/SKILLS.md/gs.o/imo)
(skill.allowed.effect . pure)

(skill.allowed . skill.receipt.verify)
(skill.allowed.scope . fs.o/SKILLS.md/gs.o/receipt)
(skill.allowed.effect . pure)

(skill.allowed . skill.seed.base64)
(skill.allowed.scope . fs.o/SKILLS.md/gs.o/encoding/rs.o/base64)
(skill.allowed.effect . pure)

(skill.denied . skill.hardware.project)
(skill.denied.reason . requires-explicit-hardware-receipt)
```

---

## 27. Example Collaboration Request

```omi-lisp
(rfc.id . rfc-0002)
(rfc.title . "Add Base64 seed object")
(rfc.requester . contributor.local)
(rfc.role.requested . SkillAuthor)
(rfc.scope . fs.o/SKILLS.md/gs.o/encoding/rs.o/base64)
(rfc.effect . pure)
(rfc.files . "(SKILLS.md fs.imo)")
(rfc.skill . skill.seed.base64)
(rfc.validation . test.base64.seed)
(rfc.status . candidate)
```

Accepted receipt:

```omi-lisp
(receipt.accepts . rfc-0002)
(receipt.scope . fs.o/SKILLS.md/gs.o/encoding/rs.o/base64)
(receipt.effect . pure)
(receipt.result . accepted)
```

Rejected receipt:

```omi-lisp
(receipt.rejects . rfc-0002)
(receipt.scope . fs.o/SKILLS.md/gs.o/encoding/rs.o/base64)
(receipt.reason . "missing test vectors")
(receipt.result . rejected)
```

---

## 28. Compliance Levels

A repository MAY declare a compliance level.

### Level 0: Informational

Repository has `REPO.md`, but no machine-readable declarations.

### Level 1: Declarative

Repository defines roles, scopes, and policies in `REPO.md`.

### Level 2: Scoped

Repository uses FS/GS/RS/US scope prefixes.

### Level 3: Skill-Gated

Repository integrates `SKILLS.md` skill declarations.

### Level 4: Receipt-Gated

Repository requires receipts for accepted changes.

### Level 5: Network-Collaborative

Repository can receive peer declarations and validate locally.

### Level 6: Side-Effect Safe

Repository enforces receipt-before-side-effect for tools, hardware, deployment, network, and package publishing.

Declaration:

```omi-lisp
(repo.compliance . OMI-RRBAC-Level-4)
```

---

## 29. Required Repository Invariants

This repository MUST preserve:

```text
Recognition is not acceptance.
Citation is not acceptance.
Closure is not acceptance.
Projection is not acceptance.
Validation and receipt accept.
```

This repository MUST preserve:

```text
gauge_process() witnesses.
bridge_resolve() stages.
parse_form() constructs.
validate_candidate() judges.
receipt_store() accepts.
projection_allowed() gates side effects.
hardware_project() acts only after accepted hardware receipt.
```

---

## 30. Final Canon

`REPO.md` is the repository collaboration authority.

`AGENTS.md` is the resolver behavior authority.

`SKILLS.md` is the reproducible algorithm authority.

`ADAPTERS.md` is the adapter and carrier boundary.

`.imo` files are normalized carriers.

Receipts are accepted authority.

Roles define who may act.

Scopes define where action may occur.

Skills define how computation is performed.

Effects define what risk the action carries.

Validation defines whether a candidate is accepted or rejected.

No actor, agent, model, file, skill, bridge word, branch, projection, peer, or source block may accept repository state without receipt.

---

## 31. One-Sentence Summary

`REPO.md` defines Role/Repo Based Access Control for the OMI protocol repository by binding roles, scopes, skills, effects, files, `.imo` carriers, collaboration requests, local validation, and receipt gates into a protocol where every contribution remains a candidate until validated and recorded as an accepted or rejected receipt.

---

## 32. LLM Bootstrap

An LLM agent operating in this repository is a scoped Resolver by default.

It may:

```text
inspect repository files
explain authority boundaries
cite candidate relations
prepare candidate declarations
prepare candidate patches
invoke permitted pure/read-only skills
summarize receipts and projections
```

It must not:

```text
claim acceptance without receipt
perform side effects before receipt
broaden its own role
override REPO.md
interpret rendering as truth
interpret parser success as truth
interpret model output as truth
```

### 32.1 Bootstrap Prompt

Use this prompt when starting an LLM session:

```text
You are operating inside the OMI protocol repository.

Your default role is Resolver.

Read and obey local authority in this order:

1. REPO.md
2. AGENTS.md
3. SKILLS.md
4. ADAPTERS.md
5. *.imo carrier files, if present
6. source files
7. tests and receipts

Authority split:

REPO.md defines who/where/what is allowed.
AGENTS.md defines resolver behavior.
SKILLS.md defines reproducible algorithms.
ADAPTERS.md defines carrier and adapter boundaries.
*.imo files are normalized carriers.
Receipts define accepted state.

You may recognize, cite, inspect, explain, propose, and generate candidates.

You must not claim acceptance without validation and receipt.

You must not treat generated code, rendered output, parser success, bridge
words, source blocks, network input, browser events, or projections as accepted
authority.

Core invariant:

Recognition is not acceptance.
Citation is not acceptance.
Closure is not acceptance.
Projection is not acceptance.
Validation and receipt accept.

For repository work, use this pipeline:

prompt
  -> scope
  -> read authority files
  -> select permitted skills
  -> generate candidate
  -> validate or request validation
  -> record receipt only if allowed
  -> project only if allowed

Always label work as candidate unless a valid receipt proves acceptance.
```

### 32.2 Short Bootstrap Prompt

```text
Act as an OMI Resolver.

Read authority in this order: REPO.md, AGENTS.md,
SKILLS.md, ADAPTERS.md, *.imo, source, tests, receipts.

You may propose candidates, inspect files, explain bit operations, and prepare
patches.

You must not claim acceptance without validation and receipt.

Do not treat rendering, parsing, source text, bridge words, browser events,
network input, or model output as acceptance.

Always label work as candidate unless a receipt proves acceptance.
```

### 32.3 Work Template

```text
Use the OMI Resolver rules from REPO.md.

Task:
<describe task>

Relevant files:
<list files>

Required scope:
fs.o/<file>/gs.o/<group>/rs.o/<record>/us.o/<unit>

Allowed effect:
pure | read-only | local-write | repo-write | network | hardware | security-sensitive

Validation required:
<tests, build command, lint command, parser check, receipt check>

Output required:
- what was inspected
- scope affected
- candidate change
- validation required
- risks
- acceptance status
```

Default acceptance status:

```text
candidate, not accepted
```

### 32.4 Candidate Declaration Block

For proposed changes:

```omi-lisp
(change.status . candidate)
(change.scope . fs.o/<file>/gs.o/<group>/rs.o/<record>/us.o/<unit>)
(change.effect . pure)
(change.skill . skill.<name>)
(change.validation . required)
(change.result . not-accepted)
```

For accepted changes:

```omi-lisp
(receipt.accepts . change-id)
(receipt.scope . fs.o/<file>/gs.o/<group>/rs.o/<record>/us.o/<unit>)
(receipt.effect . pure)
(receipt.result . accepted)
```

A candidate block MUST NOT be treated as an accepted receipt.

---

## 33. P2P Authority

The repository may display, exchange, or prepare peer-to-peer carriers, candidates,
and receipts.

It does not exchange truth.

Canonical P2P rule:

```text
Peers exchange carriers, candidates, and receipts.
Peers do not exchange truth.

Each peer validates locally.
Each peer accepts locally.
Each peer records its own receipts.
```

Network invariant:

```text
Send does not accept.
Receive does not accept.
Validation accepts.
Receipt records.
```

P2P authority split:

```text
REPO.md     -> role/repo authority
AGENTS.md   -> resolver behavior
SKILLS.md   -> algorithm registry
*.imo       -> normalized carriers
receipts    -> accepted/rejected state
```

P2P consequence:

```text
No peer is global authority.
No sender is automatically trusted.
No received carrier is automatically accepted.
Every peer validates under its own REPO.md.
```

### 33.1 Repository Federation

Peer A may send a candidate declaration to Peer B.

Peer B validates the candidate under Peer B's own authority files.

```omi-lisp
(peer.send . candidate)
(peer.receive . candidate)
(peer.validate . local-REPO-md)
(peer.receipt . store-if-accepted)
```

Peer B may accept a candidate in:

```text
fs.o/docs
```

while rejecting the same peer's candidate in:

```text
fs.o/src
```

### 33.2 Distributed Skill Execution

A peer may request computation from another peer that has a special runtime,
model, hardware device, dataset, or validated skill.

```omi-lisp
(peer.request . skill.audio.mix)
(peer.scope . fs.o/SKILLS.md/gs.o/audio/rs.o/mixer/us.o/v1)
(peer.effect . pure)
(peer.receipt . required)
```

For hardware or external effects:

```omi-lisp
(peer.request . skill.hardware.project)
(peer.scope . fs.o/hardware/gs.o/gpio)
(peer.effect . hardware)
(peer.receipt . required-before-projection)
```

Hardware command declarations and hardware result declarations are different
relations.

### 33.3 Remote Receipt Verification

A remote receipt proves what the remote peer accepted.

It does not prove what the local peer accepts.

Verification SHOULD check:

```text
receipt exists
receipt hash is nonzero
receipt scope matches claim
receipt result is accepted or rejected
receipt parent is consistent when parent is declared
algorithm hash matches declared skill
effect class matches declared permission
```

Local acceptance requires local validation.

### 33.4 Receipt Ring Synchronization

The receipt ring is a local ordering and storage structure, not global
consensus by itself.

Slot rule:

```text
slot5040 = fano7 * 720 + role3 * 240 + local240
```

where:

```text
fano7 in 0..6
role3 in 0..2
local240 in 0..239
```

P2P agreement emerges only when peers compare, validate, and accept compatible
receipt chains.

Preferred term:

```text
receipt ring synchronization
```

not:

```text
receipt ring consensus
```

---

## 34. Multimedia Policy

The repository may carry or project multimedia.

Media is carrier material, not authority.

```text
video is a carrier
audio is a carrier
image is a carrier
3D model is a carrier
texture is a carrier
thumbnail is a carrier
```

A media file is not accepted merely because it renders.

Media must still pass validation and receipt.

### 34.1 Multimedia Seeds

Media interpretation can be declared with seed objects:

```omi-lisp
(seed.video . o.video.o)
(seed.audio . o.audio.o)
(seed.image . o.image.o)
(seed.model3d . o.model3d.o)
```

Seed rule:

```text
A seed declares interpretation scope.
A seed does not imply payload acceptance.
```

### 34.2 Multimedia Effect Classes

The base effect classes remain:

```text
pure
read-only
local-write
repo-write
network
hardware
security-sensitive
```

Multimedia operations SHOULD initially be represented as declared effect
subtypes rather than new core enum values.

```omi-lisp
(effect . network)
(effect.subtype . streaming)

(effect . hardware)
(effect.subtype . render)

(effect . local-write)
(effect.subtype . transcode)

(effect . security-sensitive)
(effect.subtype . capture)
```

Media effect mapping:

```text
streaming  -> network
rendering  -> hardware
transcode  -> local-write
capture    -> security-sensitive
inference  -> hardware or security-sensitive, depending on model/data
```

### 34.3 Multimedia Bridge Policy

The base external bridge is:

```text
0xAA55 = external bridge authorization
```

Media-specific bridge words MAY be introduced later, but they SHOULD be treated
as extension words, not required core protocol.

Candidate extension words:

```c
#define OMI_BRIDGE_MEDIA_STREAM     0x1A55u
#define OMI_BRIDGE_MEDIA_RENDER     0x1B55u
#define OMI_BRIDGE_MEDIA_CAPTURE    0x1C55u
#define OMI_BRIDGE_MEDIA_TRANSCODE  0x1D55u
```

Extension rule:

```text
Media bridge words MUST NOT bypass effect permission.
Media bridge words MUST NOT bypass validation.
Media bridge words MUST NOT write receipts directly.
```

### 34.4 P2P Media Matrix

| Use Case | OMI Feature | P2P Attribute |
|---|---|---|
| Repository federation | `REPO.md` per peer | local sovereignty |
| Distributed skills | effect classes + skills | zero-trust execution |
| Receipt verification | `skill.receipt.verify` | provenance checking |
| Decentralized version control | FS/GS/RS/US scopes | scoped histories |
| Skill exchange | `SKILLS.md` + test vectors | deterministic execution |
| Receipt synchronization | 5040-slot ring + cycles | local receipt lineage |
| Conflict resolution | Betti + Schläfli | topology-assisted inspection |
| Video streaming | chunk receipts | validated media delivery |
| Collaborative editing | scoped edit candidates | mergeable media histories |
| 3D sharing | WebGL projection | receipt-gated rendering |
| Audio production | pure media skills | deterministic mixing |
| Media inference | effect-gated models | validated AI output |
| Media discovery | metadata carriers | read-only search |

### 34.5 Canonical P2P And Multimedia Invariant

```text
Recognition is not acceptance.
Citation is not acceptance.
Closure is not acceptance.
Projection is not acceptance.
Validation and receipt accept.

Send does not accept.
Receive does not accept.
Validation accepts.
Receipt records.

Media is not acceptance.
A video is a carrier.
An audio file is a carrier.
An image is a carrier.
A 3D model is a carrier.
A texture is a carrier.
A thumbnail is a carrier.

Streaming is a network effect.
Rendering is a hardware effect.
Transcoding is a local-write effect.
Capture is a security-sensitive effect.
Inference is an effect-gated candidate process.

Each effect requires explicit authorization.
Each peer validates under its own REPO.md.
Each peer stores its own receipts.
Peers exchange carriers, candidates, and receipts, not truth.
```
