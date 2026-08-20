# **x402 Foundation: Tax Working Group Strategy Document Skeleton**

**Skeleton v0.3**  
*For working group discussion*

## Overview

**Authors:** Tim Fisher (co-author), Joseph Hughes Jr. (co-author)

**Committed reviewers:** Stripe (Steve Kaliski plus tax specialists), Coinbase (Philippe d'Argent plus legal/accounting).

**Sourcing rule:** every seed below reflects a position stated in WG meetings or channels, or a public source. Nothing here comes from private conversations, and nothing in this document promotes any vendor.

### Goal of this document

The goal of this document is to provide the skeleton and framework for establishing the x402 Foundation's tax working group's strategy document.

During the inaugural meeting on July 30, 2026, the group discussed and acknowledged that accounting, risk, insurance, and compliance are all downstream of the x402 protocol, and agreed that the impacts to accounting and other dependencies should also be considered within the tax working group, at least until there are more appropriate venues for those topics.

This document is deliberately broader than the working group's likely final scope, and in places broader than the protocol itself. That is intentional. The use cases below are laid out so that every scope choice gets answered explicitly instead of assumed silently. Cutting a question is a decision the group records; skipping one is a gap someone discovers later, in an audit.

### Boundary of the x402 ecosystem

One assumption is made throughout this document, because it has been explicitly stated by the protocol founders:

> The protocol and its ecosystem solve one problem: making sure the right information exists in or around the transaction at the moment money moves. Everything downstream (computing tax, keeping books, filing, auditing, underwriting) is solved by new entities and/or existing businesses working from this information.

Sections mark this with a **Boundary:** callout wherever the line could blur.

### The two halves of every transaction

Every commercial transaction has two components: the delivery of goods or services from seller to buyer, and the movement of compensation from buyer to seller. When the two happen together, that is a cash transaction: three dollars across the counter, a soda back. Most enterprise commerce does not work that way. Businesses run on accrual accounting, where the payment leg and the delivery leg happen at different times, and the books track the gap between them as an asset or a liability.

The protocol has now formally taken a position on this. The TSC's paymentFlow decision (PR \#3053, adopted the first week of August) declares when settlement runs relative to the work, inside existing schemes:

- **authorize** (the default): verify → work → settle. Payment follows the work. The closest to cash.  
- **upfront**: settle → work. Payment lands before delivery. The buyer has prepaid; the seller now owes the work.  
- **escrow**: settle (funds locked) → work → settle (funds released). Payment is committed first and released on delivery.

The moment settlement can run before or after the work, x402 spans accrual patterns, and the two legs must be traceable to each other: every delivery event should be able to point at the payment event it fulfills, and every payment event at the delivery it funds. At machine speed, that lineage cannot be reconstructed afterward from logs; it has to be written at the moment each leg happens. That lineage requirement runs through every section below.

**How to use this skeleton.** Each section gives its purpose, the questions the fleshed section must answer, seeded content, and open issues. The use cases in §2 are the document's engine: each is a short, concrete scenario built to force a decision. Claim a section by adding your name next to it. If you disagree with a seed, convert it into an open issue rather than deleting it: this draft exists to surface disagreements, not resolve them silently. Open issues are tagged `OI-n` inline and collected in §10; use cases are tagged `UC-n` and cross-referenced throughout.

**The two personas, used throughout.** Both are constructions with illustrative figures, not case studies.

- **Persona A, the metered API seller.** Sells a data-lookup API at $0.0004 per call over x402. Buyers are agents. Roughly 40 million calls a month, settled in stablecoin through a facilitator: about $16,000 of monthly revenue arriving as tens of millions of individual events. Runs a free tier, and offers prepaid balances to steady buyers.  
- **Persona B, the priced content seller.** Sells reports at $3.00 each over x402. Buyers are consumer-side agents worldwide. Plans to add card acceptance as that work matures, which brings consumption tax, refunds, and chargebacks into one business.

A third, edge-case study (a nonprofit receiving agent-initiated donations) is proposed as **Annex A**, owned by Joseph.

---

## 0\. Preamble: why now, scope, non-goals

**Purpose.** State plainly why a payments protocol needs a tax-and-accounting document, what this document will eventually make normative, and what it refuses to be. Record the scope the WG adopted, tax → accounting → audit → insurability, in that dependency order, and establish the boundary line above as the document's method.

**Questions this section answers.** Why does this document exist? What will future versions require? What is out of scope permanently?

**Seeds.**

- The protocol's author and WG chair (Erik Reppel) has stated the founding motivation: facilitators exist largely because of tax compliance, and they should compete on tax and transaction-ledgering services through a common extension spec, so that the needed information is defined once and no one is locked in. This document seeds what that spec must carry.  
- The eventual normative payload, for later versions to formalize: the information that must be present in or around an x402 transaction for the money to be bookable, taxable, auditable, and ultimately insurable.  
- The precedent worth naming: the last generation of instant-payment rails optimized for the minimum viable data packet, the fewest fields that let money move without friction. They got the speed. They also got an authorized-push-payment fraud problem publicly reported in the hundreds of millions of pounds a year in the UK alone, now being retrofitted with enhanced fraud-data standards. The lesson for this document is direct: the data requirements are the control surface, and they are far cheaper to set before the rail hardens than after.  
- Why the controls must be structural (use once, here, and nowhere else): a human employee is constrained by things no ledger records, the risk of being fired, a reputation, a sense of what is normal. Auditors call that a culture of control and test for it. Agents have none of it and act at machine speed, so the controls and the evidence have to be carried by the transaction itself.  
- The receiving side comes first. AI monetization gateways stood up by the large CDNs over the past two months mean sellers are already receiving agent payments at volume; the money-in questions are live before most buyers have formal programs.  
- Non-goals: this document is not legal or tax advice; it is not a tax-calculation spec; it does not solve identity (it consumes the identity WG's output, §3); it does not standardize anyone's chart of accounts; it names no vendors. This draft uses US-heavy examples and flags jurisdiction portability wherever it applies.

**Open issues.** `OI-1` Whether "insurable" belongs in the eventual normative field list or stays descriptive context (§9 depends on the answer).

**Owner:** Tim.

---

## 

## 1\. The five questions

**Purpose.** Give the reader one spine to hold. Every field, scenario, and open issue in this document exists to answer one of five questions.

1. Who paid you?  
2. What price was true?  
3. What did you record?  
4. What do you owe the government?  
5. What proves the books are right?

None of these are payment questions. They are accounting questions, answered before the payment instead of after it.

**Mapping table (to flesh):**

| Question | Lifecycle stage (§2) | Use cases | Sections |
| :---- | :---- | :---- | :---- |
| Who paid you? | Settlement → reconciliation | UC-3, UC-4, UC-5, UC-10 | §3, §4 |
| What price was true? | Integration → settlement | UC-2, UC-8 | §4 |
| What did you record? | Reconciliation → accounting submission | UC-1, UC-6 | §2, §4, §8 |
| What do you owe the government? | Tax determination and filing | UC-3, UC-4, UC-5 | §5, §7 |
| What proves the books are right? | Every stage, tested afterward | UC-7, UC-9, UC-10 | §8, §9 |

**Owner:** Tim.

---

## 

## 2\. Lifecycle and use cases: two sellers, three settlement flows

**Purpose.** Ground the document in concrete situations before any field list appears. Walk the four-stage lifecycle the WG adopted (Steve Kaliski's total-integration framing), show how the three paymentFlows change the accounting at each stage, and then put ten use cases on the table, each built to force a working-group decision.

**The four stages.**

1. **Developer integration**: what the seller (and buyer) implements.  
2. **Reconciliation**: settlement events become internal records.  
3. **Accounting submission**: records become books.  
4. **Tax determination and filing**: books become obligations, paid.

**Boundary:** stage 1 and the record contents are protocol-and-ecosystem territory. Stages 3 and 4 belong to the seller's accountants, engines, and filings, working from that record.

### 2.1 What each settlement flow does to the books

Stage 2 is where two accounting worlds meet. x402 settles like cash: value moves the moment the event happens. Business books run on accrual: revenue when earned, obligations when incurred. The paymentFlow field (PR \#3053) is where the protocol acknowledges the difference, and each flow produces a different bookkeeping shape:

| Flow | Order | What the books say |
| :---- | :---- | :---- |
| authorize | work, then settle | Revenue is earned at delivery and cash arrives at settlement, usually moments apart. The near-cash case. |
| upfront | settle, then work | At settlement: cash in, plus a liability, a recorded duty to deliver (deferred revenue or customer deposit). At delivery: the duty closes and revenue is recognized. A refund of unspent balance reverses the liability; it never touches revenue. |
| escrow | settle (lock), work, settle (release) | At the lock: committed or restricted funds, not yet the seller's. At the release: revenue. Funds returned from escrow were never revenue at all. |

Three consequences the fleshed section must draw out:

- **A settlement is not always a sale, and a sale is not always a settlement.** Under upfront, the settlement event creates a liability, and the later consumption events are the sales. An x402 event can therefore be a payment record, a usage record, or both, and the record must say which it is (`OI-22`).  
- **The two legs need lineage.** Each consumption event must reference the prepayment it draws down; each delivery receipt must reference the payment it fulfills. The signed offer and receipts extension already in the protocol repo (amended by PR \#2811) is the natural carrier for the delivery leg (§4, §9).  
- **Reconciliation is an audit control, not housekeeping.** Books of record do not live on the chain itself. Established practice for on-chain activity is a scheduled reconciliation, daily at volume and per chain, comparing chain state to the books and classifying every difference as timing or permanent. Without that comparison there is no reliable record to show an auditor, only two systems that each insist they are right.

### 2.2 The consolidation seam

Two truths, both stated, no vendor names on either side:

- A facilitator can compute per-transaction tax and can keep the ledger for the transactions it settles. These are real, competitive services, and the protocol author has invited exactly this competition.  
- An enterprise's books span multiple facilitators, multiple rails (cards, stablecoins, bank transfers), and all of its non-agentic business. Consolidated books, revenue reporting, and filings live above any single venue. No CFO's quarterly report has an x402 microtransaction count; revenue reporting is protocol-agnostic.

The same seam holds for controls as for books: each rail and platform can see and govern only what happens inside its own walls, while the obligations run enterprise-wide. A monthly limit per counterparty across every rail, or a nexus threshold built from every channel's sales, is a policy no single rail can enforce or even observe (UC-4, UC-8). The document serves both layers and confuses neither: venue-level services need the fields in §4, and consolidation needs those same fields to survive the trip upward intact.

### 2.3 The use-case library

Each use case is a scenario, the questions it forces, and where those questions land. Companies in these scenarios are unnamed constructions unless a public source is cited. The library is meant to grow: members should add cases from their own operations, and regulatory requirements alongside them, so that the eventual field list is derived from situations the group has agreed are real rather than from anyone's architecture.

**UC-1 · Prepay and burn down.** A buyer settles $10.00 upfront to a tool marketplace. Over the week its agent consumes $8.00 across forty tool calls, tracked off-chain by the marketplace; the marketplace later refunds the unused $2.00. This pattern is live in the ecosystem today and publicly documented, including its own stated trust assumption: the buyer must trust the intermediary's bookkeeping of the burn-down. *Forces:* Which events here are payments and which are usage records (`OI-22`)? What does the seller's liability look like between settlement and consumption? What does the buyer hold, and how does either side prove the $8.00 without trusting the other's log? What makes the refund a liability reversal rather than negative revenue? → §2.1, §4, §9.

**UC-2 · The negotiated rate.** An endpoint lists a call at $2.00. A master services agreement entitles one buyer's principal to $1.00. The buyer's agent pays $2.00 because entity resolution completed only after settlement; reconciliation later discovers that, as of that date and time, the true contractual price was $1.00. *Forces:* Price truth is time-bound and agreement-bound: as of this moment, under this agreement, what price was valid? Where does the price book live when pricing decisions happen per request, in milliseconds, at the edge, and who owns it together with its contract terms (`OI-23`)? What is the correction path, and in which period does the correction land? → §4, §8.

**UC-3 · Paying on someone else's behalf.** A large agent platform negotiates preferential pricing with monetization gateways on behalf of its users and pays sellers directly, passing the cost through. The wallet that pays is the platform's; the party consuming the service is the end user; the platform may be acting in something like a fiduciary capacity. *Forces:* Who is the seller's customer? Who is taxed, the platform or the end user? What must the record carry so the payer role and the customer role stay distinct (`OI-24`)? The identity WG's role vocabulary (§3) already separates payer, principal, and custodian; this case shows why tax needs the same separation. → §3, §5.

**UC-4 · Twelve wallets, one buyer; one seller, many addresses.** A buyer entity operates a dozen wallets across its agents. The seller, for its part, rotates receiving addresses, a practice the TSC has discussed for exactly the confidentiality reasons in UC-9. Nexus thresholds, information-reporting thresholds, and MSA pricing tiers all run on entities, not addresses, and some run on aggregates: transaction counts, cumulative volume by jurisdiction, cumulative volume by counterparty. *Forces:* One payment of a few dollars triggers nothing; millions of dollars of them, aggregated by entity or by jurisdiction, trigger obligations. What must each record carry so aggregation is possible later, on both sides of a rotating address space (`OI-29`)? US thresholds are the worked example; every jurisdiction has its own trigger logic, and the international picture is an open question for reviewers. → §3, §7.

**UC-5 · The unknown payer.** A corporate wallet cannot refuse an inbound transfer; funds can simply arrive, from a counterparty the seller cannot resolve. Anti-money-laundering and know-your-customer obligations mean the seller may not be able to simply keep the money: the funds sit in suspense until the party is resolved, and the endings differ. Resolve the party: recognize revenue. Discover a party you are barred from transacting with: return the funds or, where law requires, hand them to the state as unclaimed property (escheatment). *Forces:* What does the protocol record need to carry to support a hold-and-resolve flow, and a disposition decision at the end of it (`OI-25`)? The protocol author's stated preference is to keep the payment experience as close to cash as possible, and for some flows entity resolution may genuinely be unnecessary; the requirement this case establishes is that skipping it must be an explicit, recorded choice, not a silent default (`OI-31`). → §3, §5.

**UC-6 · The free tier crosses into paid.** Persona A gives every buyer 10,000 free calls a month. Consumption under the free tier is not a payment, but it must be recorded, because the meter that decides when call 10,001 becomes billable is an accounting record that exists before any money moves. *Forces:* Are consumption-only records in or out of the ecosystem's scope (`OI-28`)? If out, how does anyone later verify that billing started at the right call? → §2.1, §4.

**UC-7 · The chargeback and the revoked attestation.** Persona B takes a card-rail sale in Q3; the buyer charges it back in Q4. Separately, an attestation Persona A relied on at settlement is revoked in September, effective back to August, after weeks of calls settled in reliance on it. *Forces:* Everything in §8: per-rail finality, dispute states, the subsequent-events doctrine, and the period question, including what happens to tax already remitted. The protocol repo has already taken a compatible position: PR \#2811, merged July 23, says that when a receipt is verified after the payment session, signer authorization is evaluated as of the receipt's issuance time, because mutable sources do not give durable historical evidence after key rotation. That is the subsequent-events doctrine, already in the spec's own words. → §8, §9.

**UC-8 · A budget with tax inside it, or not.** A mandate authorizes an agent to spend no more than $100. In the US, sales tax is added on top of a list price; in much of the world, tax is baked into it. The upto pattern advertises a ceiling that includes worst-case tax and settles the actual. *Forces:* Is the $100 tax-inclusive or tax-exclusive, and can the agent even know at authorization time (`OI-26`)? How do budget semantics, upto ceilings, and jurisdiction-dependent price presentation interact so that a control means the same thing everywhere? And when the budget spans every rail the enterprise uses, where can it even be evaluated (§2.2)? → §4, §5.

**UC-9 · The payment log is a strategy log.** An observer watching a seller's fixed receiving address can read its revenue and customer concentration from the chain. An observer watching a buyer's payment history can read its agent's strategy: which APIs, when, in what order. A publicly documented ecosystem experiment (SubEtha, using a burn-and-mint privacy token) hides who paid whom while leaving amounts visible, precisely to close this leak. *Forces:* Privacy here is business confidentiality, not only personal data. And it lands in direct tension with UC-4: if who-paid-whom is hidden on the wire, entity-level aggregation for nexus, reporting, and MSA pricing must happen somewhere off the wire, through the token and attestation layer. Reconciling those two requirements is one of this document's hardest open issues (`OI-29`). → §6, §7.

**UC-10 · The agent that paid its own principal.** A seller accepts x402 payments. An engineer inside the same company deploys a buying agent that, in the course of its work, purchases the company's own API. The settlement is real; the revenue is not. Related-party transactions must be identified and eliminated or disclosed, not booked as ordinary sales. Booked unknowingly, this is a misstatement; booked knowingly, it is fraud; and under Sarbanes-Oxley the signing officers attest personally to the controls that were supposed to catch it. The auditor's version of the same worry is round-tripping, money spent on one side of the house reappearing as revenue on the other, and its cousin is the wash-trade pattern made famous in NFT markets. *Forces:* Entity resolution is not only about strangers; the record must make it possible to recognize yourself. What must a record carry for related-party detection: corporate-family identifiers, principal linkage across buy-side and sell-side agents (`OI-32`)? Which relying party checks it, and when? → §3, §9.

**Open issues.** `OI-2` recording grain for micro-metered sales. `OI-3` retention expectations for settlement-level detail beneath the books' grain. `OI-22` explicit record typing (payment, usage, both). `OI-28` consumption-only records.

**Owner:** Tim drafts; Stripe review for stage-1 realism; every member invited to add use cases and regulatory requirements to the library.

---

## 

## 3\. The identity dependency: a wallet is not an entity

**Purpose.** State this document's dependency on the identity working group precisely, and consume its output rather than solving it. Tax needs jurisdiction. Accounting and information reporting need the legal entity. A wallet address is neither, and address behavior in the wild (many wallets per buyer, rotating addresses per seller) makes the gap wider, not narrower.

**Questions this section answers.** Which of the five questions require entity resolution? What is the minimum identity payload a bookable, taxable record needs? Who is the counterparty on the invoice, and on the information return, when several parties touch one transaction? When is not resolving the entity an acceptable, explicit choice?

**Seeds.**

- Established in the WG, and conceded by the protocol author: knowing the on-chain address is not knowing the counterparty. Correlating address → entity → authorized agent is the identity WG's problem. This document states the dependency and consumes the result.  
- The identity WG's emerging role model, adopted here as vocabulary: **principal** (the liable party), **operator or platform** (runs the agent), **agent instance**, **counterparty endpoint**, plus **funds custodian** (an FBO-style holder) as a distinct party when present. UC-3 adds the **third-party payer**: the party whose wallet pays may be none of the above, and the customer record must not collapse into the payment record.  
- A customer record at the moment of payment does real work: it is what keeps self-dealing from being booked as revenue, and it is what regulated industries mean by knowing the customer. UC-10 shows the full family this guards against: related-party payments, round-tripping, and wash-trade patterns. Detecting any of them requires the record to reveal when payer and payee resolve into the same corporate family (`OI-32`).  
- Entity resolution may be legitimately skipped in some flows, consistent with the protocol author's stated preference for keeping payments close to cash. The requirement is that the skip is explicit and recorded, so a reviewer can distinguish a considered choice from a gap (`OI-31`, UC-5).  
- Three axes, kept separate: identity (who this is), verified attributes (what a registry says about them, §7), authority (what they may do).  
- Standing authority (a mandate) is different from per-transaction **authorization**: a decision made at the moment of payment against budget remaining, attestation validity, and the policy version in force. The authorization record is itself evidence (§9), not only a gate. Existing payments law already demands this shape in places: US rules in the Reg E family require explicit authorization and a supporting audit trail for consumer electronic transfers, and agent-initiated payments raise the same question, who authorized this, with the human one step further away. Jurisdiction portability flag.  
- The identity landscape is plural and moving. The TSC has adopted a v1 reputation-and-identity approach in which an agent declares an ERC-8004 identity in the 402 response and signs its responses as a commitment to service delivery (PR \#1024 discussion), with a higher-trust on-chain tier possible later. Registry-style attestation frameworks exist alongside it. This document's requirements must hold regardless of which identity mechanism wins: whatever resolves, the tax and accounting layer needs an entity reference, the roles present, and a pointer to the authorization.  
- Pay-to address rotation (raised in the TSC) means even the seller side of attribution is unstable at the address level; attribution must key on entities and declared relationships, not addresses.

**Open issues.** `OI-4` Requirements letter from this WG to the identity WG: the minimum payload tax and accounting need (entity reference, roles present including third-party payer, authorization reference, attribute pointers, corporate-family linkage). Possibly an appendix. `OI-5` Records where entity resolution completes after settlement: confirm the §8 doctrine covers this case (UC-2 shows the price consequence). `OI-31` Entity resolution as an explicit, per-flow choice: who declares it, where it is recorded. `OI-32` Related-party identification across buy-side and sell-side agents.

**Owner:** Tim; identity WG cross-review.

---

## 

## 4\. Required information at transaction time

**Purpose.** The heart of the document. Enumerate what must exist in or around the transaction, at the moment it happens, for the money to be bookable and taxable, and for the record to hold up later. This is the seed of the common extension spec the protocol author called for. Organized by the five questions rather than as a flat schema; canonical field names and wire format are explicitly deferred.

**For each item, the fleshed section states:** which question it answers · where it can live (on the wire, in an extension, as a token or reference resolvable later, off-protocol with a pointer) · its privacy stance (§6).

**Seeds.**

- **Who paid you:** entity reference and roles including third-party payer where present (§3, UC-3), authorization reference, attribute pointers (§7), counterparty tax character where it changes treatment (taxable business, nonprofit, government; UC-5 and Annex A), and corporate-family linkage where it exists (UC-10).  
- **What kind of event this is:** payment settlement, usage record, or both (UC-1, UC-6), plus the declared paymentFlow (authorize, upfront, escrow) so downstream systems know which bookkeeping shape applies without inferring it (`OI-22`).  
- **What settled, exactly:** the settlement asset itself, not just an amount. What arrives may not be cash on the books: pending US standard-setting (an exposure draft the reviewers should cite precisely) conditions cash-equivalent treatment for stablecoins on a direct redemption right against the issuer, so a holder whose redemption runs only through an intermediary holds a financial asset, not cash. And a stablecoin is not automatically a dollar: measurement needs a source for the moments an asset trades off its peg. Classification and measurement are the books' problem, but only the record can carry the facts they depend on (`OI-33`).  
- **What price was true:** the upto pattern advertises a ceiling including worst-case tax and settles the actual, so one transaction legitimately carries several true amounts: listed price, contractual price under the governing agreement, authorized ceiling, settled total, tax component, net to seller. UC-2 adds the contract dimension: an agreement reference, a price basis (list, negotiated, tiered), and an as-of timestamp, so that "what price was true" is answerable years later without access to anyone's live price book (`OI-23`).  
- **What do you owe:** the missing primitive is a way to declare tax geography. The naive answer, a billing address carried as an extension, puts personal information on the wire and is rejected (§6). The stated direction is **tokenized taxation**: a token redeemable with a trusted third party for tax jurisdiction. The record stores the token reference and the jurisdiction outcome used at computation time. Records must also support later aggregation by entity and by jurisdiction, because nexus and reporting thresholds run on aggregates (UC-4, §7).  
- **What did you record, and what proves it:** rail identifier and finality class (§8), event and settlement timestamps, facilitator reference, lineage references between payment legs and delivery legs (§2.1), and the computation inputs preserved (which rate, which jurisdiction claim, which policy version), so the number can be re-derived later by someone who was not there.  
- **The delivery leg has a carrier.** The signed offer and receipts extension in the protocol repo delivers a cryptographic receipt binding the delivered service to the payment made, and PR \#2811 already governs how its signer authorization is evaluated after the fact. The fleshed section should treat the receipt as the delivery-side twin of the settlement record rather than inventing a parallel artifact.  
- 1099-DA-style exports exist for digital-asset legs, and stable-to-stable settlement avoids capital-gains noise on the transfer itself. What remains open even on the "solved" leg is classification and measurement (above), consolidation across rails (§2.2), and entity-level reporting (§5.x).

**Worked-example stubs.** One complete record per persona, shown field by field (annotated pseudocode acceptable). Persona B's record shows upto with the contract dimension from UC-2. Persona A's record shows grain plus record typing: a prepaid settlement, a stream of usage records drawing it down, and the aggregation keys that make UC-4 answerable.

**Open issues.** `OI-6` Required versus recommended fields. `OI-7` Canonical schema and naming: defer to the extension-spec process; this document defines semantics. `OI-8` Where tax-inclusive versus tax-exclusive pricing is declared, and how it interacts with budget semantics (`OI-26`, UC-8). `OI-9` When a facilitator computes tax as a service, how its computation inputs are preserved in the seller's own audit trail. `OI-27` Whether records of refused payments (a payment blocked by policy before settlement) are in ecosystem scope; auditors test that controls fired, and a control that leaves no record when it fires cannot be tested. `OI-33` Settlement-asset classification and measurement: cash-equivalence conditions, de-peg pricing sources; reviewers to confirm the current standard-setting citation.

**Owner:** Tim drafts; Stripe tax specialists review the field list. This is the section that most needs WG iteration.

---

## 

## 5\. The obligation boundary: who owes what, and who must never accidentally owe

**Purpose.** Make it unambiguous where tax and merchant-of-record obligations sit, so that parties who enforce or route payment never accidentally inherit obligations they did not take on, and so that sellers never assume an obligation moved when only a computation did. This section gives an analysis framework, not legal advice.

**Questions this section answers.** Which party in a given flow is the merchant of record? What creates a collection or filing obligation? What does a party have to do, and avoid doing, to remain a conduit? When a payer is not the customer, who carries the tax?

**Seeds.**

- The requirement, stated by reviewers in the WG (Steve Kaliski): protocol design must keep obligation placement unambiguous. Context: edge gateways that meter access and enforce payment are deliberately not in the flow of funds; the design intends them to carry no merchant-of-record status, and the analysis here should show why that holds.  
- Working principle: obligation follows whoever controls the receiving relationship. Who is the buyer's money owed to, who sets the price, who would the buyer pursue if the goods never arrived. The section turns this into a factor test (flow of funds · contractual relationship with the buyer · price control · receiving relationship) that each party can run on itself.  
- The inverse warning, for sellers: a facilitator computing your tax has not assumed your tax. Computation is a service; obligation moves only by contract, through an explicit merchant-of-record arrangement, and the record should state which situation holds.  
- Pass-through payers (UC-3) get their own analysis: a platform paying on behalf of its users at negotiated rates may be a customer, an agent of the customer, or a reseller, and the three have different tax outcomes. The document should give the questions that distinguish them, and flag the answer as jurisdiction-dependent (`OI-24`).  
- Unknown counterparties (UC-5) create obligations of a different kind: funds that cannot be attributed may have to be held, returned, or escheated to the state, and anti-money-laundering rules can bar recognizing them at all. The record's job is to make the hold-and-resolve path, and the eventual disposition decision, possible (`OI-25`).  
- Custody itself is jurisdiction-constrained: some virtual-asset regimes restrict or condition the use of unhosted wallets, which shapes who can even hold the receiving relationship in those   
- markets. Portability flag; reviewer input.

**5.x Information reporting mechanics.** Who files what about whom: 1099-DA for digital-asset legs; 1099-K and 1099-NEC-style analogues where they apply; thresholds and aggregation run on the entity, not the wallet (§3, UC-4); the paying side mirrors the receiving side (who did you pay, at what address); nonprofit acknowledgment edge cases (Annex A). Portability flag: the US forms are worked examples of a general class; every jurisdiction has its own information-reporting regime. **Owner of 5.x: Joseph.**

**Worked-example stubs.**

- B sells through a gateway: run the factor test. The gateway fails every factor, so it is a conduit; B is the merchant of record.  
- A buys a facilitator's tax-computation service: the factor test is unchanged, so A still files. Contrast stub: an explicit merchant-of-record arrangement, and exactly what changes in the record when one exists.  
- The UC-3 platform: run the same factor test three ways (customer, agent, reseller) and show how the record distinguishes them.

**Open issues.** `OI-10` Should the extension spec carry an explicit merchant-of-record declaration field? `OI-11` US state marketplace-facilitator statutes applied to x402 facilitators: reviewer memo requested. `OI-12` Cross-border merchant-of-record variation. `OI-24` Pass-through payer taxation. `OI-25` Suspense, return, and escheatment mechanics for unresolved counterparties.

**Owner:** Tim frames; lawyer review; Stripe input.

---

## 

## 6\. Privacy architecture: nothing on the wire that should not be public forever

**Purpose.** Hold two requirements at once and refuse to trade one for the other: the record must be sufficient for compliance, and the wire must not leak what should stay private. The section shows the two are reconcilable, by proving claims with tokens and references that the right party can resolve later, instead of shipping raw data.

**Questions this section answers.** What must be provable at transaction time versus resolvable later? Who holds the mapping from token to person or entity, and who may resolve it, under what process? How does confidentiality coexist with the aggregation that tax and reporting require?

**Seeds.**

- The WG-supported principle (Ken Fromm): no personally identifying information on the wire, at the protocol level; disclosure happens through selective, tokenized mechanisms at higher layers.  
- Why the bar sits higher here than on card rails: much of this settles on chains that never forget. A billing address in an extension is not a leak risk; it is a permanent publication.  
- Privacy here is also business confidentiality, not only personal data (UC-9). A fixed receiving address publishes a seller's revenue and customer concentration; a buyer's payment trail publishes its agent's strategy. Ecosystem experiments already respond to this: one publicly documented project settles x402 payments through a burn-and-mint privacy token so that who-paid-whom leaves no on-chain link, while amounts stay visible. Sellers rotate receiving addresses for related reasons.  
- The tension this creates is this document's to name, not to dodge: nexus, information reporting, and MSA pricing all require entity-level aggregation (UC-4), and confidentiality tech removes the on-chain data those aggregates were naively going to come from. The resolution direction is the same as tokenized taxation: aggregation happens off the wire, through attestations and references that authorized parties can resolve, rather than through public chain analysis (`OI-29`).  
- Flagship pattern: the jurisdiction token (§4). The wire carries a claim reference; the trusted issuer holds the mapping; the seller learns what tax needs ("a 20 percent VAT country," "US-CA") without an address; an authority with proper legal process can resolve further.  
- The disclosure ladder, drafted as a table: what the wire carries → what the counterparty learns → what the books hold → what an auditor or tax authority can reach, with process.

**Open issues.** `OI-13` Who qualifies as a trusted issuer for jurisdiction tokens (accreditation, plurality of issuers, or both). `OI-14` Retention and deletion rules for the off-wire mappings versus record-keeping obligations; data-protection interplay. `OI-29` Reconciling who-paid-whom confidentiality with entity-level aggregation. Reviewer input: lawyer, Coinbase legal.

**Owner:** Tim seeds; the principle's WG proponent is the natural reviewer.

---

## 

## 7\. Jurisdiction and nexus: portable attributes, mobile agents, aggregate exposure

**Purpose.** Answer where obligations arise when the actor is an agent whose location is a configuration choice, and make the attribute layer jurisdiction-portable from the start rather than US/EU-shaped with everyone else bolted on.

**Questions this section answers.** Whose location matters for which tax: seller entity, buyer principal, agent runtime, custodian, third-party payer? What must the record carry so a nexus question can still be answered years later? What counts as a verified attribute in jurisdictions this WG has not met yet?

**Seeds.**

- Verified attributes are jurisdiction-specific, and the attribute layer must carry registry-backed claims from any jurisdiction: a US EIN, an EU eIDAS credential, and a Colombian NIT with its Cámara de Comercio record are the same kind of thing. The spec should define the class, not enumerate the instances.  
- Nexus is an aggregate property, not a per-transaction one (UC-4). A single small payment triggers nothing; cumulative transaction counts or cumulative volume into a jurisdiction, or from a single counterparty entity, cross thresholds that change the seller's obligations. Triggers vary by regime: some count transactions, some count dollars, some both. The per-transaction record's job is to carry the keys (entity, jurisdiction, amount, timestamp) that make the aggregate computable later. The US threshold mechanics are the worked example; international trigger logic is explicitly flagged for reviewer input.  
- Bill-to address is the working anchor for location in most commercial systems today; the legal weight of agent runtime location versus customer location is unresolved and framed neutrally (`OI-15`).  
- Nexus scenarios to work through: (a) an agent "domiciled" in a low-tax jurisdiction doing business for a principal elsewhere, the IP-licensing arbitrage pattern restated for agents; (b) consumption-tax exposure on cross-border digital sales, the buyer side of B's business; (c) the UC-3 pass-through, where the payer's jurisdiction and the consumer's jurisdiction differ.  
- Regulatory engagement noted in the WG: sandbox interest from MAS (Singapore) and the UK. Track as an engagement path, not a scope expansion; the UK's broader posture now has a concrete anchor (§9).

**Worked-example stubs.** B's sale into a VAT country (consumption tax follows the consumer). A's buyer agent hosted in a third country for a principal in a fourth: walk which locations enter the analysis at all, and which never do. A's year-end nexus review: reconstructing jurisdiction aggregates from records under rotating addresses.

**Open issues.** `OI-15` Agent runtime location: ever tax-relevant? Frame both positions neutrally; legal input. `OI-16` Attribute-registry coverage beyond US/EU: open call for jurisdiction contributions.

**Owner:** Tim frames the scenarios; Joseph takes the reporting angles; legal review.

---

## 

## 8\. Finality, reversals, and subsequent events

**Purpose.** Define what happens to a record after settlement. Card acceptance gives x402 rails that become final in different ways; the paymentFlow work means settlement can precede delivery, which changes what a reversal even is; and attestations relied on at settlement can later be revoked. One doctrine governs the section: records are appended to, never rewritten.

**Questions this section answers.** What does "final" mean, per rail and per flow? How do refunds, chargebacks, and revocations enter the books, and the filings, without falsifying history? Which accounting period does an after-the-fact event belong to?

**Seeds.**

- **Mixed finality.** Crypto legs settle final: when the transfer clears there is no window in which it can be pulled back. Card legs carry chargeback and reversal windows measured in weeks. The record therefore needs a per-rail finality class and a dispute lifecycle (settled → disputed → reversed or upheld), so downstream systems know which records can still move. Finality class is a transaction-time field (§4).  
- **Flow-aware reversals.** Under authorize, a reversal claws back earned revenue and already-remitted tax (UC-7's chargeback). Under upfront, returning unspent balance is a liability reversal that never touched revenue (UC-1's refund). Under escrow, funds returned from the lock were never the seller's. The same word, refund, names three different accounting events, and the record's flow and type fields (§4) are what keep them distinct.  
- **Subsequent-events doctrine**, accounting's existing answer applied to protocol evidence: when an attestation relied upon at settlement is later revoked, the settlement record is not retroactively false. It preserved what was known and believed at commit time. The revocation is appended as a flagged subsequent event with its own timestamp, and its consequences (a reversal, a claim, a dispute) reference both records. Accounting has had doctrine for events-after-the-period for a century; this document applies it rather than inventing a replacement.  
- The protocol repo already agrees. PR \#2811 (merged July 23\) amended the offer-and-receipts extension to separate signature validity from signer authorization, and to state that when a receipt is verified outside the payment session, for reputation, audit, or dispute, authorization is evaluated as of the receipt's issuance time, because mutable sources do not give durable historical evidence after key rotation. That is the as-of-commit-time principle in the spec's own words; the fleshed section should cite it and generalize it: it also answers what happens when entity resolution completes after settlement (`OI-5`) and when a price is corrected after the fact (UC-2).  
- The period question: a Q3 sale charged back in Q4. Which quarter takes the revenue reversal, and what happens to tax already remitted. Reclaim and adjustment mechanics differ by regime; portability flag.

**Worked-example stubs.**

- B: Q3 card sale, Q4 chargeback. The reversal entry, the remitted-VAT reclaim, and the dispute states walked end to end.  
- A: an attestation revoked in September, effective back to August, after weeks of calls settled in reliance on it. The settled records stand as written; the subsequent-event flag propagates; the section shows what an auditor sees when reading the trail.  
- A, upfront variant: a buyer demands its unspent prepaid balance back mid-month. No revenue moves; the duty-to-deliver liability shrinks; the record shows why.

**Open issues.** `OI-17` A standard vocabulary for dispute states across rails and flows. `OI-18` Finality class: required or recommended in §4? `OI-19` Treatment of reversals that span filing periods, across regimes.

**Owner:** Tim (lead-authored). Card-acceptance WG cross-review.

---

## 

## 9\. Audit and insurability: evidence, and the parties who rely on it

**Purpose.** Complete the ladder the WG adopted: **bookable → taxable → auditable → insurable**, each rung standing on the one below. Name the relying parties beyond the taxpayer (auditors, insurers, dispute forums, and now regulators) and specify what each one relies on. Position adjacent specs and the TSC's own evidence-record work as cross-references, not absorbed scope.

**Questions this section answers.** What does an auditor test when the actor has no culture to test? What must exist for an underwriter to price agent risk at all? Where do the adjacent layers plug in, and what does this working group owe the TSC's evidence-record effort?

**Seeds.**

- The ladder, one line per rung: bookable (a correct entry can be made) → taxable (obligations can be determined) → auditable (a third party can verify later without trusting the teller) → insurable (a stranger will price the risk and pay claims on the strength of the artifacts).  
- What the auditable rung is for, in one line: when money is misallocated, the question an investigation asks is innocent error or something worse, and the provenance the record carries is what makes that question answerable quickly instead of forensically. UC-10 is the sharpest case: proving revenue is not your own money coming back requires counterparty evidence written at transaction time.  
- Public precedent that underwriters price artifacts: Amex's Agent Purchase Protection (April 2026\) covers only registered agents transmitting authenticated intent. The evidence a transaction carries determines whether its risk is transferable at all.  
- **Auditors are relying parties and prospective participants, and their participation has a precondition.** Professional independence rules limit how an audit firm engages with a body dominated by its own audit clients, so a public governance roster and neutral foundation stewardship are not just hygiene: they are what makes audit-firm participation clearable at all. The foundation's Linux Foundation structure is an asset here and worth stating in the document.  
- **The TSC has a live evidence-record effort, and this document is its requirements source.** The post-settlement accountability proposal now on the TSC agenda (tsc\#4) scopes phase one to a portable, independently verifiable record of what was authorized and what settled, explicitly settlement-flow-agnostic, with adjudication and enforcement downstream and out of scope. Independent implementations are converging on a canonical record form (RFC 8785 / JCS) with conformance vectors. The division of labor is clean and should be stated: the TSC effort defines the record's shape and verification; this working group defines what the record must be able to answer for tax, accounting, audit, and insurance. Requirements flow from here to there (`OI-30`).  
- **The regulatory clock is external and public.** HM Treasury's Financial Services AI Adoption Plan (July 14, 2026), Recommendation 10, asks industry at high priority to build a trust framework for agentic payments, with an accountability record at its base. The EU AI Act's Article 26 places log-custody duties on deployers. Neither names this protocol; both describe the artifact this document specifies.  
- Adjacent layers, cross-referenced rather than absorbed: the signed offer and receipts extension (delivery evidence, already in the repo, governed after the fact by PR \#2811) answers what was delivered against what was paid; LCP (the AAA-backed terms-acknowledgment extension) answers what was agreed; AEP (the A-Comm Evidence Protocol) shapes the evidence bundle; multi-party approval protocols in the ecosystem (open-source MPAS, contributed to OMA3) show how several keyed gates can each sign the exact command they approved, a class of authorization evidence the record should be able to reference; dispute-resolution forums consume all of it, downstream.  
- Refused payments revisited (`OI-27`): a control that blocks a payment and leaves no record cannot be shown to have fired. Whether refusal records are protocol scope, extension scope, or purely off-protocol is an open question this group should put to the TSC rather than assume.

**9.x Arbitration and dispute-forum adjacency.** What makes protocol evidence usable in an AAA-style forum; the relationship between the transaction record, the delivery receipt, the evidence bundle, and admissibility. **Owner of 9.x: Joseph.**

**Worked-example stubs.** A's revoked-attestation event (§8) read as an insurance claim: which artifacts the underwriter examines, in what order. B's chargeback read as a dispute-forum case: record, terms acknowledgment, delivery receipt, evidence bundle. UC-1's burn-down read as an audit test: proving $8.00 of consumption against $10.00 of prepayment without trusting the intermediary's log. UC-10 read as an audit test: demonstrating the absence of related-party revenue from the records alone.

**Open issues.** `OI-20` A minimum evidence bundle per transaction class, and the division of labor between this document's §4 fields and AEP's evidence tiers. `OI-21` Admissibility expectations from dispute forums: Joseph to frame. `OI-30` Coordination with the TSC evidence-record effort: requirements handoff, canonical form, conformance. `OI-34` Legal-review coverage beyond tax counsel: recruit law practices at the digital-assets and AI intersection into WG review.

**Owner:** Tim (ladder, relying parties, TSC coordination); Joseph (9.x).

---

## 

## 10\. Open-issues register and glossary seed

**Purpose.** One table for every open issue above, each with a neutral framing of the options and a suggested resolution path. Plus the glossary the preamble points to.

### Open-issues register

| ID | § | Question | Suggested path |
| :---- | :---- | :---- | :---- |
| OI-1 | 0 | Is "insurable" part of the eventual normative field list, or descriptive context? | WG discussion |
| OI-2 | 2 | Recommend a recording grain for micro-metered sales, or leave to sellers with trade-offs stated? | WG discussion |
| OI-3 | 2 | Retention expectations for settlement-level detail beneath the books' grain | Reviewer memo (accounting) |
| OI-4 | 3 | Requirements letter to the identity WG: minimum identity payload, including third-party payer and corporate-family linkage | Draft as appendix; identity WG review |
| OI-5 | 3 | Entity resolution completing after settlement | Covered by §8 doctrine per PR \#2811 framing; confirm with identity WG |
| OI-6 | 4 | Required vs. recommended fields | Extension-spec process |
| OI-7 | 4 | Canonical schema and naming | Defer to extension-spec process |
| OI-8 | 4 | Tax-inclusive vs. tax-exclusive price declaration | WG discussion (with OI-26) |
| OI-9 | 4 | Preserving a facilitator's computation inputs in the seller's own trail | Reviewer input (Stripe tax) |
| OI-10 | 5 | Explicit merchant-of-record declaration field? | WG discussion |
| OI-11 | 5 | US marketplace-facilitator statutes vs. x402 facilitators | Legal memo |
| OI-12 | 5 | Cross-border merchant-of-record variation | Reviewer memo |
| OI-13 | 6 | Who qualifies as a trusted issuer for jurisdiction tokens | WG discussion |
| OI-14 | 6 | Mapping retention/deletion vs. record-keeping obligations | Legal \+ Coinbase legal |
| OI-15 | 7 | Is agent runtime location ever tax-relevant? | Legal input; neutral framing |
| OI-16 | 7 | Attribute-registry coverage beyond US/EU | Open call for jurisdiction contributions |
| OI-17 | 8 | Standard dispute-state vocabulary across rails and flows | Card-acceptance WG coordination |
| OI-18 | 8 | Finality class: required or recommended field? | Extension-spec process |
| OI-19 | 8 | Reversals spanning filing periods, across regimes | Reviewer memo (tax) |
| OI-20 | 9 | Minimum evidence bundle per transaction class; division of labor with AEP tiers | WG \+ AEP coordination |
| OI-21 | 9 | Dispute-forum admissibility expectations | Joseph to frame |
| OI-22 | 2, 4 | Explicit record typing: payment settlement, usage record, or both; paymentFlow declared on the record | WG discussion → extension spec |
| OI-23 | 4 | Price-truth fields: agreement reference, price basis, as-of time; who owns the price book | WG discussion; Stripe input |
| OI-24 | 5 | Third-party payer and pass-through arrangements: who is the customer, who is taxed | Legal memo |
| OI-25 | 5 | Unresolved-counterparty funds: suspense, return, escheatment; what the record must carry | legal \+ reviewer input |
| OI-26 | 4 | Budget and mandate semantics: tax-inclusive or tax-exclusive; interaction with upto ceilings | WG discussion (with OI-8) |
| OI-27 | 4, 9 | Records of refused payments: protocol scope, extension scope, or off-protocol | Put to the TSC |
| OI-28 | 2 | Consumption-only records (free tier, prepaid burn-down) before or without settlement | WG discussion |
| OI-29 | 6, 7 | Reconciling who-paid-whom confidentiality with entity-level aggregation for nexus and reporting | WG discussion; identity WG \+ privacy proponents |
| OI-30 | 9 | Coordination with the TSC post-settlement evidence-record effort (tsc\#4): requirements handoff, canonical form | Tim to carry to the Aug 19 TSC; ongoing |
| OI-31 | 3 | Entity resolution as an explicit, declared per-flow choice | WG discussion |
| OI-32 | 3, 9 | Related-party identification: what the record must carry so payments inside one corporate family are detectable | WG discussion; identity WG (principal linkage) |
| OI-33 | 4 | Settlement-asset classification and measurement: cash-equivalence conditions, de-peg pricing; confirm current standard-setting citation | Reviewer memo (Coinbase \+ Stripe accounting) |
| OI-34 | 9 | Legal-review coverage beyond tax counsel: recruit digital-assets and AI law practices | WG chairs \+ LF ops |

### Glossary seed

Each term gets one line stating its job, not its etymology.

- **facilitator**: the service that settles an x402 payment and can offer tax and record-keeping services around it  
- **gateway**: infrastructure that meters access and enforces payment without holding the funds  
- **merchant of record**: the party legally selling to the buyer, and therefore the one who owes tax collection and customer obligations  
- **principal**: the legal person liable for what an agent does  
- **operator / platform**: the party that runs the agent  
- **agent instance**: the specific running agent that acted  
- **counterparty endpoint**: the address or service actually transacted with  
- **funds custodian**: a party holding money for the benefit of another (FBO), present in the flow without being the customer  
- **third-party payer**: a party whose wallet pays for a transaction consumed by someone else (a platform paying for its users)  
- **related-party transaction**: a payment between entities under common ownership or control, which the books must eliminate or disclose rather than count as ordinary revenue  
- **mandate**: standing permission to act  
- **authorization**: the per-transaction yes, decided against budget remaining, attestation validity, and the policy in force at that moment  
- **verified attribute**: a claim about an entity backed by a registry (an EIN, an eIDAS credential, a NIT)  
- **jurisdiction token**: a reference redeemable with a trusted issuer for tax geography, so the wire never carries an address  
- **paymentFlow**: the declared timing of settlement relative to the work: authorize (work then settle), upfront (settle then work), escrow (lock, work, release)  
- **usage record**: an x402 event that records consumption of goods or services rather than a movement of money  
- **settlement asset**: the thing that actually arrived (a stablecoin, a card credit, bank money); its classification and measurement on the books are separate questions from the amount  
- **deferred revenue**: the liability a seller books when paid before delivering; a recorded duty to deliver  
- **suspense**: the holding place for funds that cannot yet be recognized because the payer or purpose is unresolved  
- **escheatment**: the legal handover of unclaimed or unattributable funds to the state  
- **delivery receipt**: the signed artifact binding a delivered service to the payment that funded it  
- **price book**: the set of list, negotiated, and tiered prices, with their governing agreements, that determines what price is true at a moment in time  
- **finality class**: how, and whether, a settled payment can still be pulled back on its rail  
- **subsequent event**: something learned after settlement, appended to the record rather than rewritten into it  
- **nexus**: the connection, often built from aggregates, that gives a jurisdiction the right to tax  
- **bookable / taxable / auditable / insurable**: the four rungs defined in §9  
- **upto**: the protocol's price pattern; advertise a ceiling, settle the actual  
- **grain**: the unit at which events become records (per call, per settlement, per day)

**Owner:** Tim assembles; every contributor feeds it.

---

## 

## Annex A (proposed, stub): edge-case studies

First entry: a nonprofit receiving agent-initiated donations. Receipt and acknowledgment requirements, donor entity resolution when the giver is an agent, fundraising-platform roles in the flow, tax character of the receiving entity (§4), and unrelated-business-income questions. **Owner: Joseph.**

