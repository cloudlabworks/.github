# CloudLab Works supply chain policy

| | |
| --- | --- |
| Owner and approver | Alex Alvord, founder |
| Applies to | Cloud Lab Works LLC (CloudLab Works): every supplier, platform and upstream component that CloudLab Works or its open source products (first in scope: oak-events) depend on |
| Status | Approved by Alex Alvord and published 2026-11-02. |
| Review | At least once a year (next by 2027-11-01) and on any trigger in section 6 |
| Related | [Security baseline](security-baseline.md) (section 2, account and supplier register), [Security and AI governance policy](security-and-ai-governance-policy.md), oak-events [`docs/SBOM.md`](https://github.com/cloudlabworks/oak-events/blob/main/docs/SBOM.md), [`docs/MODEL_CARD.md`](https://github.com/cloudlabworks/oak-events/blob/main/docs/MODEL_CARD.md), [`docs/SHARED_RESPONSIBILITY.md`](https://github.com/cloudlabworks/oak-events/blob/main/docs/SHARED_RESPONSIBILITY.md) |

CloudLab Works is a one-person company. It buys or uses standard online services on their published
terms; it cannot negotiate contracts with them. This policy therefore works by **selection and review**:
a supplier is used only if it meets the minimum requirements in section 3, or if a written exception
with compensating controls is approved (section 7). Everything below is checked against public
information, and each check records its source URL and the date it was read.

## 1. Suppliers in scope

The supplier list is the account and supplier register in the security baseline (section 2). For this
policy they fall into four kinds:

| Kind | Suppliers | Agreement |
| --- | --- | --- |
| Platform services with a CloudLab Works account | GitHub, PyPI, Cloudflare, the domain registrar, Zoho Mail, Bitwarden, the CSA STAR Platform | The supplier's published terms of service |
| Hardware and SDK vendor | Luxonis (DepthAI SDK, device firmware, model zoo) | Luxonis terms of service; DepthAI is open source |
| Model provider | The reference model's developer (Meituan, YOLOv6) and distributor (Luxonis model zoo) | The model licence (GPL-3.0) |
| Upstream open source packages | The Python packages oak-events installs, listed in the SBOM | Each package's licence; no service relationship |

## 2. Risk tiers

Each supplier gets a tier from what a compromise or failure of that supplier could do.

| Tier | Meaning | Suppliers |
| --- | --- | --- |
| High | Could change the code or model customers run, or take over CloudLab Works accounts | GitHub, PyPI, Luxonis (DepthAI SDK and firmware run on the customer's host and camera), the model provider, Bitwarden, Zoho Mail (security contact and account recovery), the domain registrar and Cloudflare (DNS for `security.txt` and the site) |
| Medium | Could break the build or a release, or expose CloudLab Works' business information | Upstream packages in the runtime path (`numpy`, `mcp` and their dependencies), build and CI tools in `requirements/` |
| Low | No access to code, releases or credentials | CSA STAR Platform |

## 3. Minimum requirements

| Requirement | Applies to | How it is checked |
| --- | --- | --- |
| Multi-factor authentication is offered, and CloudLab Works has it turned on | Every supplier with an account | Supplier documentation; CloudLab Works' own status is tracked privately (security baseline section 2) |
| A published security attestation (SOC 2, ISO/IEC 27001 or similar) **or** a published security policy with a vulnerability disclosure channel | High tier services | Supplier's public trust or security page |
| Signed artifacts or provenance are used where the supplier offers them | Code and package platforms, upstream packages, model files | PyPI attestations (PEP 740), GitHub artifact attestations, model archive hash in `MODEL_CARD.md` |
| Published terms cover confidentiality of private data, account security and acceptable use | Platform services | Terms of service, read in full at first use and at each review |
| Licence compatible with oak-events (MIT) distribution | Upstream packages, model | SBOM licence summary; `MODEL_CARD.md` |
| Maintained (a release or commit in the last 12 months) and reachable for security reports | Upstream packages in the runtime path | PyPI and repository pages at each SBOM regeneration |

Service levels, personnel screening and audit rights cannot be required from suppliers that offer only
standard terms. CloudLab Works accepts that, because no customer depends on a CloudLab Works hosted
service (customers run oak-events themselves). The risk is recorded in oak-events `RISK_ASSESSMENT.md`
(risk R11).

A High tier supplier that does not meet a requirement is used only under an exception (section 7).

## 4. Supplier review record

First review: 2026-11-02, by Waku (AI assistant) for Alex Alvord; approved by Alex on publication. All
pages were read on 2026-11-02.

| Supplier | Tier | Terms read | What the terms say (summary) | Security attestation or policy | MFA offered | Signed artifacts | Result |
| --- | --- | --- | --- | --- | --- | --- | --- |
| GitHub | High | [GitHub Terms of Service](https://docs.github.com/en/site-policy/github-terms/github-terms-of-service) | Service provided "as is" (section O); keeping the account secure is the user's responsibility (B.4); private repository content treated as confidential (E) | GitHub lists SOC 1 Type 2, SOC 2 Type 2, ISO/IEC 27001:2022, CSA STAR Level 2 and a CSA CAIQ among its compliance reports ([list](https://docs.github.com/en/enterprise-cloud@latest/admin/overview/accessing-compliance-reports-for-your-enterprise)). The reports are downloadable only from an Enterprise account, which CloudLab Works does not have, so CloudLab Works relies on the published list, not the report contents | Yes; GitHub requires 2FA for code contributors ([source](https://docs.github.com/en/authentication/securing-your-account-with-two-factor-authentication-2fa/about-mandatory-two-factor-authentication)) | Artifact attestations offered ([docs](https://docs.github.com/en/actions/how-tos/secure-your-work/use-artifact-attestations/use-artifact-attestations)); not used by oak-events yet | Meets minimums. Action: use attestations when the release workflow is fixed |
| PyPI | High | [PyPI Terms of Service](https://policies.python.org/pypi.org/Terms-of-Service/) (effective 2025-02-25) | Service provided "as is"; account security is the user's responsibility | No SOC 2 or ISO attestation found in the pages read; security policy and reporting channel published ([pypi.org/security](https://pypi.org/security/)) | Yes; required for all users since 2024-01-01 ([PyPI blog](https://blog.pypi.org/posts/2024-01-01-2fa-enforced/)) | Trusted publishing ([docs](https://docs.pypi.org/trusted-publishers/)) and attestations ([docs](https://docs.pypi.org/attestations/)) offered; oak-events' `release.yml` uses trusted publishing; no release has been published yet | Meets minimums (security policy route) |
| Luxonis | High | [Luxonis Terms of Service](https://www.luxonis.com/terms-of-service) (last updated 2024-12-06) | Software and content provided "AS IS" without warranty | No attestation found; [depthai-core security policy page](https://github.com/luxonis/depthai-core/security/policy) says no security policy is set up | Not applicable: no account is used | DepthAI wheels on PyPI have no attestations (section 4.1); model zoo archives are not signed (`MODEL_CARD.md` section 5) | **Does not meet** the attestation-or-policy minimum. Exception E1 (section 7) |
| Model provider (Meituan YOLOv6, via Luxonis model zoo) | High | Licence: GPL-3.0 ([meituan/YOLOv6](https://github.com/meituan/YOLOv6)) | Model provided under GPL-3.0; oak-events does not redistribute it | Not applicable to a model; provenance and evaluation in `MODEL_CARD.md` | Not applicable | No signature offered; CloudLab Works publishes the tested archive's SHA-256 | Covered by exception E1 (no signature) |
| Cloudflare | High | [Cloudflare Terms](https://www.cloudflare.com/terms/) | Services provided "as is" and "as available" (section 11) | ISO 27001, ISO 27701, PCI DSS and SOC 2 Type II listed on the [Trust Hub](https://www.cloudflare.com/trust-hub/) | Yes ([docs](https://developers.cloudflare.com/fundamentals/user-profiles/2fa/)) | Not applicable (hosts the website only) | Meets minimums |
| Zoho Mail | High | [Zoho Terms of Service](https://www.zoho.com/terms.html) (last updated 2022-03-02) | Content from other users provided "AS IS" | SOC 2 Type 2 and ISO/IEC 27001 (certificate valid to 2028-08-21) listed on [Zoho compliance](https://www.zoho.com/compliance.html) | Yes | Not applicable | Meets minimums |
| Bitwarden | High | [Bitwarden Terms of Service](https://bitwarden.com/terms/) (effective 2017-06-01) | Standard service terms | ISO 27001, SOC 2 and SOC 3 listed on [Bitwarden compliance](https://bitwarden.com/compliance/) | Yes | Not applicable | Meets minimums |
| Domain registrar | High | Not named in public documents | UNKNOWN | UNKNOWN | UNKNOWN | Not applicable | **Commitment:** Alex reviews it against section 3 and records the result privately by 2026-12-15 |
| CSA STAR Platform | Low | CSA platform terms | Not read yet | Not required for Low tier | Not checked | Not applicable | **Commitment:** read terms by 2026-12-15 |
| Upstream Python packages | Medium | Licences in the SBOM licence summary | All permissive or public-domain style (SBOM.md) | Not applicable | Not applicable | 22 of 31 components have PyPI attestations (section 4.1) | Meets minimums; attestation gaps noted |

### 4.1 Provenance of upstream packages

Checked 2026-11-02 against the components of `sbom-oak-events-0.1.1-00d9053.cdx.json`, using the PyPI
integrity API (`https://pypi.org/integrity/<project>/<version>/<file>/provenance`,
[docs](https://docs.pypi.org/api/integrity/)) for every file of the resolved version.

- **22 of 31 components** publish attestations for every file, each from the project's own GitHub
  repository through trusted publishing: anyio, attrs, click, cryptography, idna, jsonschema,
  jsonschema-specifications, mcp, mcp-types, numpy, pip, pydantic, PyJWT, python-multipart, referencing,
  rpds-py, sse-starlette, starlette, truststore, typing-inspection, typing_extensions, uvicorn.
- **9 components** publish none: annotated-types, cffi, depthai, h11, httpcore2, httpx2,
  opentelemetry-api, pycparser, pydantic_core.

CI installs its tools from hash-pinned lists (`requirements/*.txt`, `pip install --require-hashes`), so a
changed file for a pinned version fails the build. Customer installs use the version ranges in
`pyproject.toml` and are not hash-pinned.

## 5. Annual review

Once a year, with the AI-CAIQ refresh, Alex (or an AI assistant for Alex) repeats section 4 for every
supplier:

1. Re-read each supplier's terms; note the "last updated" date and any change to confidentiality,
   security, data use or acceptable use.
2. Re-check each security attestation or policy page; record what is listed and the date.
3. Confirm MFA is still on for each CloudLab Works account (private record).
4. Re-run the provenance check in section 4.1 against the newest SBOM.
5. Re-assess tiers and minimums (section 6 steps 2 to 5), renew or close exceptions.
6. Commit the updated section 4 as a dated change to this file.

## 6. Risk-based assessment process

Run for a new supplier before first use, at each annual review, and on these triggers: a supplier
security incident or breach notice, a material change to terms, an attestation that lapses, a new
DepthAI major version, a change of reference model, or a new runtime dependency.

1. **Identify** what CloudLab Works gives the supplier or takes from it: code, packages, models,
   credentials, email, DNS. Note any personal data (CloudLab Works holds little: reporters' email
   addresses).
2. **Tier** it (section 2) by what a compromise could do to customers or to CloudLab Works accounts.
3. **Check** the minimum requirements (section 3) and record sources and dates.
4. **Look for incidents:** the supplier's status or security pages, the GitHub Advisory Database, and
   news of breaches in the last 12 months.
5. **Decide:** use, use with compensating controls (exception, section 7), or do not use. Record the
   decision in section 4, and a new or changed risk in oak-events `RISK_ASSESSMENT.md`.

## 7. Exceptions

Exceptions follow the governance policy (section 5), except that supplier exceptions are reviewed at
each annual review rather than expiring after 90 days, because there is often no alternative supplier.

| ID | Supplier | Gap | Compensating controls | Owner | Review |
| --- | --- | --- | --- | --- | --- |
| E1 | Luxonis (SDK, firmware, model zoo) and the model provider | No published security policy or attestation; DepthAI wheels and model archives are not signed | oak-events forces DepthAI telemetry, crash dumps and auto-calibration off; never downloads a model at runtime; publishes the tested model archive's SHA-256; DepthAI version and model changes trigger a threat model review; the deployer pins and checks the model hash. **Commitment:** ask Luxonis whether it publishes a vulnerability disclosure process, by 2027-02-28 | Alex Alvord | Next by 2027-11-01 |

There is no alternative supplier for E1: oak-events exists to use Luxonis OAK cameras.

## 8. Bill of materials per release

1. Every oak-events release regenerates the CycloneDX SBOM and licence summary (`docs/SBOM.md`,
   security baseline section 7 step 5) and re-runs the provenance check in section 4.1.
2. The model is not in the SBOM because oak-events does not ship one; its bill-of-materials fields
   (name, version, developer, distributor, licence, training data, format, hash) are in
   `MODEL_CARD.md` section 2.
3. **Commitment** (from `SBOM.md`): generate the SBOM in `release.yml` and attach it to each GitHub
   release by 2027-02-28.

## 9. Review of this policy

Alex reviews this policy at least once a year and after any trigger in section 6. Each review is a commit
to this file.
