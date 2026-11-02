# CloudLab Works security and AI governance policy

| | |
| --- | --- |
| Owner and approver | Alex Alvord, founder |
| Applies to | Cloud Lab Works LLC (CloudLab Works) and its open source products (first in scope: oak-events) |
| Status | Approved by Alex Alvord and published 2026-11-02. |
| Review | At least once a year (next by 2027-11-01) and after any significant change (section 4) |

## 1. Purpose and scope

CloudLab Works is a one-person Washington LLC founded by Alex Alvord. It has no employees, runs no hosted
service, holds no customer data and trains no models. It publishes open source software, including
oak-events, which runs a person detector on an OAK camera and gives AI agents text events, never pictures.

This policy sets who decides what, which documents make up the security and AI governance program, how they
are reviewed, how exceptions work, which laws are tracked, and the acceptable use of oak-events. Items marked
**Commitment** are not done yet; each has a target date.

## 2. Roles

Alex Alvord holds every role: security owner, AI governance owner, product owner, incident lead, policy
approver and data protection contact. AI assistants help draft code and documents; they hold no role.

**Substitute controls for a one-person company**

| Missing control | Substitute |
| --- | --- |
| Second reviewer | Public code; automated checks on every pull request (tests, lint, CodeQL, Scorecard, Dependabot) |
| Separate approver for releases | Releases built and published by CI from a tag, never from a laptop |
| Independent oversight | Public AI-CAIQ self-assessment; **Commitment:** independent security review of oak-events by 2027-03-31 |
| Ethics committee | Ethics and privacy check (section 9) recorded in the pull request for changes to collected data, event fields, MCP tools or the reference model |
| Deputy for incidents | Coordinated disclosure timelines in the incident response plan; reporters may disclose if CloudLab Works misses them |

Nothing is published, released or submitted under the CloudLab Works name without Alex's approval.

## 3. Policy set and AICM domain map

| Document | Covers (AICM domains) |
| --- | --- |
| This policy | GRC, A&A, LOG (policy), HRS (AI use), legal and regulatory tracking |
| Security baseline | IAM, CEK, DCS (assets), UEM, AIS and CCC (change and release), TVM (scanning), STA (suppliers) |
| Incident response plan | SEF, TVM (vulnerability handling) |
| oak-events `SECURITY.md` | TVM (intake) |
| oak-events `docs/THREAT_MODEL.md` | TVM, MDS (adversarial threats), AIS |
| oak-events `docs/MODEL_CARD.md` | MDS (model documentation, open-model risk, failure modes) |
| oak-events `docs/DATA_FLOW.md` | DSP, IPY, LOG (redaction) |
| oak-events `docs/SBOM.md` | STA (bill of materials) |
| Not written yet | BCR (continuity): **Commitment** by 2027-03-31. DCS physical and I&S infrastructure: not applicable, no data centre or hosted service |

## 4. Review

Each document is reviewed at least once a year and after a significant change: a new product or hosted
service, the first employee or contractor, a new kind of data, a change of reference model, an incident, or
a change in a law listed in section 7. The review is a dated commit to the document. Alex approves it.

## 5. Exceptions

1. Any deviation from a policy or the baseline needs a written exception: what, why, risk, compensating
   control, expiry (no more than 90 days), and Alex's approval. It is recorded as a private note in the
   affected repository's issue tracker or security advisory, or in the policy file's history.
2. Emergency changes (for example a fix for an actively exploited flaw) may skip normal steps such as waiting
   for scheduled checks. They are recorded within 2 business days and reviewed as in step 1.
3. Expired exceptions are closed or renewed at the quarterly access review.

## 6. Assurance and internal assessment

1. Once a year, CloudLab Works repeats the AI-CAIQ self-assessment against the current documents and code.
2. OpenSSF Scorecard runs weekly; Alex reviews its findings and the code scanning and Dependabot alerts at
   least monthly.
3. Cycle: plan (yearly, in the quarter before the AI-CAIQ refresh), collect evidence (public links to files,
   commits and settings), record findings, set remediation dates, report in the published AI-CAIQ.
4. Logging and monitoring: CloudLab Works relies on the GitHub account security log, GitHub Actions run logs,
   code scanning, secret scanning and Dependabot alerts, and private vulnerability reports. Alerts are read
   when notified and in the monthly review. oak-events sends nothing to CloudLab Works; its event files stay
   on the customer's host.

## 7. Legal and regulatory tracking

Initial self-assessment, 2026-11-02. This is not legal advice; it is checked once a year and when a listed
law changes.

| Requirement | Applies to CloudLab Works? | Note |
| --- | --- | --- |
| Washington data breach notification (RCW 19.255) | Only for personal information CloudLab Works holds (for example a reporter's email) | Notice duties in the incident response plan |
| Washington biometric identifiers law (RCW 19.375) | No, as shipped | oak-events produces no biometric identifiers; adding identification is a prohibited use (section 8) |
| Washington privacy act on recording (RCW 9.73) | No, as shipped | oak-events uses no microphone and records no communications |
| GDPR, UK GDPR, US state privacy laws | Not as controller or processor | Deployers who run oak-events are controllers of their event data |
| EU AI Act | Initial view: free and open source release; no placing on the EU market as a product | Deployers assess their own use; person detection is not biometric identification |
| Open source licences | Yes | oak-events is MIT; dependency licences are listed in the SBOM; the reference model YOLOv6 is GPL-3.0 and is fetched by the deployer, not redistributed by oak-events |
| Platform terms (GitHub, PyPI, CSA STAR) | Yes | Followed as a user of each platform |
| Washington LLC annual report | Yes | Corporate filing |

## 8. Acceptable use of oak-events (people-sensing camera)

oak-events detects people and depth changes. Even without pictures, its events show when people are present,
for how long and how far from the camera.

**Intended use:** presence and change events for your own home, lab or workspace, with the knowledge of the
people who use that space.

**Not allowed:**

1. covert monitoring of people who have not been told a camera is in use,
2. identifying individuals, for example by adding face recognition or re-identification models, or by
   joining events with other data to single out a person,
3. monitoring of employees, tenants or the public where local law does not allow it,
4. cameras in places where people expect privacy, such as bathrooms or other people's bedrooms,
5. decisions with legal or similarly significant effect on a person made only from events,
6. use as a safety or security system that people rely on to protect life or property,
7. modifying oak-events to send pictures to the host and distributing that as oak-events.

**Deployer duties:** tell the people in view (notice or signage), have a lawful reason, set a retention
period, keep event files private, and keep a human in control of what agents do with events.

**Human oversight.** oak-events gives agents read-only tools; an agent cannot move, reconfigure or read the
camera through it. If you let an agent act on events (send messages, unlock doors, call services), put a
human approval step before any action that affects a person.

**Enforcement.** CloudLab Works enforces this in what it ships: no image path to the host, tests that fail on
image files, the `CONTRIBUTING.md` rule, and refusal of contributions that add identification or image
output. The MIT licence does not let CloudLab Works bind deployers; outside the shipped code, this section
states expectations and the terms of any support CloudLab Works gives.

## 9. Ethics and privacy check

Before merging a change to collected data, event fields, MCP tools, host output queues or the reference
model, Alex answers in the pull request: Does it reveal more about people? Could it identify someone? Does it
let an agent act? Does it change who can read the data? If the answer is yes, the change needs a threat model
and data flow update, or it is declined.

## 10. Internal use of AI

1. CloudLab Works uses AI assistants to draft code, tests and documents.
2. Alex reviews and approves AI-drafted work before it is merged, released or published.
3. Secrets are not placed in prompts to hosted models. Tasks that need a secret use a tool path that keeps the
   secret out of the model context.
4. Other people's personal data is shared with an AI assistant only when a task needs it.
5. AI assistants get credentials scoped as in the security baseline.

## 11. Contacts with security and AI communities

OpenSSF (Best Practices project 16204, Scorecard), the Cloud Security Alliance (STAR Platform account),
the GitHub Advisory Database (through Dependabot alerts), and Luxonis upstream issue trackers for DepthAI
and model issues. Reviewed with this policy.
