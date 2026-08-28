# Cloudora Account Takeover Investigation

**SOC Investigation | KQL / Azure Data Explorer | Incident Response**
*Fictional client scenario*

---

## Summary

A password-spray campaign originating from Lagos, Nigeria targeted Cloudora's Microsoft 365 tenant over three days, ultimately compromising the CEO's account and a second executive account. I led the investigation end-to-end using KQL against Entra ID sign-in and audit logs — detecting the spray, confirming account compromise, establishing behavioral baselines, uncovering attacker persistence, scoping the full blast radius, clearing a false positive, and verifying containment.

**Severity:** P1 - CEO account compromised, rogue MFA device registered, inbox rule staged for BEC
**Outcome:** Contained. Sessions revoked, credentials reset, persistence removed, attacker IPs blocked, containment verified via follow-up query.

## What this demonstrates

- Writing and adapting KQL queries against custom log tables (`CloudoraSignIn_CL`, `CloudoraAudit_CL`)
- Distinguishing malicious activity from normal behavior using sign-in baselines
- Identifying attacker persistence mechanisms (MFA device registration, mailbox rules)
- Scoping an incident beyond the initially reported account
- Investigating and clearing a false positive (geo-anomaly) without over-reacting
- Mapping findings to MITRE ATT&CK
- Verifying containment with evidence, not assumption
- Writing incident documentation for both technical (SOC) and client-facing audiences

## Repo contents

| File | Description |
|---|---|
| [`Cloudora_Incident_Report.pdf`](./Cloudora_Incident_Report.pdf) | Full formal incident report - executive summary, timeline, findings, IOCs, MITRE mapping, remediation, lessons learned |
| [`Account_takeover_evidence_log.md`](./Account_takeover_evidence_log.md) | Step-by-step investigation log with KQL queries and query-result screenshots for each stage |
| [`images/`](./images) | Screenshots of KQL queries and results referenced in the evidence log |

## Investigation at a glance

| # | Step | Key finding |
|---|---|---|
| 1 | Password-spray detection | 100+ failed logins (`ResultType 50126`) across 3 Lagos IPs, 20+ accounts targeted |
| 2 | CEO account compromise | Failed logins -> successful auth at 03:12:05 UTC from 102.89.44.17, followed by OWA + Azure Portal access |
| 3 | CEO baseline comparison | Confirmed Lagos activity had zero overlap with 2+ weeks of London-only sign-ins |
| 4 | Persistence | Rogue Pixel 6 MFA device registered; "RSS Subscriptions" inbox rule created to hide finance/invoice emails |
| 5 | Scope of compromise | Second account (Priya Nair) compromised from same infrastructure; used to access SharePoint |
| 6 | False positive cleared | Dubai sign-in for a third user verified as legitimate travel, not a related compromise |
| 7 | Containment verification | Follow-up query confirmed zero successful logins from attacker infrastructure post-remediation |

## Tools & environment

- **KQL** (Kusto Query Language) for detection, baselining, and scoping queries
- **Azure Data Explorer** (custom tables simulating Entra ID sign-in and audit logs)
- Concepts: Entra ID sign-in logs, Exchange Online audit logs, Conditional Access, MFA methods, mailbox rules, MITRE ATT&CK

## Scenario disclaimer

Cloudora is a fictional company created for a training exercise (MyFirstHack). All data, accounts, IPs, and identities in this project are simulated and do not represent a real organization or real individuals.
