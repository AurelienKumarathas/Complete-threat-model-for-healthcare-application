# Solaris Care Connect 360 Architecture

## Overview
This document describes the architecture of the Solaris Care Connect 360 healthcare platform.

## Components

### Frontend Applications
- **Patient Mobile App**: iOS/Android app for patients
- **Doctor Portal**: Web application for healthcare providers
- **Admin Dashboard**: Internal management interface

### Backend Services
- **API Gateway**: Central entry point, rate limiting, routing
- **Auth Service**: OAuth 2.0, MFA, session management
- **Patient Records**: CRUD for patient data, audit logging
- **Prescription Service**: Rx management, pharmacy integration
- **Messaging Service**: Doctor-patient secure messaging
- **Vitals Monitoring**: Real-time health data processing

### Data Stores
- **PostgreSQL**: Primary database (encrypted at rest)
- **S3**: Document storage (medical images, PDFs)
- **Redis**: Session cache, real-time data

### External Integrations
- Insurance provider APIs (read/write)
- Pharmacy network (prescriptions)
- Lab results feed (inbound)

## Trust Boundaries
1. Internet → API Gateway (TLS termination)
2. API Gateway → Backend Services (internal network)
3. Backend Services → Database (encrypted connection)
4. Backend Services → External APIs (mutual TLS)



```mermaid
flowchart LR
    subgraph INTERNET["Internet Zone"]
        Patient["📱 Patient Mobile App\niOS / Android"]
        Doctor["💻 Doctor Web Portal\nReact SPA"]
        Admin["🔧 Admin Dashboard\nInternal UI"]
    end

    subgraph DMZ["DMZ - Trust Boundary 1 - TLS"]
        WAF["🔥 WAF / DDoS Protection"]
        GW["⚡ API Gateway\nRate Limiting - Routing - Logging"]
    end

    subgraph AUTH["Auth Zone - Trust Boundary 2"]
        AuthSvc["🔑 Auth Service\nOAuth 2.0 - MFA - JWT"]
        RBAC["👤 RBAC Engine\nRole-Based Access Control"]
    end

    subgraph SERVICES["Backend Services - Internal Network"]
        PR["🏥 Patient Records\nCRUD - Audit Logging"]
        RX["💊 Prescription Service\nRx Management"]
        MSG["💬 Messaging Service\nEnd-to-End Encrypted"]
        VITAL["❤️ Vitals Monitoring\nReal-Time Processing"]
    end

    subgraph DATA["Data Layer - Trust Boundary 3"]
        DB[("🐘 PostgreSQL\nEncrypted at Rest")]
        S3[("📁 S3 Storage\nMedical Images - PDFs")]
        REDIS[("⚡ Redis Cache\nSessions - Real-Time")]
        AUDIT[("📋 Audit Log\nImmutable Trail")]
    end

    subgraph EXTERNAL["External Systems - Trust Boundary 4 - mTLS"]
        INS["🏦 Insurance APIs\nRead / Write"]
        PHARM["💉 Pharmacy Network\nPrescriptions"]
        LAB["🔬 Lab Results\nInbound Feed"]
    end

    Patient -->|HTTPS| WAF
    Doctor -->|HTTPS| WAF
    Admin -->|HTTPS| WAF
    WAF --> GW
    GW -->|Verify Token| AuthSvc
    AuthSvc --> RBAC
    GW -->|Authorised| PR
    GW -->|Authorised| RX
    GW -->|Authorised| MSG
    GW -->|Authorised| VITAL
    PR --> DB
    PR --> S3
    PR --> AUDIT
    RX --> DB
    MSG --> REDIS
    VITAL --> DB
    VITAL --> REDIS
    RX -->|mTLS| PHARM
    PR -->|mTLS| INS
    VITAL -->|mTLS| LAB
```
