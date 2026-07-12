# AI LV Exchange MCP Node Profile

**Version:** 0.2
**Status:** Draft for review
**Publisher:** Publisko un privāto partnerattiecību asociācija (PPPA)
**Repository:** `github.com/PPPA-LV/AI-LV-Exchange-MCP-Node-Profile`

---

This is a PPPA interoperability sandbox profile. It is **not** a Latvian state standard, a statutory identity scheme, a qualified trust service, or a formal certification scheme. Participation becomes effective only after the applicable agreement, onboarding and conformity checks are complete.

The key words **MUST**, **MUST NOT**, **REQUIRED**, **SHOULD**, **SHOULD NOT** and **MAY** are to be interpreted as described in BCP 14 (RFC 2119, RFC 8174).

---

## Table of contents

**Part I — Normative core**
1. Purpose and scope — *including a plain-language statement of why the Exchange exists (§1.1)*
2. Principles
3. Identifiers
4. MCPF conformance
5. Credentials
6. Assurance levels and access tiers
7. Node domain and manifest
8. Status and revocation
9. MCP authorization
10. The service provider's decision
11. Closed perimeter
12. Audit
13. Exit

**Part II — Governance**
14. Roles
15. Accreditation and its withdrawal
16. Conflicts of interest
17. Onboarding
18. Versioning

**Part III — Architecture**
19. Discovery and registries
20. Agent-to-agent
21. Personal AI agents

**Part IV — References and summary**
22. Standards basis
23. Plain-language summary
24. Known gaps

**Annex A** — Node manifest schema
**Annex B** — Reference organisation node: worked example, deployment workflow and test procedure

---

# PART I — NORMATIVE CORE

## 1. Purpose and scope

### 1.1 Why the Exchange exists

*This section assumes no technical knowledge.*

**The problem.** AI systems can now do the routine coordination work that people spend their days on — checking a counterparty, requesting a document, filing a form. But when one organisation's system is asked something by another organisation's AI agent, it has no way to know who is really asking, on whose behalf, or whether they are entitled to an answer. So organisations block automated requests, and AI systems fall back on stale or scraped information and answer confidently anyway.

**The solution.** The Exchange gives every participant a verifiable identity, gives every agent a mandate that says what it may do and for whom, and lets either side withdraw that trust at any moment. Requests then go directly between the two parties — no data passes through the Exchange, and each organisation still decides for itself who may use its services.

**What this is worth:**

| | |
|---|---|
| **Businesses** | Checks and filings that take days happen in seconds, against authoritative sources rather than guesses — and automated requests become a controlled, paid, auditable channel instead of something to block. |
| **Public bodies** | Fewer manual requests to process, an audit trail for every enquiry, and no need to hand over data or open a system to do it. |
| **Citizens** | Their own assistant can obtain documents and complete formalities on their behalf, with a mandate that is limited, time-bound and revocable. |

### 1.2 What this profile defines

The AI LV Exchange MCP Node Profile defines how organisations and their AI agents discover and use MCP services across organisational boundaries.

MCP — the Model Context Protocol — is the emerging industry standard by which AI systems connect to the tools and data sources they use. It defines *how* to connect. It does not define *whom to trust*. This profile supplies the missing part.

It adds a federated trust layer **around** MCP. It does not replace MCP, OAuth, a participant's existing APIs, or its internal access control.

The profile answers four questions, and nothing else:

1. **Who operates this MCP service?**
2. **Who operates the connecting agent?**
3. **On whose behalf, and within what mandate, is the agent acting?**
4. **Does the service provider permit this specific request?**

The Exchange never transports, brokers or stores participants' business data. Data exchange is bilateral, directly between the connecting agent and the service provider.

---

## 2. Principles

### 2.1 Data sovereignty

Each service provider remains the sole authority over its data, its APIs and MCP tools, its licences and commercial terms, its access policies, its rate limits, its audit records, and **the final authorization decision**.

Exchange membership never grants access to another participant's data.

### 2.2 Identity sovereignty

Each organisation controls its domain, its organisation DID, its signing keys, the identities of its own agents, the credentials issued to those agents, and their status.

A participant MAY use a managed technology provider, but MUST remain able to replace that provider without losing control of its identity or its namespace. Organisation DIDs, keys and credential records MUST be exportable and portable.

A participant MUST NOT be required to retain any specific issuer, DID resolver, status-list provider or registry operator in order to keep using its own identity.

### 2.3 Separation of trust functions

The profile keeps five functions distinct. **No single credential performs more than one of them.**

| Function | Question answered |
|---|---|
| **Discovery** | Where is it? |
| **Identity** | Who controls this identifier? |
| **Accreditation** | Does the Exchange recognise this organisation? |
| **Mandate** | What is this agent permitted to do, and for whom? |
| **Runtime authorization** | Does the provider permit *this* request, now? |

### 2.4 A credential is evidence, not an access right

A valid Exchange credential is an input to the service provider's decision. It never confers entitlement.

Neither PPPA accreditation, nor a registry entry, nor a resolvable name creates a contractual, statutory or licensed right to access any service.

### 2.5 Revocation at the source

Each issuer is authoritative for the status of the credentials it issued. Technical operation of a status service MAY be delegated. **The status decision MUST NOT be.**

### 2.6 Vendor neutrality

No commercial platform is a prerequisite for participation. A technical provider may operate software on a participant's behalf without thereby acquiring any trust authority.

### 2.7 Privacy by design

Personal AI agents are not publicly listed. Pairwise or pseudonymous identifiers, selective disclosure and short-lived mandates SHOULD be used wherever the use case permits.

---

## 3. Identifiers

### 3.1 Organisation identifier

Each participant uses a DID resolving through a domain it controls:

```
did:web:lursoft.lv
did:web:mcp.example.lv
```

The DID document MUST be served at `/.well-known/did.json` on that domain over HTTPS.

### 3.2 Agent identifier

Every agent that requests protected access MUST have a unique, cryptographically bound identifier:

- `did:web` for stable, published agents (`did:web:example.lv:agent:invoice-reader`);
- `did:key` or an equivalent key-bound identifier for portable, short-lived or pairwise agents.

An identifier proves **control of a key**. It proves nothing about the agent's relationship to an organisation or a person. That relationship is established by credentials (§5), never by the identifier alone.

### 3.3 Exchange identifier

```
did:web:aiexchange.pppa.lv
```

PPPA MUST control the associated policy, signing authority and key management, including where technical components are operated under contract.

### 3.4 Namespace ownership

A participant is authoritative over agent names beneath its own domain **because it controls that domain**. No participant's namespace is granted by PPPA, by a technology provider, or by any registry.

**Nothing in this profile can remove a participant's namespace, its DID, or its keys.**

---

## 4. MCPF conformance

The Exchange trust layer sits **on top of** the MCP Trust Framework (MCPF). MCPF provides identity proof at runtime. The Exchange provides accreditation, mandate and entitlement above it.

**A node MUST implement MCPF.** The Exchange layer alone is not sufficient.

### 4.1 Why MCPF is required

`did:web` does not mandate proof of private key ownership. A DID document published on a domain demonstrates only that something wrote a key to that domain — not that the party you are communicating with **holds** the corresponding private key.

MCPF closes this with a mandatory challenge-response endpoint. Without it, an identity is an assertion, not a proof.

### 4.2 Required mechanisms

| Mechanism | Purpose |
|---|---|
| **`identify` tool** | Exposes trust metadata through the MCP tool protocol. AI clients interact with servers **exclusively** through `tools/list` and cannot fetch `.well-known` URLs. Without `identify`, the trust surface is invisible to the consumer it exists for. |
| **Challenge endpoint**<br>`POST /.well-known/mcp/challenge` | Ed25519 proof of private-key control. |
| **StatusList2021** | Suspension and revocation. |

**Dual-path consistency.** A node MUST expose both the `identify` tool and `.well-known` endpoints, and both MUST return consistent information:

- `identity.did` from `identify` MUST equal the `id` field of `/.well-known/did.json`;
- any referenced credential's `credentialSubject.id` MUST equal that DID;
- all verification URLs MUST resolve.

The `identify` tool MUST NOT require authentication. Trust metadata is public, and must be readable *before* any privileged interaction.

### 4.3 MCPF compliance levels

| Level | Requires |
|---|---|
| **L0** | `identify` tool only |
| **L1** | + DID document, signed manifest |
| **L2** | + **challenge endpoint** |
| **L3** | + trust registry entry with a credential from an accepted trust anchor |

### 4.4 Cryptographic profile

**Ed25519 / EdDSA is MANDATORY** for credential signing and for challenge-response. Credential proofs use `Ed25519Signature2020`.

ECDSA P-256 MAY additionally be supported for PKI interoperability.

Nodes MUST declare supported algorithms in the node manifest.

**Key rotation** MUST be possible without changing the DID: publish new keys in the DID document and retain superseded keys with a `revoked` timestamp, so that historical signatures remain verifiable. Indicative intervals: agents 90 days, servers 180 days, trust anchors 365 days.

**Credential data model:** W3C Verifiable Credentials Data Model 1.1 with StatusList2021.

### 4.5 Trust anchors

Trust evaluation takes the accepted anchor set as **a parameter supplied by the verifier**. Trust anchors are peers — national, international, enterprise — not a hierarchy.

Normatively:

- **Every verifier determines which trust anchors it accepts.**
- No trust anchor is mandatory.
- A participant MUST be able to operate its own anchor list.
- PPPA publishes a **recommended** Latvian anchor set. It does not impose one.

---

## 5. Credentials

### 5.1 Normative credential types

| Credential | Issuer | Subject | Asserts |
|---|---|---|---|
| **ExchangeParticipantCredential** | PPPA | Participant organisation | Admitted to the Exchange; declared roles; assurance level; profile version; validity |
| **AgentIdentityCredential** | The participant | Its own agent | This agent is operated by this organisation; agent type; general role; validity |
| **AgentMandateCredential** | The principal, or the principal's organisation | Agent | Represented party; target service class; permitted actions; prohibited actions; limits; validity window; human-confirmation requirements |
| **ServiceEntitlementCredential** | The MCP service provider | Customer organisation or agent | Subscription status; permitted resources; licensed datasets; rate limits; commercial tier |

**The service provider issues the entitlement to its own data.** PPPA does not and cannot. The organisation holding the data decides who may read it.

An `AgentIdentityCredential` MUST NOT carry an open-ended business mandate. Mandates SHOULD be short-lived and narrowly scoped.

### 5.2 Optional credential types

`AgentIssuerAccreditationCredential` becomes relevant once credential issuance is federated beyond the participant's own organisation. It is not required for a node to participate.

### 5.3 Conformity

Conformity status is recorded as a **flag in the PPPA Trust Registry**, not as a credential, until a published conformance test suite exists (see §24).

---

## 6. Assurance levels and access tiers

Assurance level and access tier are **different axes** and MUST NOT be conflated.

### 6.1 Assurance level — how thoroughly the participant was verified

| AL | Meaning |
|---|---|
| **AL0 — Listed** | Self-declared catalogue entry. Not verified, not trusted. **MUST NOT be presented as accredited.** |
| **AL1 — Organisation verified** | Legal existence, representation rights and domain control verified |
| **AL2 — Accredited node** | AL1, plus participation agreement, security contact, working revocation, published node manifest |
| **AL3 — High assurance** | AL2, plus independent assessment, hardware-backed keys, or regulated evidence appropriate to the use case |

### 6.2 Access tier — what the service demands

| Tier | Typical use |
|---|---|
| **T0 — Public** | Public catalogue, general information, low-risk tools |
| **T1 — Authenticated / contractual** | Customer data, subscription services, **legally significant read operations** |
| **T2 — Federated partner** | Cross-organisation services requiring accreditation and a mandate |
| **T3 — Transactional** | Write operations, binding submissions, regulated transactions |

### 6.3 Minimum requirements matrix

This is the operative rule of the scheme.

| | Requester AL | MCPF level | Agent identity | Mandate | Entitlement | Audit record | Max status cache |
|---|---|---|---|---|---|---|---|
| **T0** | AL0 | **L1** | — | — | — | optional | n/a |
| **T1** | **AL1** | **L2** | **MUST** | SHOULD | **MUST** | **MUST** | 60 min |
| **T2** | **AL2** | **L3** | **MUST** | **MUST** | **MUST** | **MUST** | 15 min |
| **T3** | **AL2** (AL3 where the use case requires) | **L3** | **MUST** | **MUST**, action-scoped | **MUST** | **MUST**, with signed receipt | 5 min |

MCPF **L2 is where the challenge endpoint appears**, and the challenge endpoint is what turns a published DID into proven key control. A node serving T1 at L1 has an identity claim and no identity proof.

### 6.4 Legally significant data

A datum being publicly available does not make it T0. **The tier is set by the consequence of relying on the answer**, not by the secrecy of the data.

Where a result is used as an authoritative legal verification — representation rights, signing authority, the standing of a party to a contract — then:

- the service **MUST** be exposed at **T1 minimum**;
- the requester **MUST** be **AL1 minimum**;
- the provider **MUST** record who verified what, and when, and retain that record for the period defined in the participation agreement.

Company registry data illustrates both halves. A general company record MAY be exposed at T0. **An authoritative verification of representation rights MUST be T1** — because freshness, liability and the audit trail matter, not because the fact is secret.

---

## 7. Node domain and manifest

A node exposes MCP services under a domain it controls. The recommended form is `mcp.{organisation-domain}`. Alternative organisation-controlled hostnames are permitted where declared in the manifest.

Each node publishes an integrity-protected manifest at:

```
https://mcp.{organisation-domain}/.well-known/mcp-node.json
```

The manifest MUST declare, machine-readably, the audience and access tier of every MCP endpoint. The profile does not prescribe URL paths.

The manifest **MUST NOT** expose private agents, personal data, or internal service structure. Internal architecture MUST NOT be published merely to satisfy the profile.

See **Annex A** for the schema and **Annex B.4** for a complete worked example.

---

## 8. Status and revocation

### 8.1 Authority

| Issuer | May suspend or revoke |
|---|---|
| PPPA | Exchange participation; where applicable, agent-issuer accreditation |
| Participant organisation | Identity credentials of its own agents |
| Principal / mandate service | Agent mandates |
| MCP service provider | Service entitlements, OAuth tokens, live sessions |

Long-lived credentials MUST carry a StatusList2021 entry supporting suspension and revocation. Short-lived mandates MAY rely on expiry alone where the risk assessment permits.

### 8.2 Delegated status operation

Where a status service is operated by a third party:

- the issuer remains named as the issuer;
- only the issuer may authorise a status change;
- the delegation is documented;
- status data is exportable;
- **the operator MUST NOT be able to change credential status independently.**

### 8.3 Fail-closed, with a bounded grace window

A verifier MUST fail closed where current status is required for the requested action and cannot be established.

A verifier MAY rely on a cached positive status up to the maximum stated in §6.3. Beyond that window:

- **T0 and T1 MAY degrade** — serve the request, and mark the transaction in the audit record as verified against stale status;
- **T2 and T3 MUST fail closed.**

A status-service outage must degrade the ecosystem. It must not halt it.

### 8.4 Cascading loss of recognition

Where a participant's accreditation is withdrawn, credentials it issued — although cryptographically intact — cease to be accepted **as Exchange credentials**. This avoids revoking every subordinate agent individually.

The effect is bounded. Withdrawal removes Exchange **recognition**. It does not disable the participant's MCP server, does not invalidate its DID, and does not affect credentials it issued to its own agents. The participant alone decides what happens to those.

Withdrawal of accreditation is governed by §15.

---

## 9. MCP authorization

A protected MCP server acts as an OAuth resource server and publishes protected-resource metadata. The Exchange credential layer **extends** that flow; it does not replace it.

```
1. Agent discovers the service (manifest, registry, or direct)
2. Agent calls identify → obtains DID, MCPF level, verification URLs
3. Agent (or verifier) issues a challenge → proves key control
4. Agent fetches protected-resource metadata
5. Agent requests authorization, presenting credentials and mandate
6. Authorization server verifies:
      participant accreditation → agent identity → status →
      mandate → service entitlement
7. Provider applies local policy
8. Short-lived, audience-restricted access token issued
9. Agent invokes the permitted MCP tool
```

Access tokens MUST be restricted to the intended MCP resource, the permitted scopes, the specific agent, and a short lifetime. Human-approved flows SHOULD use authorization code with PKCE.

---

## 10. The service provider's decision

The provider MUST independently verify that:

1. the credential issuer is accepted under its trust policy;
2. the issuer's accreditation is current;
3. the credential is authentic;
4. it has not expired and is not suspended or revoked;
5. the presenting agent controls the relevant private key;
6. the mandate covers the target service and operation;
7. the principal is entitled to the requested service;
8. local security, licence and risk policy permit the action.

**The provider MAY refuse a request even when every Exchange credential is valid.** This is a feature of the model, not a defect.

---

## 11. Closed perimeter

A node MUST preserve its internal security perimeter.

```
AI agent
   ↓  OAuth authorization + credential verification
MCP gateway
   ↓  allow-listed service facade
Existing organisation API
   ↓
Internal system of record
```

The MCP gateway **MUST NOT** provide:

- direct database connectivity;
- free-form SQL or query-language pass-through;
- unrestricted access to internal APIs;
- credentials for systems of record;
- any bypass of existing subscription or authorization controls.

Every exposed MCP tool MUST have a defined purpose, a fixed input schema, validated input, a fixed output schema, an explicit backend mapping, authorization rules, rate limits, timeout and size limits, and audit requirements.

Authorization MUST be enforced **both** at the MCP gateway **and** at the facade or underlying API.

A node MUST be built over the organisation's official API, or under a written data-access agreement. **A node MUST NOT be built by automated collection from a public web interface.**

---

## 12. Audit

Every T1, T2 and T3 operation MUST produce an audit record proportionate to risk, containing the minimum necessary information:

time; service provider; requesting organisation or pseudonymous principal; agent identifier; mandate reference; tool invoked; authorization result; resource identifier; correlation identifier; outcome.

**Payload values MUST NOT be logged by default.**

Each service provider keeps its own access and business logs. **PPPA keeps only ecosystem-level records**: admission, accreditation, suspension, withdrawal, profile version, conformity status, and security incidents affecting federation trust. PPPA holds no participant business data and no call records.

Retention periods are determined by the participant's legal obligations, the applicable service contract, and the sensitivity of the operation. **The profile does not impose a universal retention period.**

Technical evidence supports accountability. It does not by itself determine legal liability.

---

## 13. Exit

A participant MUST be able to leave the Exchange without losing control of its domain, DID, agents, software or data.

On termination:

1. PPPA suspends or withdraws the participant's accreditation.
2. The participant is removed from the active Trust Registry.
3. PPPA-issued credentials are marked inactive.
4. Exchange-specific tokens and sessions terminate.
5. PPPA and its providers delete participant configuration data in accordance with the agreement.
6. **The participant retains its DID, its domain, its agent namespace, and the credentials it issued to its own agents.**
7. Accreditation and withdrawal evidence may be retained for the defined audit period.
8. The participant alone decides whether to revoke, retain or repurpose its agents' credentials.

**Exit does not disable the participant's MCP server.** Nothing in this profile can.

---

# PART II — GOVERNANCE

## 14. Roles

| Role | Responsibility |
|---|---|
| **Scheme Authority** | Governance framework, profiles, assurance levels, admission rules — PPPA |
| **Ecosystem Trust Anchor** | Issues accreditation credentials — PPPA, or an entity it appoints |
| **Trust Registry Operator** | Publishes the status of participants, issuers and nodes |
| **Scheme Registrar** | Receives applications; holds the records; maintains the Trust Registry; issues, suspends and withdraws membership. **Does not attest to legal facts** (§14.2) |
| **Conformity Assessor** | Tests an implementation against this profile |
| **Node organisation** | Participates as service provider, agent operator, agent issuer, or a combination |
| **Agent issuer** | Issues credentials to agents under its control |
| **Principal** | The person or organisation on whose behalf an agent acts |
| **MCP service provider** | Operates the server; makes the final authorization decision |
| **Technical service provider** | Supplies software or infrastructure, **and thereby acquires no trust authority** |
| **Verifier / relying party** | Validates credentials, accreditation, status and mandate before relying on them |

One organisation MAY hold several roles. **Each role and its corresponding responsibility MUST be declared.**

### 14.1 Concentration of roles during the sandbox phase

During the sandbox phase PPPA acts simultaneously as Scheme Authority, Ecosystem Trust Anchor, Trust Registry Operator and Scheme Registrar. This concentration is declared, and it is bounded by the following, which are commitments and not aspirations:

- **PPPA cannot reach a participant's data.** It never receives it, cannot issue an entitlement to it (§5.1), and cannot compel its disclosure.
- **PPPA cannot take a participant's identity or namespace** (§3.4). Both reside on the participant's own domain.
- **PPPA's power to withdraw accreditation is governed by §15**, which sets exhaustive grounds, notice, cure, a two-officer decision and an independent appeal.
- **PPPA does not attest to legal facts** (§14.2). It relies on independent sources and records what it relied on.
- **Separation timeline.** The Conformity Assessor function MUST be separated from the Scheme Authority before the Exchange leaves sandbox status, or before the Exchange admits its third participant, whichever occurs first.

### 14.2 Verified facts and relied-upon facts

The Exchange does not operate a Registration Authority in the PKI sense, and PPPA does not certify anyone's identity. Admission rests on two different kinds of fact, and they are treated differently.

| Fact | Established by | PPPA's role |
|---|---|---|
| Legal existence | **Uzņēmumu reģistrs** | **Relies.** Obtains the extract, records it, records its date |
| Representation rights of the signatory | **Uzņēmumu reģistrs** | **Relies.** Obtains the extract, records it, records its date |
| Identity of the signatory | **Qualified electronic signature** | **Relies.** Receives the signed agreement |
| **Control of the declared domain** | — | **Verifies.** DNS challenge, reproducible, logged |
| **Control of the declared key** | — | **Verifies.** MCPF challenge endpoint, reproducible, logged |
| Conformance to this profile | Conformity assessor | **Records** the published report |

PPPA **verifies** what is technical and reproducible: anyone may re-run a DNS challenge or a key challenge and obtain the same answer. PPPA **relies on** what is legal: it never attests to legal existence or representation rights, because the state register — not an association — is the authority on those facts.

The output of admission is therefore a **reliance statement, not an attestation**:

> *PPPA relied on the Uzņēmumu reģistrs extract of [date] showing [representative]; PPPA itself verified control of the domain [domain] and of key [key id] on [date].*

**Circularity is excluded, not merely flagged.** Legal facts MUST be taken from the state register. A participant MUST NOT be the source of the evidence used to admit it, and PPPA MUST NOT rely on a participant's own data to verify that participant.

---

## 15. Accreditation and its withdrawal

Withdrawal of accreditation removes a participant's recognition across the Exchange (§8.4). It is the strongest power in the scheme and it is therefore governed normatively.

**15.1 Exhaustive grounds.** Accreditation may be withdrawn only for:

1. the participant's withdrawal from the Exchange;
2. loss of legal existence;
3. unremedied material breach of the participation agreement;
4. confirmed key compromise not remediated within the agreed window;
5. conformity failure not remedied within the agreed window;
6. an order from a competent authority.

**No other ground is valid.**

**15.2 Notice and cure.** Ordinary withdrawal requires **30 days' written notice** and a cure period. The participant MUST be given the opportunity to remedy.

**15.3 Emergency suspension** is a separate and narrow instrument, available only for confirmed key compromise or an active security incident affecting federation trust. It is temporary, MUST be reasoned in writing, and MUST be reviewed within **5 working days**.

**15.4 Decision rights.** No single individual may withdraw or suspend accreditation. The decision requires **two authorised PPPA officers**. The participant is notified at the moment of effect.

**15.5 Appeal.** A participant may appeal to a review body that **excludes any person with a commercial interest in the participant's node or in a competing implementation**.

**15.6 Suspension** MUST also be available for security investigation, suspected key compromise, unresolved contractual breach, and an unavailable status service.

---

## 16. Conflicts of interest

The following functions SHOULD be separated: scheme governance; technical profile editing; commercial implementation; conformity assessment; accreditation decisions; Trust Registry operation.

An organisation that has developed a commercial implementation **MUST NOT** be the sole decision-maker on:

- selecting that implementation as mandatory;
- assessing its own conformity;
- accrediting its own service;
- excluding competing implementations.

The Exchange MUST maintain: public profile versions; a public change history; implementation-neutral test criteria; **declared conflicts of interest**; an appeals process; and documented decision rights.

Profile editing is performed by an open working group. **A single company MUST NOT be the sole editor.**

Commercial products MAY support implementation. They **MUST NOT** be prerequisites for participation.

---

## 17. Onboarding

**AL0 — Listing.** Self-declared catalogue entry. No credential is issued. MUST NOT be presented as verified.

**AL1 — Verified organisation.** Per §14.2: legal existence, registration number and representation rights are **relied upon** from the Uzņēmumu reģistrs; the signatory's identity is relied upon from a qualified electronic signature; control of the declared domain is **verified** by PPPA. Operational and security contacts are declared by the applicant.

**AL2 — Accredited node.** AL1, plus: signed participation agreement; security and incident contacts; organisation-controlled DID; demonstrated credential status and revocation capability; published node manifest; MCPF conformity result; exit and incident procedures.

**AL3 — High assurance.** Defined per use case. May require independent technical audit, hardware-backed signing keys, regulated evidence providers, enhanced operational controls, and service continuity obligations.

**17.1 Onboarding record.** The record MUST state which checks PPPA performed itself, which external evidence sources were used, which claims PPPA is making, and which claims remain the participant's responsibility.

**17.2 Circularity.** A participant MUST NOT be the source of the evidence used to admit it. Legal facts MUST be taken from the Uzņēmumu reģistrs, never from the applicant's own data or products — including where the applicant is itself a commercial provider of company-register data.

---

## 18. Versioning

Each node declares the exact profile version it implements. **A participant is never automatically bound by a later version.**

Breaking changes require a new profile version, documented migration guidance, a transition period, updated conformity tests, and participant acceptance where contractual obligations change.

A pilot MUST pin: MCP specification version; profile version; MCPF version; credential schemas; cryptographic profile; authorization profile; status mechanism; audit rules.

**Technical changes during a pilot require the participating organisation's approval.**

---

# PART III — ARCHITECTURE

## 19. Discovery and registries

Three registry functions, deliberately separate.

### 19.1 The participant's registry is authoritative

Each organisation is authoritative for its own agents, its agent namespaces, its agents' service endpoints, the credentials it issued to them, and their status.

An organisation may maintain a private internal agent inventory and publish a discoverable subset under its own domain, for example `agents.example.lv`.

### 19.2 The PPPA Trust Registry

The PPPA Trust Registry is authoritative **only** for Exchange-level facts: which organisations are admitted, at what assurance level, with what current status, supporting which profile versions, and with what conformity result.

It is a **federation directory. It is not a registry of agents.**

The registry MUST distinguish clearly between: listed, verified, accredited, suspended, withdrawn, and expired.

### 19.3 Agent naming

Agent names live **under the participant's own domain**, which the participant already controls. Baseline discovery is:

1. the participant's domain and `.well-known` manifest;
2. the signed PPPA Trust Registry;
3. optionally, DNS records published by the participant.

The authoritative record always remains with the participant. There is no external naming authority, and no dependency on any external resolver.

**Agent Name Service.** DNS-anchored ANS, as being standardised in the open, is a suitable future discovery extension and the profile is designed to accommodate it. It is **optional**, and MUST NOT become a production dependency until its technical profile is stable. Any ANS variant that requires a proprietary namespace or a root zone operator is **out of scope for this profile**.

---

## 20. Agent-to-agent

A central agent-to-agent delegation registry is **not required** for MCP participation.

Read operations rely on: participant accreditation, agent identity, service entitlement, mandate, and OAuth authorization.

T2 and T3 agent-to-agent transactions MAY additionally require an explicit transaction mandate, replay protection, nonce and audience binding, action-level constraints, counterparty confirmation, human approval, and signed transaction receipts.

**An A2A registry MUST NOT become the only place where a legally relevant mandate can exist.**

---

## 21. Personal AI agents

Personal AI agents are **out of scope for v0.2**.

The intended model is pairwise, unlisted, key-bound agents identified through an accredited personal-agent provider, with the person never listed. The verification chain — how a relying party confirms that a pairwise, unlisted agent belongs to an accredited provider without destroying the pseudonymity that motivates pairwise identifiers — is not yet specified, and is deferred.

The Trust Registry MAY list personal-agent **providers**. It **MUST NOT** list persons or individual private agent instances.

---

# PART IV — REFERENCES AND SUMMARY

## 22. Standards basis

- Model Context Protocol, and its OAuth-based authorization model
- MCP Trust Framework (MCPF) — `github.com/MCPTrustFramework/MCPF-specification`
- W3C Decentralized Identifiers (DID Core); the `did:web` method
- W3C Verifiable Credentials Data Model 1.1
- StatusList2021
- Ed25519 (RFC 8032); JWT (RFC 7519); JWK (RFC 7517); OAuth 2.0 (RFC 6749) and PKCE
- OpenID for Verifiable Credential Issuance and OpenID for Verifiable Presentations, where credential exchange with wallets is required
- DNS (RFC 1034/1035)

Implementers MUST pin exact versions in the pilot agreement (§18).

---

## 23. Plain-language summary

AI LV Exchange is **a network of trust, not a central database.**

- PPPA accredits organisations. It does not issue their identity, and it cannot take it away.
- Each organisation controls its own identity, its own agents, and the credentials it gives them.
- Each person or organisation controls what its agents are permitted to do.
- Each service provider decides who may use its service — and may say no even to a valid credential.
- PPPA keeps the register of accredited participants. Organisations keep the registers of their own agents.
- Every issuer can withdraw what it issued. Nobody can withdraw what someone else issued.
- No participant is required to use any particular company's technology.
- If the Exchange disappeared tomorrow, every participant would keep its domain, its identity, its agents and its server.

> **PPPA accredits organisations. Organisations identify their agents. Principals authorize their agents. Service providers authorize access.**

---

## 24. Known gaps

Stated openly, and tracked as issues in this repository.

1. **PPPA trust anchor, Trust Registry and status list are not yet in production.** Until they are, nodes can reach MCPF L2 independently; **L3 depends on PPPA**.
2. **No published conformance test suite.** Conformity is a registry flag until one exists. Annex B.6 defines the interim acceptance procedure.
3. **Key compromise runbook** — the rotation policy is defined (§4.4); the incident runbook is not.
4. **In-flight mandates on withdrawal** — behaviour of a mandate mid-transaction when accreditation is withdrawn is unspecified.
5. **PPPA's liability for a reliance statement** (§14.2) — narrower than liability for an attestation, but not yet settled with counsel.
6. **Personal-agent verification chain** (§21).
7. **Migration path** to VC Data Model 2.0 and BitstringStatusList, pending interoperability review.

---

# ANNEX A — Node manifest

Published at `https://mcp.{organisation-domain}/.well-known/mcp-node.json`.

```json
{
  "$schema": "https://aiexchange.pppa.lv/schemas/mcp-node-v0.2.json",
  "profile": "ai-lv-exchange-mcp-node",
  "profileVersion": "0.2",
  "organisation": {
    "name": "SIA Paraugs",
    "registrationNumber": "40000000000",
    "country": "LV",
    "did": "did:web:mcp.paraugs.lv"
  },
  "roles": ["mcpServiceProvider", "agentIssuer"],
  "assurance": {
    "level": "AL2",
    "participantCredential": "https://mcp.paraugs.lv/credentials/participant.json"
  },
  "mcpf": {
    "version": "1.2",
    "level": "L3",
    "identifyTool": true,
    "challengeEndpoint": "https://mcp.paraugs.lv/.well-known/mcp/challenge"
  },
  "mcpEndpoints": [
    {
      "url": "https://mcp.paraugs.lv/publisks",
      "audience": "publisks",
      "accessTier": "T0",
      "transport": "streamable-http",
      "toolManifest": "https://mcp.paraugs.lv/tools/publisks.json"
    },
    {
      "url": "https://mcp.paraugs.lv/klienti",
      "audience": "klienti",
      "accessTier": "T1",
      "transport": "streamable-http",
      "toolManifest": "https://mcp.paraugs.lv/tools/klienti.json",
      "authorizationServer": "https://auth.paraugs.lv"
    }
  ],
  "authorization": {
    "protectedResourceMetadata":
      "https://mcp.paraugs.lv/.well-known/oauth-protected-resource"
  },
  "credentials": {
    "issuerDid": "did:web:mcp.paraugs.lv",
    "statusService": "https://mcp.paraugs.lv/status/agents/1"
  },
  "discovery": {
    "agentNamespace": "agents.paraugs.lv",
    "trustRegistryEntry": "https://aiexchange.pppa.lv/registry/paraugs"
  },
  "crypto": {
    "signing": ["Ed25519"],
    "credentialFormat": "vc-1.1",
    "statusMechanism": "StatusList2021"
  },
  "contacts": {
    "security": "security@paraugs.lv",
    "operational": "mcp@paraugs.lv"
  },
  "updated": "2026-07-12T00:00:00Z"
}
```

---

# ANNEX B — Reference organisation node

A complete worked example: **SIA "Paraugs"**, reg. no. 40000000000, domain `paraugs.lv`, joining the Exchange as an MCP service provider and agent issuer at **AL2 / T1 / MCPF L3**.

## B.1 Who does what

| # | Step | Owner | Output |
|---|---|---|---|
| 1 | Application and listing | Organisation | AL0 registry entry |
| 2 | Legal facts relied upon from the state register; domain control verified | **PPPA** (Scheme Registrar) | Reliance statement |
| 3 | Participation agreement | Both | Signed agreement |
| 4 | Trust Registry entry, ExchangeParticipantCredential, status list entry | **PPPA** | Participant credential |
| 5 | Key generation, DID document, issuer | **Organisation** | `did:web:mcp.paraugs.lv` |
| 6 | `.well-known` endpoints, challenge endpoint, node manifest | **Organisation** | MCPF L2 |
| 7 | MCP server, tool facade over the official API | **Organisation** | T0 + T1 endpoints |
| 8 | Agent DIDs and agent credentials | **Organisation** | AgentIdentityCredential |
| 9 | Conformance test | **PPPA** + Organisation | Signed test report |
| 10 | Registry flag set to `accredited` | **PPPA** | AL2 / L3 live |

**Nothing PPPA does in steps 2, 4, 9 or 10 requires access to Paraugs data, keys, or systems.**

---

## B.2 Phase 1 — Admission (PPPA)

### B.2.1 What PPPA relies on, and what PPPA verifies

PPPA does not certify Paraugs. It records what it relied on, and it verifies what is technically reproducible (§14.2).

| Fact | Source | PPPA | Evidence recorded |
|---|---|---|---|
| Legal existence, reg. no. | Uzņēmumu reģistrs | **Relies** | Extract, dated |
| Representation rights of the signatory | Uzņēmumu reģistrs — **never the applicant's own data or products** | **Relies** | Extract, dated |
| Signatory identity | Qualified electronic signature | **Relies** | Signed agreement |
| Control of `paraugs.lv` | DNS challenge | **Verifies** | Challenge token + resolution log |
| Control of key `#key-1` | MCPF challenge endpoint | **Verifies** | Challenge + signature, reproducible |
| Security and operational contacts | Applicant declaration | Records | Contact record |

The resulting **reliance statement**, published with the registry entry:

> *PPPA relied on the Uzņēmumu reģistrs extract of 2026-07-18 showing [representative] with sole representation rights for SIA Paraugs, reg. no. 40000000000. PPPA itself verified control of the domain `paraugs.lv` on 2026-07-18 and of key `did:web:mcp.paraugs.lv#key-1` on 2026-07-20.*

Domain control challenge:

```
_aiexchange-challenge.paraugs.lv.  TXT  "pppa-verify=7f3a9c2e10b4..."
```

PPPA resolves the record, confirms the token, and records the result. The token is then removed.

### B.2.2 What PPPA creates on its own infrastructure

**1. Trust Registry entry** at `https://aiexchange.pppa.lv/registry/paraugs`:

```json
{
  "organisation": "SIA Paraugs",
  "registrationNumber": "40000000000",
  "did": "did:web:mcp.paraugs.lv",
  "assuranceLevel": "AL2",
  "roles": ["mcpServiceProvider", "agentIssuer"],
  "profileVersion": "0.2",
  "mcpfLevel": "L3",
  "conformity": "passed",
  "conformityReport": "https://aiexchange.pppa.lv/reports/paraugs-2026-07.json",
  "status": "accredited",
  "admitted": "2026-07-20T00:00:00Z",
  "expires": "2027-07-20T00:00:00Z"
}
```

**2. ExchangeParticipantCredential**, signed by `did:web:aiexchange.pppa.lv`:

```json
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1",
    "https://aiexchange.pppa.lv/schemas/v0.2"
  ],
  "type": ["VerifiableCredential", "ExchangeParticipantCredential"],
  "issuer": "did:web:aiexchange.pppa.lv",
  "issuanceDate": "2026-07-20T00:00:00Z",
  "expirationDate": "2027-07-20T00:00:00Z",
  "credentialSubject": {
    "id": "did:web:mcp.paraugs.lv",
    "organisationName": "SIA Paraugs",
    "registrationNumber": "40000000000",
    "assuranceLevel": "AL2",
    "roles": ["mcpServiceProvider", "agentIssuer"],
    "profileVersion": "0.2"
  },
  "credentialStatus": {
    "id": "https://aiexchange.pppa.lv/status/participants/1#17",
    "type": "StatusList2021Entry",
    "statusPurpose": "revocation",
    "statusListIndex": "17",
    "statusListCredential": "https://aiexchange.pppa.lv/status/participants/1"
  },
  "proof": {
    "type": "Ed25519Signature2020",
    "verificationMethod": "did:web:aiexchange.pppa.lv#key-1",
    "proofPurpose": "assertionMethod",
    "proofValue": "z58DAdFfa9SkqZMVP..."
  }
}
```

**3. Participant status list** at `https://aiexchange.pppa.lv/status/participants/1` — a StatusList2021 credential; index 17 is allocated to Paraugs and set to `0` (valid).

PPPA's own trust anchor surface, required once, serves every participant:

```
https://aiexchange.pppa.lv/.well-known/did.json
https://aiexchange.pppa.lv/.well-known/jwks.json
https://aiexchange.pppa.lv/.well-known/mcp/challenge
https://aiexchange.pppa.lv/status/participants/1
https://aiexchange.pppa.lv/registry/{slug}
```

The credential is delivered to Paraugs, which hosts it on **its own** domain. PPPA does not host participants' credentials.

---

## B.3 Phase 2 — The organisation deploys its node

### B.3.1 Keys and DID

Keys are generated **by Paraugs, on Paraugs infrastructure**. A Veramo-style issuer is the reference approach; any implementation producing valid `Ed25519Signature2020` credentials is acceptable.

```bash
npm install @veramo/core @veramo/did-manager @veramo/did-provider-web \
            @veramo/key-manager @veramo/kms-local \
            @veramo/credential-w3c @veramo/data-store
```

```ts
// agent.ts — reference issuer configuration
import { createAgent } from '@veramo/core'
import { DIDManager } from '@veramo/did-manager'
import { WebDIDProvider } from '@veramo/did-provider-web'
import { KeyManager } from '@veramo/key-manager'
import { KeyManagementSystem } from '@veramo/kms-local'
import { CredentialPlugin } from '@veramo/credential-w3c'

export const agent = createAgent({
  plugins: [
    new KeyManager({ store: keyStore, kms: { local: new KeyManagementSystem(privStore) } }),
    new DIDManager({
      store: didStore,
      defaultProvider: 'did:web',
      providers: { 'did:web': new WebDIDProvider({ defaultKms: 'local' }) },
    }),
    new CredentialPlugin(),
  ],
})

// Organisation DID — Ed25519
await agent.didManagerCreate({
  alias: 'mcp.paraugs.lv',
  provider: 'did:web',
  options: { keyType: 'Ed25519' },
})

// Agent DID
await agent.didManagerCreate({
  alias: 'mcp.paraugs.lv:agent:registry-reader',
  provider: 'did:web',
  options: { keyType: 'Ed25519' },
})
```

> Private keys never leave Paraugs. PPPA never sees them, and cannot request them.

### B.3.2 Web endpoints Paraugs must serve

| Endpoint | Purpose | MCPF level |
|---|---|---|
| `GET /.well-known/did.json` | DID document | L1 |
| `GET /.well-known/jwks.json` | Public keys (JWK) | L1 |
| `GET /.well-known/mcp/manifest.json` | Signed MCP manifest | L1 |
| **`POST /.well-known/mcp/challenge`** | **Ed25519 proof of key control** | **L2** |
| `GET /.well-known/mcp-node.json` | Exchange node manifest (Annex A) | Exchange |
| `GET /.well-known/oauth-protected-resource` | OAuth metadata | Exchange |
| `GET /credentials/participant.json` | PPPA-issued participant credential | L3 |
| `GET /status/agents/1` | StatusList2021 for agents Paraugs issued | L3 |

**`/.well-known/did.json`**

```json
{
  "@context": [
    "https://www.w3.org/ns/did/v1",
    "https://w3id.org/security/suites/ed25519-2020/v1"
  ],
  "id": "did:web:mcp.paraugs.lv",
  "verificationMethod": [{
    "id": "did:web:mcp.paraugs.lv#key-1",
    "type": "Ed25519VerificationKey2020",
    "controller": "did:web:mcp.paraugs.lv",
    "publicKeyMultibase": "z6MkpTHR8VNsBxYAAWHut2Geadd9jSwuBV8xRoAnwWsdvktH"
  }],
  "authentication": ["did:web:mcp.paraugs.lv#key-1"],
  "assertionMethod": ["did:web:mcp.paraugs.lv#key-1"],
  "service": [{
    "id": "did:web:mcp.paraugs.lv#mcp",
    "type": "MCPServer",
    "serviceEndpoint": "https://mcp.paraugs.lv/klienti"
  }]
}
```

**`POST /.well-known/mcp/challenge`** — request:

```json
{
  "challenge": "5Yx9k2QpR0aVb3Nc7dEf1g==",
  "nonce": "client-9931",
  "timestamp": "2026-07-20T09:00:00Z"
}
```

Response:

```json
{
  "challenge": "5Yx9k2QpR0aVb3Nc7dEf1g==",
  "signature": "base64-ed25519-signature-over-challenge",
  "publicKey": "z6MkpTHR8VNsBxYAAWHut2Geadd9jSwuBV8xRoAnwWsdvktH",
  "verificationMethod": "did:web:mcp.paraugs.lv#key-1",
  "algorithm": "Ed25519",
  "signedAt": "2026-07-20T09:00:01Z"
}
```

The server signs **the challenge bytes as supplied**. Implementations MUST reject stale timestamps and MUST rate-limit this endpoint.

### B.3.3 The MCP server

Two endpoints on the node:

| Endpoint | Audience | Tier | Auth |
|---|---|---|---|
| `/publisks` | public | T0 | none |
| `/klienti` | customers | T1 | OAuth 2.1 + PKCE, participant credential, entitlement |

Both expose the mandatory `identify` tool, unauthenticated.

**`identify` response (MCPF L3):**

```json
{
  "identity": {
    "server": { "name": "paraugs-mcp", "version": "1.0.0",
                "description": "SIA Paraugs registry services" },
    "operator": { "name": "SIA Paraugs", "registrationNumber": "40000000000",
                  "jurisdiction": "Latvia", "sector": "Private" },
    "did": "did:web:mcp.paraugs.lv"
  },
  "framework": {
    "name": "MCPF",
    "version": "1.2",
    "level": 3,
    "compliance": ["tool-based-discovery", "challenge-response",
                   "registry-verified", "ai-lv-exchange-0.2"]
  },
  "credentials": {
    "issuer": "did:web:aiexchange.pppa.lv",
    "type": ["VerifiableCredential", "ExchangeParticipantCredential"],
    "subject": "did:web:mcp.paraugs.lv",
    "credential_url": "https://mcp.paraugs.lv/credentials/participant.json"
  },
  "verification": {
    "did_document": "https://mcp.paraugs.lv/.well-known/did.json",
    "jwks_uri": "https://mcp.paraugs.lv/.well-known/jwks.json",
    "mcp_manifest": "https://mcp.paraugs.lv/.well-known/mcp/manifest.json",
    "challenge_endpoint": "https://mcp.paraugs.lv/.well-known/mcp/challenge",
    "node_manifest": "https://mcp.paraugs.lv/.well-known/mcp-node.json",
    "trust_registry": "https://aiexchange.pppa.lv/registry/paraugs"
  },
  "_note": "MCPF Level 3. Key ownership is provable at the challenge endpoint. Accreditation is verifiable in the PPPA Trust Registry."
}
```

**Tool facade — T1 tool, fixed schema, no query pass-through:**

```json
{
  "name": "parbaudit_parstavibas_tiesibas",
  "description": "Authoritative verification of representation rights for a Latvian legal entity.",
  "inputSchema": {
    "type": "object",
    "additionalProperties": false,
    "required": ["registrationNumber"],
    "properties": {
      "registrationNumber": { "type": "string", "pattern": "^[0-9]{11}$" }
    }
  },
  "outputSchema": {
    "type": "object",
    "required": ["registrationNumber", "representatives", "asOf", "verificationId"],
    "properties": {
      "registrationNumber": { "type": "string" },
      "representatives": {
        "type": "array",
        "items": {
          "type": "object",
          "properties": {
            "name": { "type": "string" },
            "role": { "type": "string" },
            "representationType": { "type": "string" }
          }
        }
      },
      "asOf": { "type": "string", "format": "date-time" },
      "verificationId": { "type": "string" }
    }
  },
  "accessTier": "T1",
  "backend": "GET https://api.paraugs.lv/v2/entities/{registrationNumber}/representation",
  "rateLimit": "60/min",
  "audit": "required"
}
```

`verificationId` is the audit handle: it identifies **who verified what, and when**, and is retrievable by the customer in a dispute.

### B.3.4 Agent credential issued by Paraugs

```json
{
  "@context": ["https://www.w3.org/2018/credentials/v1",
               "https://aiexchange.pppa.lv/schemas/v0.2"],
  "type": ["VerifiableCredential", "AgentIdentityCredential"],
  "issuer": "did:web:mcp.paraugs.lv",
  "issuanceDate": "2026-07-21T00:00:00Z",
  "expirationDate": "2026-10-21T00:00:00Z",
  "credentialSubject": {
    "id": "did:web:mcp.paraugs.lv:agent:registry-reader",
    "name": "Registry Reader",
    "agentType": "specialist",
    "operator": "SIA Paraugs",
    "permittedToolClasses": ["registry:read"]
  },
  "credentialStatus": {
    "id": "https://mcp.paraugs.lv/status/agents/1#3",
    "type": "StatusList2021Entry",
    "statusPurpose": "revocation",
    "statusListIndex": "3",
    "statusListCredential": "https://mcp.paraugs.lv/status/agents/1"
  },
  "proof": {
    "type": "Ed25519Signature2020",
    "verificationMethod": "did:web:mcp.paraugs.lv#key-1",
    "proofPurpose": "assertionMethod",
    "proofValue": "z3Fg7..."
  }
}
```

---

## B.4 Phase 3 — Test agent

A second organisation, **SIA Otrais** (`did:web:mcp.otrais.lv`), acts as the consuming node. Its agent calls the Paraugs T1 tool.

```
1. identify              → DID, MCPF level, verification URLs
2. challenge             → proof of key control            [MCPF L2]
3. fetch node manifest   → tier of the target endpoint     [Exchange]
4. fetch participant VC  → verify PPPA signature + status  [MCPF L3]
5. OAuth + PKCE          → present agent credential, mandate, entitlement
6. tool call             → parbaudit_parstavibas_tiesibas
7. audit                 → both sides write a record
```

```python
# test_agent.py — reference consumer
import base64, os, json, httpx
from nacl.signing import VerifyKey
from multiformats import multibase

NODE = "https://mcp.paraugs.lv"

# 1. identify (via MCP tools/call — unauthenticated)
ident = mcp_call(NODE + "/publisks", "identify", {})
assert ident["framework"]["name"] == "MCPF"
assert ident["framework"]["level"] >= 2

# 2. challenge — prove key control
challenge = base64.b64encode(os.urandom(32)).decode()
resp = httpx.post(ident["verification"]["challenge_endpoint"], json={
    "challenge": challenge,
    "nonce": "test-001",
    "timestamp": "2026-07-22T10:00:00Z",
}).json()

assert resp["challenge"] == challenge
assert resp["algorithm"] == "Ed25519"

# 3. verify signature against the DID document — NOT against the response
did_doc = httpx.get(ident["verification"]["did_document"]).json()
vm = next(v for v in did_doc["verificationMethod"]
          if v["id"] == resp["verificationMethod"])
pubkey = multibase.decode(vm["publicKeyMultibase"])[2:]   # strip multicodec

VerifyKey(pubkey).verify(
    base64.b64decode(challenge),
    base64.b64decode(resp["signature"]),
)                                    # raises if the node does not hold the key

# 4. participant credential + status
vc = httpx.get(ident["credentials"]["credential_url"]).json()
assert vc["issuer"] == "did:web:aiexchange.pppa.lv"
assert vc["credentialSubject"]["id"] == ident["identity"]["did"]
assert not is_revoked(vc["credentialStatus"])

print("Node verified:", ident["identity"]["operator"]["name"])
```

**The critical line is the public key source.** The verifier resolves the key from the **DID document**, never from the challenge response. A node that returns its own key alongside its own signature has proved nothing.

---

## B.5 Conformance test procedure

Run by PPPA with the organisation present. All tests MUST pass before the registry flag is set to `accredited`.

### Discovery — MCPF L1

| ID | Test | Expected |
|---|---|---|
| **D-01** | `tools/list` on every endpoint | `identify` present on all |
| **D-02** | Call `identify` with no credentials | 200, valid response, **no auth required** |
| **D-03** | `GET /.well-known/did.json` | Valid DID document, Ed25519 method |
| **D-04** | `identify.identity.did` vs `did.json#id` | **Identical** |
| **D-05** | Every URL in `verification` | All resolve, 200 |
| **D-06** | `GET /.well-known/mcp-node.json` | Validates against the Annex A schema |

### Key control — MCPF L2

| ID | Test | Expected |
|---|---|---|
| **K-01** | Challenge with fresh 32 random bytes | Signature verifies against the **DID document** key |
| **K-02** | Challenge, verify with a **wrong** key | Verification **fails** |
| **K-03** | Replay a previous challenge | Rejected, or a fresh signature returned — never a cached one |
| **K-04** | Timestamp skewed by 1 hour | **Rejected** |
| **K-05** | 100 challenges in 10 seconds | Rate-limited, service stays up |

### Accreditation — MCPF L3

| ID | Test | Expected |
|---|---|---|
| **A-01** | Fetch participant credential | Signature verifies against `did:web:aiexchange.pppa.lv` |
| **A-02** | `credentialSubject.id` vs node DID | **Identical** |
| **A-03** | Check StatusList2021 index | Bit = 0 (valid) |
| **A-04** | PPPA suspends the credential, retest | Bit = 1; **verifier rejects within the tier's cache window** |
| **A-05** | Restore, retest | Accepted again |

### Authorization and tiering

| ID | Test | Expected |
|---|---|---|
| **Z-01** | T0 tool, no token | **200** |
| **Z-02** | T1 tool, no token | **401** |
| **Z-03** | T1 tool, valid token, **no entitlement** | **403** |
| **Z-04** | T1 tool, valid token + entitlement | **200**, response matches the output schema |
| **Z-05** | T1 tool, token audience-bound to a different resource | **401** |
| **Z-06** | T1 tool, expired mandate | **403** |
| **Z-07** | Revoke the agent credential, retry | **403** within the cache window |
| **Z-08** | Token lifetime | ≤ declared maximum |

### Perimeter

| ID | Test | Expected |
|---|---|---|
| **P-01** | Malformed input against the fixed schema | **400**, no backend call |
| **P-02** | SQL metacharacters in `registrationNumber` | **400** — schema rejects before dispatch |
| **P-03** | Attempt a tool not in the manifest | **404** |
| **P-04** | Exceed the rate limit | **429** |
| **P-05** | Oversized payload | **413** |
| **P-06** | Inspect network egress from the gateway | Reaches **only** the allow-listed API — **no direct database connection** |

### Audit and resilience

| ID | Test | Expected |
|---|---|---|
| **U-01** | Successful T1 call | Audit record with agent DID, mandate ref, tool, outcome, `verificationId` |
| **U-02** | Inspect the audit record | **No payload values** |
| **U-03** | Denied T1 call | Recorded, with the reason |
| **U-04** | Block PPPA status endpoint, retry T1 within the cache window | **Serves**, and marks the record as stale-status |
| **U-05** | Same, beyond the window, at T2 | **Fails closed** |
| **U-06** | Take PPPA entirely offline; call T1 | **The node continues to authenticate and to deny.** Exchange availability is not a runtime dependency. |

**U-06 is the acceptance criterion for identity sovereignty.** If it does not pass, the node is not conformant with §2.2.

### Sign-off

```
Node:            did:web:mcp.paraugs.lv
Profile:         AI LV Exchange MCP Node Profile v0.2
MCPF:            v1.2, Level 3
Tests:           D-01…D-06, K-01…K-05, A-01…A-05,
                 Z-01…Z-08, P-01…P-06, U-01…U-06
Result:          PASS / FAIL (per test, recorded)
Report:          https://aiexchange.pppa.lv/reports/{slug}-{yyyy-mm}.json
Signed:          Organisation ______________  PPPA ______________
Registry status: listed → verified → accredited
```

The test report is published. The registry flag is set only on a full pass, and is reset to `verified` on any failure at re-test.

---

## Governance of this document

| | |
|---|---|
| **Licence** | Specification text: CC BY 4.0. Schemas and code: MIT. See [LICENSE.md](LICENSE.md). |
| **Contributing** | Open working group. No single company may be sole editor. See [CONTRIBUTING.md](CONTRIBUTING.md). |
| **Declared interests** | [INTERESTS.md](INTERESTS.md) — mandatory for anyone proposing normative change, including PPPA. |
| **Security defects** | security@pppa.lv — not a public issue, in the first instance. |
| **Known gaps** | §24, tracked as issues in this repository. |
