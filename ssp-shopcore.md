# System Security Plan — ShopCore E-Commerce Application

**Document Control**

| Field | Value |
|---|---|
| System Name | ShopCore |
| System Owner | ShopCore Operations (fictional) |
| Authorizing Official | Chief Information Security Officer |
| SSP Version | 1.0 |
| Date | 2026-11-12 |
| Classification | Internal |
| Framework | NIST SP 800-18 Rev. 2 / NIST SP 800-53 Rev. 5 |
| Prepared By | Danielle Law |

---

## Table of Contents

1. System Identification
2. System Categorization
3. System Description and Architecture
4. System Environment
5. System Interconnections
6. Responsible Parties
7. Risk Assessment Summary
8. Rules of Behavior
9. Control Implementation — Management Controls
10. Control Implementation — Operational Controls
11. Control Implementation — Technical Controls
12. Plan of Action & Milestones (POA&M)

---

## Section 1 — System Identification

| Field | Value |
|---|---|
| System Name | ShopCore |
| System Abbreviation | SHC |
| System Type | Major Application |
| Operational Status | Operational |
| System Function | E-commerce platform enabling customers to browse products, create accounts, place orders, and process payments |
| Primary Data Processed | Customer PII, order history, product catalog |
| Payment Data | Payment card data tokenized via third-party processor; raw card data not stored in ShopCore systems |

---

## Section 2 — System Categorization

Security categorization is performed per NIST FIPS 199 — Standards for Security Categorization of Federal Information and Information Systems.

### 2.1 Information Types

| Information Type | Confidentiality | Integrity | Availability |
|---|---|---|---|
| Customer PII (names, addresses, email) | High | Moderate | Moderate |
| Order and transaction records | Moderate | High | Moderate |
| Payment card data (tokenized) | High | High | High |
| Product catalog and pricing | Low | Moderate | High |
| System and audit logs | Moderate | High | Moderate |

### 2.2 System Security Categorization

The overall system categorization is determined by the **high-water mark** across all information types:

| Security Objective | Impact Level |
|---|---|
| Confidentiality | **High** (driven by customer PII and payment data) |
| Integrity | **High** (driven by transaction record accuracy and payment processing) |
| Availability | **High** (driven by e-commerce revenue dependency) |

**Overall System Categorization: HIGH**

> A HIGH categorization means the loss of confidentiality, integrity, or availability could have a **severe or catastrophic** adverse effect on operations, assets, or individuals. This drives the selection of the full NIST 800-53 Rev. 5 HIGH baseline.

---

## Section 3 — System Description and Architecture

### 3.1 System Purpose

ShopCore is a cloud-hosted e-commerce web application that allows customers to:
- Browse and search a product catalog
- Create and manage customer accounts
- Place and track orders
- Process payments via a third-party payment processor

Internal users (employees) use an admin portal to manage inventory, customer accounts, and order fulfillment.

### 3.2 System Boundaries

The ShopCore authorization boundary is defined in `system-diagram.md`. In summary:

**In-scope (ShopCore responsibility):**
- Web application frontend (CloudFront/S3)
- Application server tier (EC2)
- Database tier (RDS PostgreSQL)
- Admin portal
- Authentication and session management
- AWS infrastructure configuration (VPC, security groups, IAM, CloudTrail)
- CI/CD pipeline components running in AWS

**Out-of-scope (AWS or vendor responsibility):**
- AWS physical data center security (inherited)
- AWS hypervisor and hardware (inherited)
- Third-party payment processing infrastructure (Stripe — vendor assessment)
- GitHub source code repository (third-party SaaS)

### 3.3 Users

| User Type | Count (approx.) | Access Method | Authentication |
|---|---|---|---|
| Customers (public) | Thousands | Public internet → HTTPS | Username + password; MFA optional |
| Support agents | ~10 | Internal → Admin portal via VPN | Username + password + MFA (required) |
| Developers | ~5 | CI/CD pipeline only; no direct prod access | GitHub SSO + AWS IAM role |
| Database administrators | ~2 | VPN → DB admin tool | Username + password + MFA (required) |
| System administrators | ~2 | VPN → AWS console | AWS SSO + MFA (required) |
| Security team | ~2 | Read-only log/audit access | AWS SSO + MFA (required) |

---

## Section 4 — System Environment

| Attribute | Value |
|---|---|
| Hosting | Amazon Web Services (AWS) |
| Primary Region | us-east-2 (Ohio) |
| Deployment Model | Cloud (IaaS/PaaS) |
| Availability Zone Coverage | Multi-AZ (RDS, ALB) |
| Operating System | Amazon Linux 2023 (EC2 instances) |
| Primary Language / Runtime | Node.js 20 LTS |
| Database | PostgreSQL 15 on AWS RDS |
| Container / Orchestration | None (EC2-based deployment) |

### 4.1 Hardware

ShopCore has no physical hardware. All compute, storage, and networking is provided by AWS and managed through infrastructure-as-code (Terraform). Physical and environmental controls are inherited from AWS.

### 4.2 Software

| Component | Version | Purpose |
|---|---|---|
| Node.js | 20 LTS | Application runtime |
| PostgreSQL | 15 | Primary database |
| Nginx | 1.24 | Reverse proxy on EC2 |
| AWS CloudFront | N/A (managed) | CDN and TLS termination |
| AWS ALB | N/A (managed) | Load balancing |
| Dependabot | N/A (GitHub) | Dependency vulnerability scanning |

---

## Section 5 — System Interconnections

| Connected System | Owner | Data Shared | Direction | Security Controls |
|---|---|---|---|---|
| Stripe Payment API | Stripe (fictional) | Payment tokenization requests; transaction results | Outbound (ShopCore → Stripe) | TLS 1.2+; API key authentication; PCI-DSS certified vendor |
| AWS SES | Amazon Web Services | Customer email addresses (order confirmations) | Outbound (ShopCore → AWS SES) | TLS; AWS IAM role with least-privilege |
| GitHub | GitHub, Inc. | Source code (no customer data) | Bidirectional | GitHub SSO; branch protection rules; no production data in repo |
| Corporate VPN | ShopCore IT | Employee authentication (no customer data) | Inbound | Certificate-based VPN; MFA |

**Interconnection Agreements:**
- Stripe: governed by Stripe's Master Services Agreement and Data Processing Agreement
- AWS SES: governed by AWS Customer Agreement
- GitHub: governed by GitHub Enterprise agreement; no PII or Restricted data stored

---

## Section 6 — Responsible Parties

### 6.1 Key Roles

| Role | Name / Team | Responsibility |
|---|---|---|
| System Owner | ShopCore Engineering (fictional) | Overall accountability for the system |
| Authorizing Official | CISO (fictional) | Authorizes the system to operate; accepts residual risk |
| Information System Security Officer (ISSO) | Information Security Team | Day-to-day security oversight; maintains this SSP |
| System Administrator | IT Operations Team | Infrastructure management; patching; monitoring |
| Database Administrator | DBA Team | Database configuration; backup; access control |
| Privacy Officer | Legal / Compliance Team | PII handling; breach notification; privacy policy |

### 6.2 External Service Providers

| Provider | Service | POC |
|---|---|---|
| AWS | Cloud infrastructure | AWS Enterprise Support |
| Stripe (fictional) | Payment processing | Vendor account manager |

---

## Section 7 — Risk Assessment Summary

A formal risk assessment was conducted using NIST SP 800-30 Rev. 1. The full risk register is maintained separately at [risk-register-lab](https://github.com/danielletjlaw/risk-register-lab).

### 7.1 Summary of Findings

| Risk Level | Count | Examples |
|---|---|---|
| Critical | 1 | SQL injection against payment database (R-001, score: 20) |
| High | 4 | Credential stuffing (R-002), third-party processor breach (R-003), ransomware (R-004), XSS (R-005) |
| Medium | 9 | Misconfigured S3, DDoS, insider threat, cloud outage, vulnerable dependencies, others |
| Low | 1 | CDN cache poisoning (R-015) — Accepted |

### 7.2 Risk Acceptance Statement

The Authorizing Official has reviewed the risk register and accepts the residual risk associated with operating ShopCore at its current control implementation level, subject to the remediation commitments documented in Section 12 (POA&M). Authorization is conditional on completion of High-priority POA&M items by the dates specified.

---

## Section 8 — Rules of Behavior

All users with access to ShopCore systems must acknowledge and comply with the following rules. Violations may result in access revocation, disciplinary action, or legal consequences.

**All users shall:**
- Access only the data and systems they are explicitly authorized to access
- Use strong, unique passwords and enroll in MFA where required
- Report suspected security incidents or policy violations immediately
- Not share credentials with any other person under any circumstances
- Not use ShopCore systems for personal or unauthorized business purposes

**Employees additionally shall:**
- Not install unauthorized software on ShopCore systems
- Not connect unauthorized devices to ShopCore's network or cloud environment
- Not copy Restricted or Confidential data to personal devices or unapproved cloud storage
- Complete required security awareness training annually

**Developers additionally shall:**
- Not hardcode credentials, API keys, or secrets in source code
- Not deploy directly to production outside the approved CI/CD pipeline
- Not use real customer data in development or test environments

---

## Section 9 — Control Implementation: Management Controls

### CA — Assessment, Authorization & Monitoring

**CA-2 — Control Assessments**
*Implementation:* No formal independent control assessment has been conducted. The Information Security Team conducts informal internal reviews annually. A formal assessment by an independent assessor is planned for Q2 2027.
*Status:* Partially Implemented

**CA-7 — Continuous Monitoring**
*Implementation:* AWS CloudWatch monitors system health, performance metrics, and log anomalies. CloudTrail records all API activity. Alerts are configured for key security events (failed logins, IAM policy changes, security group modifications). The Information Security Team reviews alerts daily.
*Status:* Implemented

**CA-9 — Internal System Connections**
*Implementation:* All internal connections between ShopCore tiers are documented in `system-diagram.md`. Network security groups restrict traffic to approved flows only. No undocumented internal connections are permitted.
*Status:* Implemented

---

### PL — Planning

**PL-2 — System Security Plan**
*Implementation:* This document constitutes the ShopCore System Security Plan. It is maintained by the ISSO and reviewed annually or upon significant system change.
*Status:* Implemented

**PL-4 — Rules of Behavior**
*Implementation:* Rules of Behavior are defined in Section 8 of this SSP. All employees acknowledge the Rules of Behavior upon hire and annually thereafter.
*Status:* Implemented

---

### RA — Risk Assessment

**RA-2 — Security Categorization**
*Implementation:* System categorization is documented in Section 2 of this SSP, using NIST FIPS 199. The system is categorized as HIGH for confidentiality, integrity, and availability.
*Status:* Implemented

**RA-3 — Risk Assessment**
*Implementation:* A full risk assessment was conducted using NIST SP 800-30 Rev. 1 methodology. Results are documented in the ShopCore Risk Register (see Section 7). The risk assessment is reviewed annually and after significant system changes.
*Status:* Implemented

**RA-5 — Vulnerability Monitoring and Scanning**
*Implementation:* Dependabot is enabled in the GitHub repository and scans dependencies for known CVEs. Manual vulnerability assessments are conducted quarterly. Automated infrastructure scanning (AWS Inspector) is not yet configured.
*Status:* Partially Implemented

---

## Section 10 — Control Implementation: Operational Controls

### AT — Awareness & Training

**AT-2 — Literacy Training and Awareness**
*Implementation:* No formal security awareness training program exists. Employees receive a security briefing upon hire and are directed to the security policies documentation. A formal annual training program is planned.
*Status:* Planned

**AT-3 — Role-Based Training**
*Implementation:* Developers receive informal security guidance during code review. No formal role-based training program exists for developers, DBAs, or system administrators.
*Status:* Planned

---

### CP — Contingency Planning

**CP-2 — Contingency Plan**
*Implementation:* No formal contingency plan document exists. Recovery objectives are defined informally: RTO of 4 hours, RPO of 24 hours. A formal contingency plan is planned for Q1 2027.
*Status:* Planned

**CP-4 — Contingency Plan Testing**
*Implementation:* No contingency plan testing has been conducted. Testing is planned following completion of the contingency plan document.
*Status:* Planned

**CP-9 — System Backup**
*Implementation:* AWS RDS automated backups are enabled with a 7-day retention period. Daily snapshots are stored in a separate AWS region. S3 versioning is enabled on all data buckets. Backup integrity is verified monthly by the DBA team.
*Status:* Implemented

**CP-10 — System Recovery and Reconstitution**
*Implementation:* Recovery procedures are documented informally. No tested recovery runbook exists. Recovery from RDS snapshot has been tested once in a non-production environment.
*Status:* Partially Implemented

---

### IR — Incident Response

**IR-1 — Policy and Procedures**
*Implementation:* The ShopCore Incident Response Policy (POL-IR-001) is documented and published. It defines the IR lifecycle, severity levels, response timelines, and notification requirements.
*Status:* Implemented

**IR-4 — Incident Handling**
*Implementation:* Incident response runbooks are documented for three incident types: ransomware, credential stuffing, and confirmed data breach. Runbooks follow the NIST 800-61r3 lifecycle.
*Status:* Implemented

**IR-6 — Incident Reporting**
*Implementation:* All employees are required to report suspected incidents to the Information Security Team immediately. The IR Policy defines escalation paths and notification timelines.
*Status:* Implemented

**IR-8 — Incident Response Plan**
*Implementation:* The IR Policy and runbooks collectively constitute the ShopCore Incident Response Plan. The plan has not yet been tested via tabletop exercise. A tabletop exercise is scheduled for Q1 2027.
*Status:* Partially Implemented

---

### MA — Maintenance

**MA-2 — Controlled Maintenance**
*Implementation:* AWS manages all physical hardware maintenance. ShopCore is responsible for OS and application patching. Critical CVEs are patched within 7 days; high severity within 30 days per the Password & Authentication Policy patching cadence (extended to all system components).
*Status:* Partially Implemented (patch SLA defined; enforcement not yet automated)

---

### PS — Personnel Security

**PS-3 — Personnel Screening**
*Implementation:* Background checks are conducted for all employees with access to production systems prior to access provisioning. Contract staff are screened by their staffing agency.
*Status:* Implemented

**PS-4 — Personnel Termination**
*Implementation:* The Access Control Policy (POL-AC-001) requires all system access to be revoked within 24 hours of employee separation. IT Operations executes access revocations upon notification from HR.
*Status:* Partially Implemented (policy defined; not consistently enforced — gap in HR-to-IT notification process)

---

## Section 11 — Control Implementation: Technical Controls

### AC — Access Control

**AC-2 — Account Management**
*Implementation:* All user accounts are provisioned through a formal request and approval process. Access is reviewed quarterly by the Information Security Team. Admin accounts are separate from standard user accounts. Service accounts are documented and reviewed semi-annually.
*Status:* Partially Implemented (review process defined; quarterly cadence not yet consistently executed)

**AC-3 — Access Enforcement**
*Implementation:* Role-based access control is enforced at the application layer. AWS IAM enforces least-privilege access to cloud resources. Database access is restricted by role (customers cannot access the database directly; support agents have read-only access; DBAs have full access with logging).
*Status:* Implemented

**AC-6 — Least Privilege**
*Implementation:* All roles are defined with minimum necessary access per the Access Control Policy. Production database access is limited to two named DBAs. Developers have no direct production access. IAM roles follow least-privilege principles and are reviewed upon any role change.
*Status:* Implemented

**AC-11 — Device Lock**
*Implementation:* Admin portal sessions expire after 15 minutes of inactivity. Customer sessions expire after 30 minutes. Session tokens are invalidated on logout and cannot be reused.
*Status:* Implemented

**AC-17 — Remote Access**
*Implementation:* Remote access to ShopCore internal systems requires VPN authentication. The admin portal is not accessible from the public internet. VPN access requires certificate-based authentication plus MFA.
*Status:* Implemented

---

### AU — Audit & Accountability

**AU-2 — Event Logging**
*Implementation:* The following events are logged: all authentication attempts (success and failure), all privileged actions, all database queries by admin roles, all IAM policy changes, all security group modifications, and all API calls via AWS CloudTrail.
*Status:* Implemented

**AU-3 — Content of Audit Records**
*Implementation:* All log records include: timestamp (UTC), source IP, user identity, action taken, affected resource, and outcome (success/failure). Logs are structured JSON format stored in CloudWatch Logs.
*Status:* Implemented

**AU-6 — Audit Record Review**
*Implementation:* The Information Security Team reviews security-relevant log alerts daily via CloudWatch dashboards and alert notifications. Full log review is conducted weekly.
*Status:* Implemented

**AU-9 — Protection of Audit Information**
*Implementation:* CloudWatch Logs are configured with write-once retention. Log access is restricted to the Information Security Team and senior IT Operations staff. Logs cannot be modified or deleted by application-layer users.
*Status:* Implemented

---

### IA — Identification & Authentication

**IA-2 — Identification and Authentication (Organizational Users)**
*Implementation:* All employee and admin accounts require MFA. Acceptable MFA methods are defined in the Password & Authentication Policy (POL-IA-001). Single-factor authentication is not permitted for any admin-level access.
*Status:* Implemented

**IA-5 — Authenticator Management**
*Implementation:* Passwords are stored using bcrypt with a minimum cost factor of 12. Passwords are checked against known-breached password lists at creation and reset. Default credentials are prohibited. API keys and service account credentials are stored in AWS Secrets Manager.
*Status:* Implemented

**IA-11 — Re-Authentication**
*Implementation:* Admin sessions require re-authentication after 15 minutes of inactivity. Sensitive operations (password change, payment method update) require re-authentication regardless of session age.
*Status:* Implemented

---

### SC — System and Communications Protection

**SC-5 — Denial of Service Protection**
*Implementation:* AWS Shield Standard is active on all CloudFront distributions and ALBs (included at no additional cost). Rate limiting is configured on the application load balancer and at the application layer for authentication endpoints. AWS Shield Advanced has not been subscribed to; this is a known gap for sustained large-scale DDoS.
*Status:* Partially Implemented

**SC-7 — Boundary Protection**
*Implementation:* The ShopCore network boundary is enforced through AWS VPC with separate public, private, and isolated subnets. Security groups implement default-deny with explicit allow rules. No internet access is permitted from the database subnet. All inbound internet traffic passes through CloudFront before reaching the ALB.
*Status:* Implemented

**SC-8 — Transmission Confidentiality and Integrity**
*Implementation:* All data in transit is encrypted using TLS 1.2 minimum (TLS 1.3 preferred). HTTP is redirected to HTTPS at the CloudFront layer. HSTS headers are set with a 1-year max-age. TLS 1.0 and 1.1 are disabled.
*Status:* Implemented

**SC-28 — Protection of Information at Rest**
*Implementation:* RDS PostgreSQL is encrypted using AWS-managed KMS keys (AES-256). S3 buckets are encrypted using SSE-S3. EC2 EBS volumes are encrypted. AWS Secrets Manager encrypts all stored secrets.
*Status:* Implemented

---

### SI — System and Information Integrity

**SI-2 — Flaw Remediation**
*Implementation:* Dependabot is configured to scan dependencies and open pull requests for known CVEs. The patch SLA is: Critical CVEs within 7 days, High within 30 days. OS patches are applied monthly during a maintenance window. No automated infrastructure scanning is in place.
*Status:* Partially Implemented

**SI-3 — Malware Protection**
*Implementation:* AWS GuardDuty is enabled and monitors for malicious activity including malware indicators, compromised instances, and anomalous API calls. Endpoint protection on developer workstations is managed by IT Operations.
*Status:* Implemented

**SI-10 — Information Input Validation**
*Implementation:* All database queries use parameterized statements (prepared statements). User input is validated at the API layer before processing. Output encoding is applied before rendering user-supplied content to prevent XSS. A Web Application Firewall (WAF) is planned but not yet deployed — this is a known gap.
*Status:* Partially Implemented (WAF not deployed)

---

## Section 12 — Plan of Action & Milestones (POA&M)

The POA&M documents known control gaps and their remediation commitments. The Authorizing Official reviews the POA&M quarterly.

| POA&M ID | Control | Gap Description | Priority | Responsible Party | Target Date |
|---|---|---|---|---|---|
| POA-001 | SI-10, SC-7 | Web Application Firewall not deployed; SQL injection and XSS protections rely on application-layer controls only | High | DevSecOps Team | Q4 2026 |
| POA-002 | AC-2 | Quarterly access review process not consistently executed; last review was 6 months ago | Medium | Identity & Access Team | Q4 2026 |
| POA-003 | AT-2, AT-3 | No formal security awareness training program; employees receive only an onboarding briefing | High | Information Security Team | Q1 2027 |
| POA-004 | CP-2, CP-4 | No formal contingency plan document; recovery runbook not tested | Medium | IT Operations | Q1 2027 |
| POA-005 | IR-8 | IR Plan has not been tested via tabletop exercise | Medium | Information Security Team | Q1 2027 |
| POA-006 | RA-5 | Automated infrastructure vulnerability scanning (AWS Inspector) not configured | Medium | Cloud Operations | Q1 2027 |
| POA-007 | PS-4 | HR-to-IT notification process for employee separations not consistently followed; access revocation sometimes delayed | Medium | HR / IT Operations | Q4 2026 |
| POA-008 | MP-6 | Media sanitization procedures for end-of-life hardware not documented (low priority; no physical media) | Low | IT Operations | Q2 2027 |

---

*End of System Security Plan — ShopCore v1.0*

*This is a fictional scenario created for GRC portfolio purposes. No real customer data, systems, or organizations are represented.*
