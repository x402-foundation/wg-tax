# The Obligation Matrix: Intake Format for Obligation-Driven Requirements

**Status:** Session 2 pre-read. Decision 2 proposes adopting this as the standard intake for obligation-driven requirements, implemented as the Requirement issue template in this repository.

## What it is

A requirement that exists because the law requires something must arrive with its obligation fully described, so the group can decide placement without re-arguing whether the obligation is real. The matrix is seven questions. Answering them is the work; the answers become the issue.

| Dimension | The question |
| :-- | :-- |
| **Trigger** | What fact pattern activates the obligation? (Actor role, transaction type, amount, aggregation history, documentation state, jurisdiction.) |
| **Responsible party** | Who must determine, report, withhold, retain, or act? |
| **Required facts** | What must that party know to comply? |
| **Timing** | When must they know it: before authorization, before settlement, at settlement, or after? |
| **Evidence** | What must be retained or verifiably referenced, and by whom? |
| **Missing-data treatment** | If the facts are absent at the required time: withhold, hold, route, block, permit, or record as unresolved? The chosen treatment is recorded with the transaction. |
| **Protocol placement** | Core anchor capability, profile requirement, extension, or off-protocol reference? |

Every submission also identifies the **primary authority** (statute, regulation, or rule, with citation), the **jurisdiction**, and the **effective date**. Law in force qualifies; predicted law goes to an experimental profile. Profile mandate and core promotion are separate decisions: a profile may require something the day the obligation applies, while core promotion turns on cross-profile interoperability need.

## Worked example: backup withholding

Illustrative, for discussion; the filed Requirement issue carries the full analysis and gets practitioner review before any normative text is proposed.

| Dimension | Backup withholding |
| :-- | :-- |
| **Trigger** | A reportable payment to a payee whose taxpayer identification or documentation is missing, unverified, or subject to an IRS notice. |
| **Responsible party** | The payor: the business deploying the buying agent, or its withholding agent. |
| **Required facts** | Payee documentation state (W-9 or equivalent on file, or not), payment reportability, gross amount, payee status. |
| **Timing** | Before settlement. Once a final, irreversible transfer clears, the withholding option is gone. |
| **Evidence** | The documentation state as checked at decision time, the treatment chosen, and any amount withheld, recorded with the transaction. |
| **Missing-data treatment** | Withhold (24% of gross), hold pending documentation, route to a documented-payee flow, or block. Never silently permit. |
| **Protocol placement** | Profile requirement (US information-reporting profile). The core needs only anchors: a payee party reference, amount, time, and a place to record the treatment. |
| **Primary authority** | IRC §3406; law in force. |

Why this example matters: the obligation cannot be met after the fact. A payor that settles without resolving the documentation state has already self-assessed a 24% liability plus penalties. The control is preventative by legal construction, not by design preference, which is exactly the class of requirement this working group exists to surface.

## Why this format

It converts the recurring need-versus-want argument into a case-by-case determination with an authority and an effective date attached, instead of a philosophical dispute. It keeps compliance logic out of the core while making the anchors the core must carry derivable rather than asserted. And where the facts don't support a determination, the intake records the state as unresolved rather than defaulting, so an open question never masquerades as a settled one.
