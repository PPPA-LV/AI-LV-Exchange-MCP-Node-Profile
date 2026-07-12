# Contributing

This document implements **§16 (Conflicts of interest)** and **§18 (Versioning)** of the AI LV Exchange MCP Node Profile. It is not a formality: the profile makes specific commitments about who may edit it and how decisions are made, and this file is where those commitments are kept.

---

## 1. Who edits this profile

The profile is edited by an **open working group** convened by PPPA.

**A single company MUST NOT be the sole editor.** This is a normative requirement of the profile (§16), not an aspiration, and it constrains PPPA as much as anyone else.

| | |
|---|---|
| **Chair** | PPPA. Convenes, publishes agendas and minutes, does not hold a casting vote on technical content. |
| **Members** | Open. Any implementer, participant, prospective participant, public body, academic or independent reviewer may join by opening an issue requesting membership. |
| **Editors** | At least two, from **different organisations**. If only one organisation is willing to supply an editor, the profile does not advance to a new version until a second is found. |
| **Minutes** | Public, in this repository. |

Membership is not conditional on being an Exchange participant, on using any particular technology, or on commercial relationship with PPPA or any member.

---

## 2. Declaration of interests — mandatory

Every contributor who proposes normative change **MUST** declare their interests. There is no exception for editors, for the chair, or for PPPA.

A declaration is required where the contributor, or their employer, or an entity they control:

- sells or intends to sell an implementation of this profile;
- sells or intends to sell a product whose adoption this profile would advantage;
- is an Exchange participant, or is applying to become one;
- performs, or expects to perform, Registration Authority or conformity assessment functions;
- has any other interest a reasonable reader would want to know about.

**How.** Add the declaration to the pull request description, using the template in §6. Declarations are also recorded in [`INTERESTS.md`](INTERESTS.md), which is public and kept current.

**Consequence of declaring.** Declaring an interest does not disqualify anyone. Concealing one does. A contributor with a declared interest participates fully in discussion, and **recuses from the decision** on any change that would:

- make their implementation mandatory or advantaged;
- exclude a competing implementation;
- assess the conformity of their own product;
- accredit their own service.

This mirrors §16 of the profile. It is the mechanism by which a contributor who is *both* a standard's author and a vendor of a product implementing it remains a legitimate participant rather than a conflict of interest.

---

## 3. What kind of change are you proposing

| Type | Examples | Path |
|---|---|---|
| **Editorial** | Typos, clarity, formatting, examples | PR. One editor approves. |
| **Substantive, non-breaking** | New optional credential, additional test case, tightened guidance | PR + working group discussion. Two editors from different organisations approve. |
| **Breaking** | Changing a MUST, changing the AL×T matrix, changing the credential model or crypto profile | **New profile version.** See §5. |

If you are unsure, open an issue first. It is cheaper than a rejected PR.

---

## 4. Requirements for normative change

A pull request that changes a normative requirement **MUST** state, in the description:

1. **The problem.** What breaks, or cannot be built, without this change.
2. **Who is affected.** Which participants, at which assurance level and access tier.
3. **Implementation cost.** What an existing node must do to comply.
4. **Test impact.** Which Annex B.5 test cases change, and what new ones are needed. **A normative requirement with no test case is not accepted** — if it cannot be tested, it cannot be conformed to.
5. **Interoperability.** Whether it conflicts with MCPF, MCP, W3C VC/DID, or OAuth. If it diverges from an upstream specification, say so explicitly and justify it.
6. **Declaration of interests** (§2).

Test criteria must be **implementation-neutral**. A test that only one product can pass is not a test, it is a procurement specification.

---

## 5. Versioning

Per §18 of the profile:

- Each node declares the exact profile version it implements.
- **A participant is never automatically bound by a later version.**
- Breaking changes require: a new version number; documented migration guidance; a transition period; updated conformance tests; and participant acceptance where contractual obligations change.
- **Technical changes during a live pilot require the participating organisation's approval.** A merged PR is not authority to change a running pilot.

Versions are tagged. The version in `README.md`, in `schemas/`, and in the `profileVersion` field of the manifest MUST always agree. A PR that changes one and not the others will not be merged.

---

## 6. Pull request template

```markdown
## Change type
Editorial / Substantive non-breaking / Breaking

## Problem
What cannot be built or verified without this change.

## Who is affected
Assurance levels: AL_
Access tiers:    T_
Existing nodes must: ...

## Test impact
New or modified Annex B.5 cases:
- [ ] ...

## Interoperability
Conflicts with MCPF / MCP / W3C VC / DID / OAuth?  Yes / No
If yes, justification:

## Declaration of interests
I, [name], contributing on behalf of [organisation], declare:

- [ ] I have no interest to declare.
- [ ] I declare the following interest(s): ...

I confirm that I will recuse myself from the decision on this change if it
would advantage an implementation in which I have an interest.
```

---

## 7. Reporting a defect in the profile

Security-relevant defects — anything by which a node could be impersonated, a credential forged, a revocation ignored, or a perimeter bypassed — go to **security@pppa.lv**, not to a public issue, in the first instance. We will acknowledge within 3 working days and agree a disclosure timeline with you.

Everything else goes in a public issue. A specification that hides its defects is not worth implementing, and §24 of the profile exists precisely so that they can be seen.

---

## 8. Appeals

A contributor or participant who believes a decision was taken improperly — including a decision to reject a change, or to withdraw an accreditation (§15) — may appeal.

The review body **MUST exclude any person with a commercial interest** in the affected node or in a competing implementation. If that leaves the working group without a quorum, an independent reviewer is appointed from outside it.

---

## 9. Upstream

Where a change here would be useful beyond Latvia — particularly to the MCP Trust Framework — it **SHOULD** be proposed upstream once field-tested. This profile is intended to be a national profile of open standards, not a fork of them.

Conversely, where this profile diverges from an upstream specification, the divergence **MUST** be documented and justified in the profile itself, not left implicit.
