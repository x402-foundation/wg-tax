# x402 Working Group: Tax & Audit Transparency Strawman Specification

> **Document Status**: Unified Working Group Strawman Proposal (PR #4 — Incorporating Buyer-Side Qualification & PR #5 Multi-Tier Attribution)  
> **Target Working Group**: x402 Tax & Accounting Working Group (`x402-foundation/wg-tax`)  
> **Authors**: Walter Hawkins (`@whawk46`) & Baptiste - Pimlol (`@Pimlol`)  
> **Companion References**: IETF SCITT Working Group (`scitt@ietf.org`) · Issue #3226 · RFC 8785 (JCS) · Delaware C-Corp Standing

---

## Abstract

This specification defines a lightweight, non-bloating tax jurisdiction discovery, buyer-side qualification layer, and immutable audit transparency extension for the x402 payment protocol.

Addressing the sub-cent micro-tax problem, this standard:
1. **Enforces Strict Settle-Only Accounting**: Mandates that tax, ledger, and volume counters trigger strictly upon completed `/settle` execution (`result.success`), entirely decoupling pre-flight `/verify` checks to eliminate phantom catalog volume.
2. **Standardizes Upfront Tax Discovery & Qualification**: Advertises tax jurisdiction, Merchant-of-Record (MoR) status, buyer qualification (`taxRegime` / `qualificationBasis`), and jurisdiction-prefixed vocabularies (`eu-vat`) upfront via DNS (`_x402`) and `/.well-known/x402`.
3. **Generates Cryptographic Audit Receipts**: Registers deterministic JSON Canonicalization Scheme (RFC 8785 JCS) accounting receipts onto SCITT Transparency Services for institutional audit readiness.

---

## 1. Problem Statement & Multi-Chain Consensus

### 1.1 The `/verify` vs `/settle` Conflation & Wash-Trading Defenses (Issue #3226 Data)
Production telemetry across independent implementations (EVM/Base, Stellar/Soroban commit `fae6daa9`, and multi-enclave facilitators) revealed two shared architectural defects:
* **Pre-flight Conflation**: Facilitators previously incremented volume and tax counters on `/verify` (`isValid: true`). `/verify` proves only that a signature *could* settle, not that capital transferred.
  - **Resolution**: Invariant rule that tax and volume ledgers MUST trigger exclusively on final `/settle` execution (`result.success` and atomic transfer verification).
* **Nominal Micro-Trial Gaming**: Requiring merely `value > 0` allows free trials or nominal fractions (e.g. 1 micro-unit or $0.0001) to artificially inflate settled gross volume.
  - **Resolution**: Invariant rule that settled volume MUST satisfy:
    $$\text{settledAmount} \ge \text{challenge.advertisedAmount}$$
    Sub-threshold promotional trials automatically drop out of gross volume metrics across all supported assets.

### 1.2 EIP-3009 Event-Driven Ledger Disambiguation
Single-boolean status checks (`authorizationState() == true`) conflate execution with cancellation. In conformance with working group telemetry (zero `AuthorizationCanceled` events across ~108k Base blocks), accounting ledgers MUST derive execution strictly from `AuthorizationUsed` and `AuthorizationCanceled` event logs.

---

## 2. Protocol Extension: `tax-transparency`

### 2.1 Upfront Discovery Manifest
Resource servers advertise jurisdiction, VAT/EIN registration, and Merchant-of-Record (MoR) routing in their `_x402` DNS TXT record or origin manifest:

```dns
_x402.merchant.example. IN TXT "v=x402-1; wk=https://merchant.example/.well-known/x402; k=resource-server; jur=US-DE; mor=facilitator.example"
```

### 2.2 Tax Metadata Schema
```json
{
  "version": "x402-1",
  "extensions": {
    "tax-transparency": {
      "jurisdiction": "US-DE",
      "merchantOfRecord": "Facilitator Treasury, Inc.",
      "vatOrTaxId": "US-EIN-XX-XXXXXXX",
      "taxIncluded": false,
      "rateBasisPoints": 0,
      "protocolAccrualBps": 20,
      "taxRegime": "eu-vat",
      "qualificationBasis": "b2b-reverse-charge",
      "auditReceiptService": "https://scitt.transparency.org"
    }
  }
}
```

---

## 3. Cryptographic Audit Receipts (SCITT & JCS)

### 3.1 Deterministic JCS Canonicalization (RFC 8785)
All accounting entries, settlement summaries, and tax invoices MUST be canonicalized using RFC 8785 (JCS) before signing, eliminating hash malleability across heterogeneous accounting databases.

### 3.2 SCITT Signed Accounting Statements
Upon settlement, the facilitator emits a signed statement registered to a SCITT Transparency Service containing:
* **Settlement Hash & Block Height**
* **Gross Transaction Value & Currency (USDC, RLUSD, FLR)**
* **Facilitator Protocol Accrual (0.20% / 20 bps)**
* **Tax Line Allocation, Tax Regime & Jurisdiction Code**
* **Cryptographic Enclave Attestation Hash**

```json
{
  "statementType": "https://x402.org/schemas/v1/tax-receipt",
  "settlementHash": "0x4b78a912e6...",
  "grossAmount": "100.000000",
  "currency": "USDC",
  "netPayee": "99.800000",
  "protocolTreasury": "0.200000",
  "taxAmount": "0.000000",
  "taxRegime": "eu-vat",
  "qualificationBasis": "b2b-reverse-charge",
  "jurisdiction": "US-DE",
  "timestamp": 1787806800,
  "enclaveMeasurement": "0x7f83b1657ff1fc53b92dc18148a1d65dfc2d4b1fa3d677284addd200126d9069"
}
```

---

## 8. Buyer-Side Tax Qualification & Multi-Tier Delegation Architecture

### 8.1 The 6-Axis Tax Decomposition
To ensure global compliance across autonomous agent transactions without protocol bloat, tax decisioning is decomposed across six orthogonal axes:
1. **Governing Tax Regime**: Declares the legal tax domain (`eu-vat`, `us-sales-tax`, `cross-border-exempt`).
2. **Qualification Basis**: Specifies the buyer's statutory status (`b2b-reverse-charge`, `exempt-entity`, `standard-b2c`).
3. **Jurisdiction Identifier**: ISO 3166-2 jurisdiction code of taxable presence (`EU-DE`, `US-IL`, `APAC-SG`).
4. **Tax Identifier Validation**: Declares legal tax identifier (e.g., VAT ID `DE123456789`) with verifiable proof.
5. **In-Band Principal Attribution**: Cryptographically signed delegation envelope linking ephemeral agent keys to the legal principal.
6. **Post-Settlement SCITT Anchor**: Canonical RFC 8785 JCS hash binding the attribution tuple into immutable transparency ledgers.

---

### 8.2 EOA & Smart Account Agnostic In-Band `principalAttribution` & Signed Payload Invariant
Legal attribution MUST NOT require the deployment of a smart contract account. Furthermore, to prevent intermediate hops (agent runtimes, aggregators, API gateways) from forging or attaching an unauthorized principal attribution, `onBehalfOf` / `principalAttribution` **MUST sit inside the signed authorization payload**.

The wallet signature binds the `{ challengeDigest, onBehalfOf }` tuple directly, guaranteeing that the settlement receipt's `principalAttribution` is derived directly from the verified signature rather than self-reported or appended post-hoc at settlement time.

```json
{
  "authorizationPayload": {
    "challengeDigest": "sha256:0123456789abcdef...",
    "onBehalfOf": {
      "principalId": "did:pkh:eip155:1:0x1234567890abcdef1234567890abcdef12345678",
      "scheme": "secp256k1-eip712",
      "jurisdiction": "EU-DE",
      "taxIdentifier": "DE123456789",
      "scope": "compute:inference:read",
      "nonce": "0x8fbc492a017e890c",
      "exp": 1788438000
    }
  },
  "sig": "0x5c48b2910fa89b213897e4198402bcde..."
}
```

* **Cryptographic Anti-Tamper Invariant**: A verifying seller or facilitator checks that `sig` resolves to the authorizing key over the canonical hash of `{ challengeDigest, onBehalfOf }`. Any modification to `onBehalfOf` by an intermediary gateway invalidates the signature.
* **Receipt Derivation**: The facilitator's emitted settlement receipt and SCITT audit record MUST derive `principalAttribution` directly from this verified signature preimage, never from an unauthenticated settlement parameter.
* **Execution Equality**: Operates identically whether `principalId` represents an unhosted EOA signing an EIP-712 envelope or an institutional ERC-4337 Smart Account.

---

### 8.3 Dual-Binding Lifecycle (In-Band Decisioning ➔ SCITT Anchor)
To eliminate timing vulnerabilities, the protocol enforces a two-stage binding lifecycle:

```
[Pre-Flight Request]
Buyer Agent  ───(In-Band `principalAttribution`)───▶  Seller Resource Node
                                                          │
                                                (Evaluates VAT Status)
                                                (Calculates 0% Reverse-Charge)
                                                          ▼
[Settlement & Clearing]
Seller Node  ───(Atomic Settle via x402 Rail)───▶  Settlement Engine
                                                          │
                                                (Computes JCS Canonical Hash)
                                                          ▼
[Post-Settlement Audit]
Facilitator  ───(SCITT Signed Statement)────────▶  SCITT Transparency Service
```

1. **Pre-Flight In-Band Decisioning**: The seller inspects `principalAttribution` during payment authorization to evaluate tax rules (e.g. applying EU B2B reverse-charge 0% tax) in real time before execution.
2. **Post-Settlement SCITT Anchor**: Upon successful settlement, the facilitator computes the RFC 8785 JCS hash of the `principalAttribution` tuple and embeds it directly into the SCITT receipt, guaranteeing permanent post-hoc non-repudiation.

---

### 8.4 N-Hop Delegation Chains
In enterprise agent fleets, legal principals delegate operational budgets through multiple organizational tiers. §8.6.3 defines an ordered delegation chain:

$$\text{Principal (Corporate Treasury)} \xrightarrow{\text{Scope 1}} \text{Fleet Operator (Gateway)} \xrightarrow{\text{Scope 2}} \text{Autonomous Agent (Session Key)}$$

```json
{
  "delegationChain": [
    {
      "hop": 1,
      "role": "agent",
      "entityId": "did:key:z6MkhaXG...",
      "parentLink": "did:pkh:eip155:1:0xOperator...",
      "scope": "execution:sub-task",
      "nonce": "0x01",
      "exp": 1788438000,
      "sig": "0x..."
    },
    {
      "hop": 2,
      "role": "operator",
      "entityId": "did:pkh:eip155:1:0xOperator...",
      "parentLink": "did:pkh:eip155:1:0xCorporateTreasury...",
      "scope": "budget:daily:500usd",
      "nonce": "0x02",
      "exp": 1788500000,
      "sig": "0x..."
    },
    {
      "hop": 3,
      "role": "principal",
      "entityId": "did:pkh:eip155:1:0xCorporateTreasury...",
      "taxIdentifier": "US-EIN-XX-XXXXXXX",
      "jurisdiction": "US-DE",
      "scope": "corporate:treasury:root",
      "nonce": "0x03",
      "exp": 1790000000,
      "sig": "0x..."
    }
  ]
}
```

* **Liability Integrity**: Each delegation hop maintains its own cryptographic signature, distinct permission scope, and expiration timestamp.
* **Non-Collapsing Legal Mapping**: Regulators and corporate auditors can inspect the entire provenance chain to determine exact organizational accountability without collapsing the legal relationships.
