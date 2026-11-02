# CloudLab Works information security and AI program

| | |
| --- | --- |
| Owner and approver | Alex Alvord, founder |
| Applies to | Cloud Lab Works LLC (CloudLab Works) and its open source products (first in scope: oak-events) |
| Status | Approved by Alex Alvord and published 2026-11-02. |
| Review | At least once a year with the AI-CAIQ refresh (next by 2027-11-01), and after any significant change listed in the governance policy (section 4) |
| Related | [Security and AI governance policy](security-and-ai-governance-policy.md), [Security baseline](security-baseline.md), [Incident response plan](incident-response-plan.md), [Supply chain policy](supply-chain-policy.md) |

This file is the index of the CloudLab Works information security and AI program. Section 1 maps every
domain of the CSA AI Controls Matrix (AICM v1.1.1) to the document and section that covers it, or says why
the domain does not apply. Section 2 lists the laws, standards and contractual terms CloudLab Works tracks
and how compliance with each was checked. Items marked **Commitment** are not done yet; each has a date.

## 1. AICM domains

Status: **In place**: the documents exist and are published. **Partial**: in place with a dated gap.
**Not in place**: no program yet, with a dated commitment. **Not applicable**: with the reason. The
question counts and owners per domain are in oak-events
[`docs/SHARED_RESPONSIBILITY.md`](https://github.com/cloudlabworks/oak-events/blob/main/docs/SHARED_RESPONSIBILITY.md).

| Domain | Covered by | Status |
| --- | --- | --- |
| A&A Audit and Assurance | Governance policy section 6 (assurance and internal assessment); section 2 of this file (requirements and verification); the published AI-CAIQ | Partial: no independent assessment yet. **Commitment:** independent security review of oak-events by 2027-03-31 |
| AIS Application and Interface Security | Security baseline sections 6 and 7; oak-events `THREAT_MODEL.md`; oak-events `HARDENING.md` | Partial: the PyPI release workflow has never succeeded. **Commitment:** a working PyPI release by 2026-12-15 (baseline section 7) |
| BCR Business Continuity and Operational Resilience | Nothing yet | Not in place. **Commitment:** continuity plan by 2027-03-31 (governance policy section 3) |
| CCC Change Control and Configuration Management | Security baseline section 7 (change and release, supported configuration baseline) | Partial. **Commitment:** branch protection on `main` by 2026-12-15 (baseline section 1) |
| CEK Cryptography, Encryption and Key Management | Security baseline section 3 | In place for what applies; oak-events performs no cryptography, so most CEK questions are not applicable (reason per question in the AI-CAIQ) |
| DCS Datacenter Security | Security baseline section 4 (assets, classification) | Partial: the asset and classification parts are in place. Data centre and physical controls are not applicable: CloudLab Works runs no data centre or hosted service; customers own their premises |
| DSP Data Security and Privacy Lifecycle | oak-events `DATA_FLOW.md`; governance policy sections 8 and 9 | In place. Training-data questions are owned by the model provider (`MODEL_CARD.md`) |
| GRC Governance, Risk and Compliance | Governance policy; this file; oak-events `RISK_ASSESSMENT.md` | In place, with the substitute for an ethics committee in governance policy section 2 |
| HRS Human Resources | Governance policy section 10 (internal use of AI) | Not applicable except AI use: CloudLab Works has no employees or contractors. Reviewed when the first person joins (governance policy section 4) |
| IAM Identity and Access Management | Security baseline sections 1 to 3 | Partial. **Commitment:** first recorded access review by 2027-02-01 (baseline section 1) |
| IPY Interoperability and Portability | oak-events `DATA_FLOW.md` section 6; README event schema | In place |
| I&S Infrastructure and Virtualization Security | oak-events `HARDENING.md` (guidance for customers) | Not applicable to CloudLab Works: it runs no infrastructure for customers. Customers own their hosts and networks |
| LOG Logging and Monitoring | Governance policy section 6 item 4; oak-events event files, `privacy` event and MCP access log (from the release after 0.1.1); `examples/webhook.md` | Partial until the release after 0.1.1 |
| MDS Model Security | oak-events `MODEL_CARD.md`; `THREAT_MODEL.md` sections 4, 6 and 7 | Partial: no model signature upstream; no CloudLab Works fairness evaluation yet. **Commitment:** spot-check by 2027-03-31 |
| SEF Security Incident Management | Incident response plan; oak-events `SECURITY.md` | In place |
| STA Supply Chain Management | Supply chain policy; oak-events `SHARED_RESPONSIBILITY.md` and `SBOM.md` | In place. **Commitment:** SBOM attached to each release by 2027-02-28 |
| TVM Threat and Vulnerability Management | Security baseline section 6; incident response plan; oak-events `SECURITY.md` and `THREAT_MODEL.md` section 7 (guardrail review) | Partial: no third-party penetration test. **Commitment:** independent review by 2027-03-31 |
| UEM Universal Endpoint Management | Security baseline section 5 | Partial. **Commitment:** per-device check with dated evidence by 2027-02-15 |

## 2. Requirements tracked and how compliance is checked

**This is not legal advice.** It is CloudLab Works' own reading, checked once a year and when a listed
law or standard changes (governance policy section 7). Scope: oak-events as released, and the
CloudLab Works accounts that build and publish it. First check: 2026-11-02, against oak-events commit
c3075be.

### 2.1 Laws and regulations

| Requirement | Applies? | How compliance was checked | Result (2026-11-02) |
| --- | --- | --- | --- |
| Washington data breach notification, RCW 19.255 | Only for personal information CloudLab Works holds (for example a reporter's email) | Incident response plan section 5 item 2 read: notice to residents and to the Attorney General within 30 days, as the statute sets | Procedure in place |
| Washington biometric identifiers, RCW 19.375 | No, as shipped | Code check: event fields (README schema) contain no face or body features, embeddings or identities; `src/` has no recognition code; `tests/test_no_images.py` passes | Not applicable as shipped. Adding identification is a prohibited use (governance policy section 8) |
| Washington privacy act, RCW 9.73 (recording communications) | No, as shipped | Code check: the pipeline creates only colour, stereo, depth and detection nodes (`camera.py`); no audio input | Not applicable as shipped |
| GDPR, UK GDPR, US state privacy laws | Not as controller or processor of event data | Code check: `src/` imports no network client library; oak-events sends nothing to CloudLab Works (`DATA_FLOW.md` section 1) | Deployers are controllers; CloudLab Works holds only reporter contact data, covered by RCW 19.255 above |
| EU AI Act, Regulation (EU) 2024/1689 | Initial view: no | Article 2(12) excludes AI systems released under free and open-source licences unless placed on the market as high-risk or under Article 5 or 50. oak-events is MIT licensed, not sold, and performs none of the Article 5 practices as shipped (no biometric identification or categorisation, no emotion recognition) | Not applicable to CloudLab Works on this reading. Deployers assess their own use |

### 2.2 Licences and platform terms

| Requirement | How compliance was checked | Result (2026-11-02) |
| --- | --- | --- |
| oak-events MIT licence | `LICENSE` present; `pyproject.toml` declares `license = "MIT"` | Compliant |
| Dependency licences | SBOM licence summary (`docs/sbom-licenses-0.1.1-00d9053.md`): all permissive or public-domain style | Compliant; re-checked at each SBOM regeneration |
| Reference model licence (GPL-3.0) | `git ls-files` shows no model archive or blob in the repository; README tells deployers to fetch the model themselves | Compliant: oak-events does not redistribute the model |
| GitHub, PyPI, Cloudflare, Zoho, Bitwarden, Luxonis terms | Supply chain policy section 4 | Read 2026-11-02; Luxonis covered by exception E1 |
| CSA STAR Platform terms | Supply chain policy section 4 | **Commitment:** read by 2026-12-15 |
| Customer contracts | Alex Alvord confirmed on 2026-11-02 that CloudLab Works has no customer contracts | None to comply with; re-checked at each yearly review and before signing any contract |

### 2.3 Standards and frameworks

| Standard | Use | How it is checked | Status (2026-11-02) |
| --- | --- | --- | --- |
| CSA AI Controls Matrix v1.1.1 and AI-CAIQ v1.1.0 | Self-assessment, published | Yearly (governance policy section 6) | First self-assessment drafted |
| OpenSSF Scorecard | Repository security checks | Weekly workflow; target 7.0 or higher (baseline section 6.1) | 7.2 for oak-events |
| OpenSSF Best Practices badge | Project practices | Reviewed yearly | Passing (project 16204) |
| CycloneDX 1.6 | SBOM format | At each release | Current SBOM is CycloneDX 1.6 |
| MITRE ATLAS, OWASP Top 10 for LLM Applications (2025), STRIDE | Threat model method | Yearly and on new versions (`THREAT_MODEL.md` section 7) | Used in the current threat model |

### 2.4 Out of scope for this check

The Washington LLC annual report and other corporate filings are tracked by Alex privately; they are not
part of the oak-events audit scope.

## 3. Review

Alex reviews this file with the AI-CAIQ refresh each year. A domain moves from Partial or Not in place to
In place only when its documents are published and its commitments are met. Each review is a dated commit.
