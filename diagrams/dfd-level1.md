# Level 1 Data Flow Diagram

```mermaid
flowchart TB
    subgraph External
        Patient[Patient]
        Doctor[Doctor]
    end

    subgraph "1.0 Authentication"
        Login[1.1 Login]
        MFA[1.2 MFA Verify]
        Session[1.3 Session Mgmt]
    end

    subgraph "2.0 Patient Records"
        View[2.1 View Records]
        Update[2.2 Update Records]
        Audit[2.3 Audit Log]
    end

    subgraph "3.0 Prescriptions"
        Create[3.1 Create Rx]
        Validate[3.2 Validate]
        Send[3.3 Send to Pharmacy]
    end

    subgraph Data
        UserDB[(User Store)]
        PatientDB[(Patient Records - PHI)]
        AuditDB[(Audit Logs - Internal)]
    end

    Patient -->|"PII - Credentials via TLS"| Login
    Login -->|"PII - Verify Identity"| UserDB
    Login -->|"PII - Token"| MFA
    MFA -->|"Sensitive - JWT Session"| Session

    Doctor -->|"PHI - View Request"| View
    View -->|"PHI - Query"| PatientDB
    View -->|"PHI - Access Log"| Audit
    Audit -->|"Internal - Store"| AuditDB

    Doctor -->|"PHI - New Rx"| Create
    Create -->|"PHI - Check"| Validate
    Validate -->|"PHI - Approved Rx via mTLS"| Send
```

> ⚠️ Any flow containing PHI must be encrypted and logged!

## Data Classification

| Data Type | Classification | Encryption Required | Logging Required |
|---|---|---|---|
| Patient Records | PHI - Critical | Yes (AES-256) | Yes - Full |
| Login Credentials | PII - High | Yes (bcrypt) | Yes - Access |
| Session Tokens | Sensitive | Yes (JWT signed) | Yes - Access |
| Audit Logs | Internal | Yes (at rest) | N/A |
| Prescription Data | PHI - Critical | Yes (AES-256) | Yes - Full |

