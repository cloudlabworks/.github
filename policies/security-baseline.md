# CloudLab Works security baseline

| | |
| --- | --- |
| Owner | Alex Alvord, founder |
| Applies to | Cloud Lab Works LLC (CloudLab Works): its accounts, devices and open source projects (first in scope: oak-events) |
| Status | Approved by Alex Alvord and published 2026-11-02. |
| Review | At least once a year (next by 2027-11-01) and after any significant change (see section 9) |
| Related | [Security and AI governance policy](security-and-ai-governance-policy.md), [Incident response plan](incident-response-plan.md) |

CloudLab Works is a one-person Washington LLC. It has no employees, runs no hosted service, holds no
customer data and trains no models. Its products are open source tools that customers run on their own
hardware. This baseline states the minimum security settings for the accounts, devices and code that
CloudLab Works controls. Items marked **Commitment** are not done yet; each has a target date.

## 1. Identities and access

**Who has access.** Alex Alvord is the only person with access to CloudLab Works accounts. AI assistants
operated by Alex act through credentials that Alex issues; they are tools, not separate staff, and Alex is
accountable for everything they do.

**Rules**

1. One named owner per account. Shared logins are not used, except that AI assistants run under credentials
   Alex issues to them.
2. Least privilege: a credential gets only the scope its task needs. Fine-grained or deploy-scoped tokens are
   preferred to account-wide tokens.
3. Granting access: Alex decides, records the change in the register below (account, scope, reason, date) and,
   for repository collaborators, in the repository settings.
4. Removing access: on the day the need ends, and at once on suspected compromise (revoke, rotate, then
   review the account security log).
5. Review: Alex reviews every account, collaborator, token and SSH or deploy key each quarter.
   **Commitment:** first recorded review by 2027-02-01.
6. Need to know: CloudLab Works holds no customer data. Its private material (credentials, recovery codes,
   unpublished vulnerability reports, business records) is available only to Alex, and to an AI assistant
   only for the specific task that needs it.

**Separation of duties.** One person cannot separate duties. Substitute controls: all code is public; changes
go through pull requests with automated checks (section 4); releases are built by CI, not on a laptop; every
change is recorded in git history and `CHANGELOG.md`; an independent review is planned (see the governance
policy). **Commitment:** branch protection or a ruleset on `main` requiring the CI checks to pass by 2026-12-15.

## 2. Account and supplier register

Multi-factor authentication is required on every account that supports it. Status per account is tracked privately and reviewed quarterly.

| Account or supplier | Purpose |
| --- | --- |
| GitHub account `cloudlabworks` (a personal-type account, not a GitHub organization) | Hosts the public repositories, CI, security alerts, private vulnerability reports |
| GitHub account `issaquahd` (Alex's personal account) | Authored most oak-events commits |
| PyPI | Package distribution for oak-events (no project published yet); publishing uses trusted publishing (section 3) |
| Cloudflare | DNS, CDN and hosting for cloudlabworks.dev |
| Domain registrar for cloudlabworks.dev | Domain |
| Zoho Mail | Email, including the security contact in `security.txt` |
| Bitwarden | Password manager for all CloudLab Works credentials |
| Cloud Security Alliance STAR Platform | STAR for AI self-assessment |
| Luxonis (DepthAI SDK, device firmware, model zoo) | Upstream for oak-events; no account needed |
| Open source Python packages | Dependencies of oak-events; listed in the SBOM |

## 3. Credentials and keys

1. Every password is unique, generated and stored in the password manager. Recovery codes are stored there too.
2. MFA is required on every account that supports it; hardware security keys or passkeys are preferred.
3. Secrets are never committed to a repository. GitHub secret scanning is enabled on the CloudLab Works account.
4. Releases need no long-lived upload token: `release.yml` publishes to PyPI through trusted publishing (a
   short-lived OIDC token issued to the GitHub Actions run, scoped to the `pypi` environment).
5. Workflow tokens are read-only by default (`permissions: contents: read` or `read-all`); a job gets write
   scopes only where it needs them.
6. Keys and tokens are rotated on suspected compromise, when their purpose ends, and reviewed once a year.
7. Encryption in transit for the website: TLS 1.2 or 1.3 only, HSTS with preload.
8. oak-events itself holds no secrets and performs no cryptography. It writes event files with mode 0600.
9. Git commits and release tags are not signed today. **Commitment:** signed tags for releases by 2027-01-31.

Alex Alvord owns credential and key management.

## 4. Asset register and classification

| Asset | Classification | Where it is tracked |
| --- | --- | --- |
| Public source code, docs, website content | Public | GitHub, git history |
| Package names (PyPI `oak-events`, MCP registry `io.github.cloudlabworks/oak-events`) | Public | This register |
| Domain cloudlabworks.dev and its DNS | Public, high business impact | Registrar and Cloudflare accounts |
| Account credentials, recovery codes, tokens, keys | Confidential | Password manager only |
| Unpublished vulnerability reports and incident records | Confidential | GitHub private security advisories |
| Business records of the LLC | Confidential | Not stored in any repository |
| Maintainer endpoints (computers used for CloudLab Works) | Internal | Inventory kept by Alex; see section 5 |
| Test hardware (one OAK-D PoE camera) | Internal | This register |

Classification follows business risk: Confidential items would allow account takeover, a malicious release
or disclosure of a reporter's unfixed finding. This register is reviewed with the baseline.

## 5. Endpoints

Every computer or phone used for CloudLab Works work must have:

1. full-disk encryption turned on,
2. an automatic screen lock after no more than 10 minutes idle, with a password or biometric unlock,
3. automatic operating system security updates turned on,
4. the operating system's built-in firewall and built-in anti-malware protection turned on,
5. the vendor's find-my-device service turned on, so a lost device can be locked or erased remotely.

Approved software sources: the operating system vendor's app store and updates, Homebrew, PyPI, npm, and
release pages of the projects themselves. Other sources need a recorded exception.

**Commitment:** a per-device check of items 1 to 5 with dated evidence by 2027-02-15. Until then this
section is a requirement, not a verified state.

## 6. Dependency and code scanning (oak-events)

| Control | Where | Cadence |
| --- | --- | --- |
| Dependabot version updates for pip and GitHub Actions | `.github/dependabot.yml` | Weekly |
| CodeQL static analysis (Python) | `.github/workflows/codeql.yml` | Every push and pull request to `main`, and weekly |
| GitHub secret scanning | Repository settings | Continuous |
| OpenSSF Scorecard (7.2 of 10 on 2026-11-02) | `.github/workflows/scorecard.yml` | Every push to `main` and weekly |
| Lint: ruff, any finding fails the build | `.github/workflows/ci.yml` | Every push and pull request |
| Tests on Ubuntu and macOS, Python 3.11 and 3.13, without `depthai` or a camera | `.github/workflows/ci.yml` | Every push and pull request |
| Third-party Actions pinned to a full commit SHA | All workflows | On every change |
| Private vulnerability reporting | Repository settings, `SECURITY.md` | Continuous |
| OpenSSF Best Practices badge: passing (project 16204) | bestpractices.dev | Reviewed yearly |

### 6.1 Security targets

These targets turn the scans above into objectives. They apply to every public CloudLab Works repository.

| Metric | Target | Status 2026-11-02 |
| --- | --- | --- |
| OpenSSF Scorecard score | 7.0 or higher on each public repository by 2026-12-15 | Met 2026-11-02: oak-events 7.2, mcp-failure-lab 7.5, agent-trace-clinic 7.3 (fuzzing added; branch protection and code review remain the gaps) |
| Open high or critical Dependabot or CodeQL alerts | None older than 14 days | oak-events: 0 Dependabot, 0 CodeQL. Scorecard process findings stay open until the Scorecard target is met |
| CI on `main` | Green; a red `main` is fixed or reverted within 2 days | Green |

Progress is reviewed at each quarterly review (section 9). A missed target is recorded as an exception (section 8) with a new date.

## 7. Change and release process

1. Every change lands through a pull request. CI (tests, lint) and CodeQL must pass before merge. This is a
   working rule today and becomes enforced with the branch protection commitment in section 1.
2. Each change adds a line under "Unreleased" in `CHANGELOG.md`.
3. Changes must keep the privacy rule in `CONTRIBUTING.md` (no picture leaves the camera).
4. A change to the MCP tool surface, the event schema, the host output queues or the reference model gets a
   security and privacy review recorded in the pull request, and updates `docs/THREAT_MODEL.md`,
   `docs/DATA_FLOW.md` or `docs/MODEL_CARD.md` as needed.
5. Release: bump the version in `pyproject.toml` and `server.json`, move "Unreleased" to the new version,
   regenerate the SBOM (`docs/SBOM.md`), then push a `vX.Y.Z` tag. `release.yml` builds the sdist and wheel
   and publishes to PyPI.
6. Rollback: revert the change and release a patch version; a broken or malicious PyPI release is yanked.
7. Known gap: both release runs so far (v0.1.0 and v0.1.1, 2026-10-12) failed, so oak-events is not on PyPI.
   **Commitment:** a working PyPI release by 2026-12-15.

**Supported configuration baseline (oak-events 0.1.x):** Python 3.10 or newer (CI tests 3.11 and 3.13);
DepthAI 3.9 or newer (live-tested with 3.11); RVC2 PoE bootloader 0.0.29 or newer; privacy defaults on
(`DEPTHAI_TELEMETRY=0`, `DEPTHAI_DISABLE_CRASHDUMP_COLLECTION=1`, `DEPTHAI_AUTOCALIBRATION=OFF`); no model
download at runtime. The baseline is checked at each release and once a year.

## 8. Exceptions

Exceptions to this baseline follow the exception process in the governance policy.

## 9. Review

Alex reviews this baseline at least once a year and after a significant change: a new person with access, a
new product or hosted service, a new supplier, or a security incident. Each review is a commit to this file.
