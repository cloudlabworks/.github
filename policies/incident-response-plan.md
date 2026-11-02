# CloudLab Works incident response plan

| | |
| --- | --- |
| Owner and incident lead | Alex Alvord, founder |
| Applies to | Security vulnerabilities and incidents in CloudLab Works open source projects (first in scope: oak-events), CloudLab Works accounts and cloudlabworks.dev |
| Status | Approved by Alex Alvord and published 2026-11-02. |
| Review | At least once a year (next by 2027-11-01), after every incident and after every exercise |

CloudLab Works runs no hosted service and holds no customer data, so a flaw in its software cannot leak
customer data from CloudLab Works. The incidents that matter are: a flaw that breaks a product's privacy
model on a customer's host, a malicious or broken release, and takeover of a CloudLab Works account.

## 1. Intake

| Channel | Use |
| --- | --- |
| GitHub private vulnerability reporting ("Report a vulnerability" on the repository's Security tab) | Preferred for any project flaw. Enabled on oak-events (checked 2026-11-02). |
| Email in `https://cloudlabworks.dev/.well-known/security.txt` | Anything else, including the website and accounts |
| Automated alerts (Dependabot, CodeQL, secret scanning, Scorecard) | Treated as reports |

Public issues are not the place for security problems; if one is opened, Alex hides or closes it and moves
the discussion to a private advisory.

## 2. Severity

Severity uses the CVSS v4.0 base score, adjusted for the project's privacy model:

| Severity | Examples | Fix target from triage |
| --- | --- | --- |
| Critical | Any path that sends pictures or depth maps off the camera or writes them to disk; a malicious release; takeover of a CloudLab Works account | 7 days |
| High | MCP server reads files outside the events directory; telemetry or crash-dump upload turned on without opt-in; CVSS 7.0 or above | 30 days |
| Medium | Event files created with broader permissions; denial of service of the event pipeline; CVSS 4.0 to 6.9 | 90 days |
| Low | Hardening gaps with no practical attack; CVSS below 4.0 | Next release |

## 3. Timelines

1. Acknowledge the reporter within 7 days (as `SECURITY.md` states).
2. Triage within 14 days: reproduce, set severity, decide whether a dependency or upstream (for example
   DepthAI or the model provider) owns the fix, and tell the reporter.
3. Fix within the target above. If the fix depends on an upstream party, report to them the same day and
   tell the reporter.
4. Coordinated disclosure when the fix is released, or 90 days after the report, whichever comes first,
   unless the reporter agrees to a different date.

## 4. Triage and response steps

1. **Record.** Open (or keep) a draft GitHub security advisory on the affected repository. It is the incident
   record: dates, severity, affected versions, decisions, fix, root cause.
2. **Contain.** For account compromise: revoke sessions, tokens and keys, rotate passwords, check the account
   security log, remove unknown collaborators, deploy keys and webhooks. For a malicious or broken release:
   yank it on PyPI, delete the tag, update the MCP registry entry, and post a notice in the README.
3. **Fix.** Develop the fix in the advisory's temporary private fork; add a test that fails before the fix.
4. **Release.** Patch release through the normal release process; emergency exceptions are allowed as in the
   governance policy.
5. **Disclose.** Publish the advisory and request a CVE through GitHub; credit the reporter if they wish; add
   a `Security` entry to `CHANGELOG.md`; for Critical issues also post on cloudlabworks.dev.
6. **Learn.** Within 14 days of closing, add the root cause and any process change to the record, and update
   the threat model or baseline if needed.

**Evidence.** Before closing an account or supply chain incident, export the relevant GitHub security log
entries and download Actions logs (GitHub keeps them for a limited time), and record SHA-256 hashes of
affected release files. Keep them with the incident record.

## 5. Notification

1. **Users of CloudLab Works software:** the published advisory, the changelog, and for Critical issues the
   README and website. GitHub notifies dependents through Dependabot alerts.
2. **Personal information held by CloudLab Works** (for example a reporter's email): if breached, follow
   Washington's breach law (RCW 19.255): notify affected residents without unreasonable delay and within 30
   days of discovery, and notify the Washington Attorney General, also within 30 days, if more than 500
   Washington residents are affected. Apply other states' laws where their residents are affected.
3. **Suppliers:** report compromise involving GitHub, PyPI or Luxonis to that supplier's security team.

## 6. Contacts

| Who | When |
| --- | --- |
| Washington State Office of the Attorney General, data breach notice (atg.wa.gov) | Breach affecting more than 500 Washington residents |
| FBI Internet Crime Complaint Center (ic3.gov) | Criminal activity, extortion, account theft |
| CISA incident reporting (cisa.gov/report) | Supply chain compromise affecting others |
| GitHub Security and PyPI security (security@pypi.org) | Compromise of an account or package on their platform |
| Luxonis (DepthAI GitHub repositories, security contact) | Flaws in DepthAI, device firmware or Luxonis models |

Contacts are checked once a year with this plan.

## 7. Records and metrics

Incident records are the GitHub security advisories (draft and published) on each repository; only the
repository owner and invited reporters can see drafts. Records are kept for at least 3 years.

Metrics, reported once a year in the AI-CAIQ refresh: reports received by severity, time to acknowledge,
time to fix compared with the target, and advisories published. As of 2026-11-02 no security advisory has
been published for oak-events. Records are reviewed once a year for patterns and root causes.

## 8. Exercise

Once a year Alex runs a tabletop exercise of this plan (for example: "a reporter shows a code path that
writes a colour frame to disk" or "a release you did not make appears on PyPI") and records what worked and
what changed. **Commitment:** first exercise by 2027-03-31.
