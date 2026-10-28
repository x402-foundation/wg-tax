# Tax Working Group Charter

- **Working Group Name:** Tax Working Group
- **Status:** Adopted at the October 15, 2026 working group session
- **Last Updated:** October 28, 2026
- **Repo:** https://github.com/x402-foundation/wg-tax
- **Authors:** Tim Fisher (Chair), Joseph Hughes Jr (Co-Chair)

---

## Purpose

The Tax Working Group exists so that businesses buying and selling over x402 can meet their tax, accounting and audit obligations. The x402 protocol is an open payments standard, not a tax service: it will not calculate, collect or file anyone's taxes. What it can do is make sure the facts and evidence these obligations depend on are present in or around the transaction when the money moves: who the parties are, where tax is owed, what was sold, at what price, and when and how it settled. At machine speed, those facts can't be pieced back together afterward.

The group brings tax, accounting and audit practitioners together with protocol engineers to define the evidentiary facts necessary for proper tax recognition, recording, and payment, starting from real use cases and the law in force today. The goal is one open definition that facilitators, sellers, tax providers and accounting systems can all build on, so providers can compete on tax services without locking anyone in.

## Strategic Objectives

Define the minimum facts an x402 transaction must carry or point to so its parties can meet their tax, accounting and audit obligations, and a common way to express them that any facilitator, seller or tax provider can implement.

- **Ground the work in real cases:** Build a shared library of x402 use cases and the obligations in force that apply to them. Start with US business-to-business payments, using bank transfer as the baseline and stablecoin as the comparison, and record what decision each fact makes possible.
- **Define the requirements:** Turn those cases into testable requirements, each traced to the obligation that creates it. Agree how they are expressed in x402 without putting personal data in the payment itself.
- **Review and validate:** Act as x402's review point for tax and accounting content wherever it appears. Test requirements with practitioners in more than one jurisdiction and with more than one implementer before proposing them for the core specification.
- **Align with the larger Foundation's design principles:**
  - Simplicity over completeness
  - Demonstrated need over anticipated need
  - Present law over predicted law

## Scope

### In Scope

**Taxes and obligations covered**

- Consumption taxes (sales tax, VAT, GST), information reporting, and the accounting and audit needs that depend on the same transaction facts.
- The data needed for information returns, such as US Form 1099-DA and similar forms, the OECD Crypto-Asset Reporting Framework, and EU DAC8.
- The ability to total activity by legal entity and by jurisdiction for reporting thresholds, even when wallets and receiving addresses change.
- A framework, not legal advice, for identifying where tax obligations sit in common x402 flows: the seller, a facilitator, a marketplace, or a platform paying for its users. The aim is that parties who only route or enforce payment don't take on obligations by accident.

**What the transaction record must support**

- The facts a payment, and the receipt for what was delivered, must carry or point to so the transaction can be booked, taxed and audited:
  - the parties and their roles
  - what was sold
  - the price that applied and why
  - the amounts, including tax
  - what asset settled, and when
- How a buyer or seller can show tax location and tax status (for example, business or consumer, exempt or not) without putting an address or other personal data in the payment. This will be worked out with the Identity Working Group.
- How x402's payment timings (pay after delivery, pay up front, escrow) and later events such as refunds, chargebacks and corrections appear in the record, so the books can tell a sale from a prepayment from a refund.
- What records an auditor or tax authority needs to verify a transaction later without depending on any one party's system. This includes making a check that was skipped distinguishable from one that passed.

**The group's role across x402**

- Overlap with the Identity Working Group requirements as it relates to a shared evidence trail and transaction record, keeping specs aligned in pursuit of the x402 Protocol Design Principles.
- Practitioner review of tax and accounting content proposed anywhere in x402, including core-specification changes and extensions.
- Worked examples and sample records that show each requirement applied.

### Out of Scope

**Tax, legal and accounting services**

- Calculating, collecting or filing tax, or maintaining tax rates and rate tables. The protocol carries the facts; tax engines, facilitators and advisors do the rest.
- Tax, legal or accounting advice to any party. The group will show where obligations can arise and cite the law, but won't say what any party owes.
- Standardizing any organization's chart of accounts, accounting policies or revenue recognition choices.
- Building, endorsing or certifying any vendor, facilitator, accounting system or other product or service.

**Work owned by other x402 groups**

*This group will say what it needs from each of these rather than design them:*

- Identity and verification methods: Identity Working Group
- Card payment mechanics: Card Acceptance Working Group
- Service discovery: Domain Discovery Working Group
- The general format and verification of x402 evidence records, which belong to the foundation's evidence-record work. This group supplies the tax, accounting and audit requirements those records must meet.

**Outside the group's current mandate**

- Requirements based on proposed or expected law. The group may track draft rules, but requirements rest on obligations in force.
- Tax treatment of holding or trading digital assets as investments, except where it changes how a payment must be recorded.
- Insurance and underwriting requirements, at least for now.
- Lobbying or advocacy. The group may engage government agencies for educational purposes, explaining its work or learning a government's position on a topic, but does not lobby.

## Deliverables / Expected Outcomes

### 0–3 Months

- Charter adopted and published in the wg-tax repository
- Intake forms for use cases, requirements and open questions live in the repository
- A review path for proposals submitted to the group: accepted as working drafts with named practitioner reviewers, which does not mean endorsement
- First use case written up: a US business-to-business payment by bank transfer (ACH) as the baseline, compared with the same payment in stablecoin. The record format shouldn't rule out regulated retention.
- First practitioner review of tax content already proposed to the core x402 specification
- Collaboration with the Identity and Evidence groups establishing a core glossary, use cases, etc.

### 3–6 Months

- First set of requirements for one business-to-business flow, each traced to its source obligation
- EU business-to-business VAT use case, with review by European practitioners
- Written request to the Identity Working Group setting out the minimum identity facts that tax and accounting need
- Strategy document moved from skeleton to a full draft for group review, including the open-questions register, by the end of January

### 6 Months+

- Proposal to the TSC for an x402 extension carrying the agreed facts, with worked examples and sample records
- Testing with at least two independent implementers, and practitioner sign-off in more than one jurisdiction; each minimum fact ships with accept and reject test cases, each rejection with a fixed reason code, reproduced by an implementation written by someone other than their authors
- Framework for where tax obligations sit in common flows
- Standing panel of practitioner reviewers (tax, accounting, audit, law) across several jurisdictions

## Working Model

### Operating Model

- **Decision-making:** Consensus-driven, with Chair/Co-Chair & TSC representative-led resolution. If consensus cannot be reached, a formal vote will be executed as per the x402 Technical Charter.
- **Work Tracking:** GitHub issues in the wg-tax repository, filed through the group's intake forms (use case, requirement, open question), with a project board. Each session has an agenda issue posted ahead of time and a written decision record afterward.
- **Primary Artifacts:**
  - use cases
  - requirements (MUST / SHOULD / MAY) with their source obligations
  - an open-questions register
  - the group's strategy document
  - proposals as pull requests to the wg-tax repository and, when ready, to the x402 repository
  - worked examples and sample records
  - review comments on x402 pull requests
  - meeting notes
- **Traceability:** Every requirement names two things:
  - the obligation or accounting need that creates it, with citation, jurisdiction and effective date
  - the design principle it follows: the x402 design principles once the TSC adopts them, and the group's working principles until then

  A requirement that can't point to both isn't ready.
- **Practitioner review:** A requirement is not treated as settled until a practitioner in the relevant field has reviewed it. The group marks plainly which requirements haven't been reviewed yet.
- **Neutrality:** Working group materials name no vendors. Participants disclose relevant commercial interests when they propose or review requirements, and all work follows the Linux Foundation antitrust policy.

## Member Roles

- **Participants (Read):** Anyone attending meetings or contributing asynchronously.
- **Practitioner Reviewers (Triage):** Tax, accounting, audit and legal professionals who review use cases and requirements in their field.
- **General Contributors (Triage):** Members focused on project management, issue organization, or those who contribute code and documents infrequently.
- **Core Contributors (Write Access):** Highly active members with a proven track record of substantive, ongoing contributions.
- **Maintainers/Approvers:** Experienced, highly trusted core members who are responsible for the quality, security, and strategic alignment of the working group's outputs.
- **Chairs/Co-Chairs:** Responsible for WG operations, facilitation, and external representation.

| Role | Persona | GH Access | Primary Responsibility |
| :--- | :--- | :--- | :--- |
| Participant | Attendees & async followers | Read (Basic access) | Join meetings, open issues, join discussions |
| Practitioner Reviewer | Tax, accounting, audit and legal professionals | Triage | Review use cases and requirements in their field; sign off or file open questions |
| General Contributor | Periodic contributors | Triage | Organize boards, label issues, submit PRs via forks |
| Core Contributor | Active, ongoing contributors | Write | Push branches directly, review PRs, build solutions |
| Maintainer | Code quality & repo leads | Maintain | Approve & merge PRs, manage releases & standards |
| Chair / Co-Chair | Group Leads | Admin | Group governance, roadmap, admin settings, permissions |
