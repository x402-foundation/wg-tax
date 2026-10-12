# Tax Working Group

The **Tax Working Group** standardizes tax jurisdiction discovery, Merchant-of-Record (MoR) attribution, in-band buyer tax qualification, sub-cent micro-tax calculation, and cryptographic audit transparency within the x402 (RFC 9110 HTTP 402) protocol framework.

As autonomous AI agents, programmatic services, and cross-border API clients transact at machine speed, the Tax Working Group establishes common architectural interfaces for determining tax liability, exchanging verifiable compliance proofs, and generating tamper-evident accounting receipts without adding latency or centralizing payment rails.

## Leadership & Governance

* **Chair**: Open (Call for Community Nominations)
* **Co-Chair**: Open (Call for Community Nominations)
* **Lead Specification Editor**: Walter Hawkins (`@whawk46`)
* **Operating Model**: All work is conducted openly under the Linux Foundation Antitrust Policy and Apache-2.0 license.

## Goals

The primary goals of the Tax Working Group are:

1. **Strict Settle-Only Accounting & Ledger Invariance**: Mandate that tax liabilities, gross volume metrics, and platform ledger counters trigger exclusively upon verified settlement execution (`result.success` / finalized on-chain transfer), strictly decoupling pre-flight authorization validity checks (`/verify`) to eliminate phantom tax accrual and wash-trading manipulation.
2. **Jurisdiction Discovery & Merchant-of-Record (MoR) Attribution**: Standardize normative schemas for resource servers to declare tax jurisdictions, Merchant-of-Record (MoR) relationships, VAT/EIN registration identifiers, and applicable tax regimes upfront via DNS TXT records (`_x402`) and domain discovery manifests (`/.well-known/x402`), enabling autonomous buyers to discover tax terms prior to transaction execution.
3. **In-Band Buyer Tax Qualification & Exemption Assertions**: Define standardized wire protocols allowing spending agents and enterprise buyers to assert tax qualification (e.g., EU VAT B2B reverse-charge eligibility, sales tax exemption, non-profit or sovereign status) directly within the payment authorization payload, cryptographically bound to the principal buyer to prevent intermediary tampering.
4. **Cryptographic Audit Receipts & Transparency Logging**: Define interoperability profiles for deterministic RFC 8785 (JCS) canonical tax and accounting receipts, specifying anchoring patterns to verifiable transparency services (such as IETF SCITT and append-only audit logs) to provide immutable, non-malleable records for enterprise accounting and tax authority examination.
5. **Cross-Working Group Interoperability & Multi-Rail Neutrality**: Harmonize tax declaration and receipt data models across heterogeneous settlement systems (EVM, XRPL, Flare, Stellar, Algorand, traditional payment card networks) and coordinate normative data flows with Domain Discovery (manifest advertisement), Identity (buyer principal attribution), and Card Acceptance (sales tax line items).

## Anticipated Deliverables

* **Specification**: *x402 Tax Transparency & Accounting Architecture Specification*.
* **JSON Schema**: Normative schemas for `tax-transparency` discovery extensions, buyer qualification payloads, and signed tax receipt statements.
* **Reference Vectors**: Test suites for deterministic RFC 8785 tax receipt hashing, settle-only counter gating, and cross-jurisdictional B2B reverse-charge assertions.
* **Implementation Guide**: Architectural best practices for automated sub-cent tax accounting, Merchant-of-Record compliance, and IETF SCITT transparency service registration.

## Meetings

Bi-weekly on Thursdays  
10:00 AM CT | 11:00 AM ET | 8:00 AM PT | 4:00 PM BST | 11:00 PM SGT

A schedule of public meetings and calendar links can be found on the [x402 Community Calendar](https://x402.org/get-involved/#calendar).

### Resources

- Past meeting notes and agendas are available [online](https://docs.google.com/document/d/1Kedu1FxVJPqvc8a6qtJO6m1MAGKoHuA2wtjSr9FYt_s/edit?tab=t.0).
- Recordings of public meetings are accessible through the LFX platform.

## Communication Channels

- **Public Slack**: [#wg-tax](https://x402workspace.slack.com/archives/C0B9Z89FP18)
- **Mailing List**: [Tax-Working-Group@lists.x402.org](mailto:Tax-Working-Group@lists.x402.org) (Subscribe at [lists.x402.org](https://lists.x402.org/g/tax-working-group))
