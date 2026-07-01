# OMI QUICK REFERENCE

## For LLMs Implementing OMI Protocol

---

## THE PIPELINE (Memorize This)

```
1. recognize (input arrives)
2. cite (OMI parses to OmiInst)
3. validate (Tetragrammatron checks rule)
4. record (receipt written to ring[slot])
5. project (Metatron renders to surface)
6. inspect (user sees result)
```

NO SKIPPING. NO REVERSING.

---

## THE AUTHORITY RULE (Memorize This)

```
Recognition ≠ acceptance
Citation ≠ acceptance
Closure ≠ acceptance
Projection ≠ acceptance

ONLY validation + receipt = acceptance
```

You cannot accept. Only Tetragrammatron accepts.

---

## THE IDENTITY RULE (CANONICAL)

```
CORRECT:   citation_identity = S0, S1, S2, S3, S4, S5, S6, S7, CAR, CDR
WRONG:     citation_identity = hash(citation)

Place-value relations are identity.
Hashes are external/carrier use only.
```

---

## THE NOTATION

```
Minimal:   omi---imo

Grammar:   o---o/---/?---?@---@

Expanded:  o-S0-S1-S2-S3/S4/S5/S6/S7?PAYLOAD?MASK@CAR@CDR

Readable:  (a . b)    meaning: a on CAR side, b on CDR side
```

---

## CORE ALGORITHMS

### delta16(x, c)
```
rotl16(x,1) ⊕ rotl16(x,3) ⊕ rotr16(x,2) ⊕ c
= rotation-XOR law
= 16-bit → 16-bit (width-preserving)
```

### bqf32(x, y)
```
60x² + 16xy + 4y²
= binary quadratic form
= 32-bit + 32-bit → 32-bit
```

### slot5040(f, r, l)
```
(f mod 7)*720 + (r mod 3)*240 + (l mod 240)
= ring address
= [0, 5040) = 7!
= 7 × 3 × 240 (exact Fano decomposition)
```

### validate(inst, rule)
```
test_rule(inst) → true | false
= deterministic always
= same input → same output always
```

### hash_fnv1a(data)
```
FNV-1a hash algorithm
= CARRIER USE ONLY
= NOT PROTOCOL IDENTITY
= Mark as CARRIER_METADATA_ONLY
```

---

## YOUR ROLE (LLM/Implementer)

### MAY DO
✓ Parse notation to OmiInst  
✓ Invoke skills from SKILLS.md  
✓ Validate using deterministic rules  
✓ Compute slot5040  
✓ Record receipts to ring  
✓ Project receipts to surfaces  
✓ Exchange receipts P2P  
✓ Validate received receipts locally  

### MUST NOT DO
✗ Use hash for protocol identity  
✗ Skip validation  
✗ Side-effect before receipt  
✗ Claim validation authority  
✗ Modify receipts after recording  
✗ Assume send = accept  
✗ Use hash outside carrier layer  
✗ Claim you are Tetragrammatron  

---

## QUICK TEST (Answer without looking back)

1. Is hash_fnv1a used for protocol identity?
   → **NO** (carrier use only)

2. Who validates citations?
   → **Tetragrammatron** (not you)

3. When does hardware actuate?
   → **After ring recording AND projection** (not before)

4. What is citation identity?
   → **Place-value relation** (S0-S7, CAR, CDR), not hash

5. Can you modify receipts?
   → **NO** (immutable)

Get all 5? You're ready.

---

## THE COVARIANT RULE

```
Code is data.
File is port.
Notation is citation.
Canon validates.
Cosmology projects.
Receipt accepts.
```

This governs everything.

---

## LAZY EVALUATION (Why It Matters)

```
(relay . on)  ← stage 1: declare
   ↓ cite    ← stage 2: no effect yet
   ↓ validate ← stage 3: no effect yet
   ↓ ring_store ← stage 4: no effect yet
   ↓ metatron_project ← stage 5: NOW effect occurs
   ↓ hardware_actuate ← stage 6: physical world changes
```

**Why**: If validation fails, no side effect. If validation passes, effect is recorded before execution.

---

## P2P SYNC (How It Works)

```
Peer A: receipts in ring
  ↓ export
Peer B: receive receipts
  ↓ validate locally (re-run Tetragrammatron)
  ↓ if valid: ring_store(receipt)
     if not valid: reject
Both peers now have identical accepted receipts
Both peers project identical state
```

**Key**: Send ≠ accept. Only validation accepts.

---

## IMPLEMENTATION ORDER

1. **Implement skills** (delta16, bqf32, rotl16, rotr16, slot5040_compute)
2. **Implement parser** (text → OmiInst)
3. **Implement ring** (array[5040])
4. **Implement validator** (deterministic rule check)
5. **Implement projector** (receipt → JSON/SVG/etc.)
6. **Implement P2P** (receipt exchange + local validation)
7. **Test everything** (end-to-end workflow)

---

## VERIFICATION CHECKLIST

- [ ] All test vectors pass (SKILLS.md)
- [ ] Determinism verified (same input → same output always)
- [ ] Place-value identity used (not hash)
- [ ] Lazy evaluation enforced (no effect before receipt)
- [ ] Validation is deterministic
- [ ] Ring is append-only to slots
- [ ] Receipts are immutable
- [ ] Projection reads only validated receipts
- [ ] P2P validates locally
- [ ] No hash used in protocol identity
- [ ] No side-effects before receipt
- [ ] No authorization claimed

Check all 12? Implementation is correct.

---

## FORMULA SHEET (Quick Lookup)

```
delta16(x,c) = rotl(x,1)⊕rotl(x,3)⊕rotr(x,2)⊕c
bqf32(x,y) = 60x²+16xy+4y²
slot5040(f,r,l) = f*720 + r*240 + l
hash_fnv1a = [CARRIER ONLY, NOT PROTOCOL IDENTITY]
validate(inst,rule) → bool (deterministic)
chirality(hash) → posting|pulling|balanced|incomplete
```

---

## COMMON ERRORS & FIXES

**ERROR**: Using hash as identity
**FIX**: Use place-value OmiInst as identity

**ERROR**: Skipping validation
**FIX**: Always validate before ring_store

**ERROR**: Hardware action before receipt
**FIX**: Wait for metatron_project after ring_store

**ERROR**: Claiming validator authority
**FIX**: Only invoke tetragrammatron_validate, don't validate yourself

**ERROR**: Modifying receipts in ring
**FIX**: Receipts are immutable; create new citation if change needed

---

## DOCUMENTS TO READ

In order:
1. **README-FOR-LLMS.md** (this guide)
2. **OMI-PROTOCOL.md** (protocol definition)
3. **OMI-AGENTS.md** (your constraints)
4. **OMI-SKILLS.md** (algorithms)
5. **OMI_Canonical_Doctrine_Place_Value_Identity.md** (identity rule)

Total: 3-5 hours to full understanding + implementation

---

## REMEMBER

```
You implement.
You do not validate.
You do not accept.
You follow the rule.
The rule is: canon validates, receipt accepts.
Everything else is candidate, carrier, or projection.
```

---

## FINAL STATEMENT

If you implement OMI correctly, you will have:

✓ Deterministic protocol (same input → same output always)
✓ No central authority needed (local validation + receipt exchange)
✓ Hardware safety (lazy evaluation)
✓ Verifiable state (receipts are immutable)
✓ P2P synchronization (without global authority)

Go implement. You have everything you need.

**The Covariant Rule governs everything.**
