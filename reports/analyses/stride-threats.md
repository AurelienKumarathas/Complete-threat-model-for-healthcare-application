# STRIDE Threat Register — Solaris Care Connect 360

## Risk Scoring Key
- 🔴 **Critical** = Immediate patient harm or full system compromise possible
- 🟠 **High** = Significant data breach or regulatory violation likely
- 🟡 **Medium** = Limited impact, exploitable under specific conditions
- 🟢 **Low** = Minimal impact, difficult to exploit

---

## S — Spoofing Threats
> Attacker pretends to be a legitimate user or system. In healthcare this can lead to wrong treatments and patient harm.

| ID | Threat | Component | Description | Severity | Mitigation | MITRE ATT&CK |
|----|--------|-----------|-------------|----------|------------|--------------|
| S1 | Credential Theft | Auth Service | Attacker steals patient credentials via phishing | 🟠 High | MFA enforcement, phishing-resistant login, user awareness training | T1566 - Phishing |
| S2 | Fake Doctor Accounts | Admin Portal | Attacker creates unauthorised doctor accounts to access PHI | 🔴 Critical | Admin approval workflow, identity verification, RBAC enforcement | T1136 - Create Account |
| S3 | Token Forgery | API Gateway | API requests made with stolen or forged JWT tokens | 🟠 High | Short JWT expiry, token rotation, signature validation | T1539 - Steal Web Session Cookie |
| S4 | MITM on Insurance API | External API | Man-in-the-middle attack on insurance provider communication | 🟡 Medium | Mutual TLS (mTLS), certificate pinning, API key rotation | T1557 - Adversary-in-the-Middle |
| S5 | Lab Result Spoofing | Lab Integration | Fake lab results injected via compromised lab feed | 🔴 Critical | mTLS with lab systems, result signing, anomaly detection | T1195 - Supply Chain Compromise |

---

## T — Tampering Threats
> Malicious modification of data. Tampered medical data can be life-threatening.

| ID | Threat | Component | Description | Severity | Mitigation | MITRE ATT&CK |
|----|--------|-----------|-------------|----------|------------|--------------|
| T1 | Record Modification | Patient Records | Unauthorised changes to patient medical records | 🔴 Critical | Field-level integrity hashing, write audit logs, RBAC write controls | T1565 - Data Manipulation |
| T2 | Prescription Alteration | Rx Service | Drug dosages or medications changed maliciously | 🔴 Critical | Digital signatures on Rx, pharmacist confirmation step, immutable Rx log | T1565.001 - Stored Data Manipulation |
| T3 | Audit Log Tampering | Audit Service | Deleting or modifying audit trails to hide breaches | 🟠 High | Write-once audit store, SIEM alerting on log gaps, off-system backup | T1070 - Indicator Removal |
| T4 | Claims Manipulation | Insurance API | Modifying insurance claim amounts in transit or at rest | 🟠 High | Payload signing, encrypted DB fields, reconciliation checks | T1565 - Data Manipulation |
| T5 | Vitals Data Tampering | Monitoring Service | Altering real-time vital signs to mask patient deterioration | 🔴 Critical | Sensor data signing, threshold anomaly alerts, redundant monitoring | T1565.002 - Transmitted Data Manipulation |

---

## R — Repudiation Threats
> User denies performing an action. Without proper logging, there is no proof.

| ID | Threat | Component | Description | Severity | Mitigation | MITRE ATT&CK |
|----|--------|-----------|-------------|----------|------------|--------------|
| R1 | Prescription Denial | Rx Service | Doctor denies approving a prescription | 🟠 High | Cryptographically signed Rx approvals, timestamped audit log | T1070 - Indicator Removal |
| R2 | Access Denial | Patient Records | Patient denies accessing their own records | 🟡 Medium | Immutable access logs with user ID, IP, and timestamp | T1070.001 - Clear Windows Event Logs |
| R3 | Permission Change Denial | Admin Portal | Admin denies changing user permissions | 🟠 High | Admin action logging, dual-approval for privilege changes | T1078 - Valid Accounts |
| R4 | Claim Receipt Denial | Insurance API | Insurance provider denies receiving a submitted claim | 🟡 Medium | Signed receipts, message delivery acknowledgements, audit trail | T1557 - Adversary-in-the-Middle |
| R5 | Export Audit Gap | Data Export | No audit trail for bulk data exports | 🟠 High | Mandatory logging on all export operations, DLP controls | T1530 - Data from Cloud Storage |

---

## I — Information Disclosure Threats
> Sensitive data is exposed to unauthorised parties. PHI exposure triggers HIPAA breach notifications.

| ID | Threat | Component | Description | Severity | Mitigation | MITRE ATT&CK |
|----|--------|-----------|-------------|----------|------------|--------------|
| I1 | SQL Injection | Patient Records | Patient records exposed via SQLi attack | 🔴 Critical | Parameterised queries, ORM, input validation, WAF rules | T1190 - Exploit Public-Facing Application |
| I2 | Excessive Data Return | API Gateway | API returns more data fields than the request requires | 🟠 High | Response filtering, API field allowlists, least privilege data access | T1213 - Data from Information Repositories |
| I3 | Verbose Error Messages | All Services | Error messages reveal stack traces or system internals | 🟡 Medium | Generic error responses in production, detailed logs server-side only | T1082 - System Information Discovery |
| I4 | Unencrypted Data in Transit | Network | PHI transmitted without TLS encryption | 🔴 Critical | TLS 1.2+ enforced on all connections, HSTS headers | T1040 - Network Sniffing |
| I5 | Exposed Backup Files | Storage | Backup files accessible without authentication | 🔴 Critical | Encrypted backups, access-controlled storage, no public S3 buckets | T1530 - Data from Cloud Storage |
| I6 | PHI in Logs | All Services | PHI accidentally written to application logs | 🟠 High | Log scrubbing rules, PII-aware logging libraries, log access controls | T1552 - Unsecured Credentials |

---

## D — Denial of Service Threats
> Systems made unavailable. In healthcare, downtime can directly impact patient care.

| ID | Threat | Component | Description | Severity | Mitigation | MITRE ATT&CK |
|----|--------|-----------|-------------|----------|------------|--------------|
| D1 | DDoS Attack | API Gateway | Distributed denial of service floods the gateway | 🟠 High | WAF, rate limiting, CDN-based DDoS mitigation (e.g. CloudFront) | T1498 - Network Denial of Service |
| D2 | Database Exhaustion | Database | Expensive queries exhaust DB connection pool | 🟠 High | Query timeouts, connection pooling limits, read replicas | T1499 - Endpoint Denial of Service |
| D3 | Storage Exhaustion | File Storage | Unlimited uploads fill available storage | 🟡 Medium | File size limits, upload quotas per user, storage monitoring alerts | T1499.001 - OS Exhaustion Flood |
| D4 | Mass Account Lockout | Auth Service | Attacker triggers lockouts across many accounts | 🟡 Medium | Progressive lockout delays, CAPTCHA, lockout alerting to SOC | T1110 - Brute Force |
| D5 | Rx Service Overload | Rx Service | Processing queue overloaded with malformed requests | 🟠 High | Input validation, request rate limiting, queue depth monitoring | T1499 - Endpoint Denial of Service |

---

## E — Elevation of Privilege Threats
> Attacker gains more access than they are authorised for. The most dangerous category in a healthcare system.

| ID | Threat | Component | Description | Severity | Mitigation | MITRE ATT&CK |
|----|--------|-----------|-------------|----------|------------|--------------|
| E1 | Patient to Doctor Privilege | Auth Service | Patient account gains doctor-level access to PHI | 🔴 Critical | Strict RBAC, role validation on every API request, privilege audits | T1078 - Valid Accounts |
| E2 | Doctor to Admin Privilege | Auth Service | Doctor account gains admin access to system config | 🔴 Critical | Separate admin accounts, MFA for admin actions, least privilege | T1078.003 - Cloud Accounts |
| E3 | SQLi to DBA Access | Database | SQL injection escalates to database admin privileges | 🔴 Critical | Parameterised queries, DB user least privilege, no app-level DBA access | T1190 - Exploit Public-Facing Application |
| E4 | Container Escape | Infrastructure | Attacker escapes container to access host system | 🔴 Critical | Read-only containers, no privileged mode, runtime security (Falco) | T1611 - Escape to Host |
| E5 | IDOR | Patient Records | Attacker accesses another patient's records via ID manipulation | 🟠 High | Server-side authorisation checks, never trust client-supplied IDs | T1548 - Abuse Elevation Control Mechanism |

---

## Overall Risk Summary

| Severity | Count | Threat IDs |
|----------|-------|------------|
| 🔴 Critical | 12 | S2, S5, T1, T2, T5, I1, I4, I5, E1, E2, E3, E4 |
| 🟠 High | 13 | S1, S3, T3, T4, R1, R3, R5, I2, I6, D1, D2, D5, E5 |
| 🟡 Medium | 6 | S4, R2, R4, I3, D3, D4 |

> ⚠️ All Critical threats must be mitigated before production deployment. High threats must have a remediation plan within 30 days.
