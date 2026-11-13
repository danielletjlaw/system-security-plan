# Control Implementation Summary

**System:** ShopCore (fictional e-commerce web application)  
**Framework:** NIST SP 800-53 Rev. 5  
**Last Updated:** 2026-11-12

This document summarizes control implementation status by family. Full implementation details are in `ssp-shopcore.md` Sections 9–10.

---

## Implementation Status Key

| Status | Meaning |
|---|---|
| **Implemented** | Control is fully in place |
| **Partially Implemented** | Control is in place but gaps exist |
| **Planned** | Control is not yet in place; remediation planned |
| **Not Applicable** | Control does not apply to this system |
| **Inherited** | AWS is responsible for this control (shared responsibility) |

---

## Control Family Summary

| Family | Key Controls | Status | Notes |
|---|---|---|---|
| **AC — Access Control** | AC-2, AC-3, AC-5, AC-6, AC-11, AC-17 | Partially Implemented | RBAC and least-privilege implemented; quarterly access review not yet formalized |
| **AT — Awareness & Training** | AT-2, AT-3 | Planned | Security awareness training program not yet established |
| **AU — Audit & Accountability** | AU-2, AU-3, AU-6, AU-9, AU-12 | Implemented | CloudTrail and CloudWatch Logs enabled; log integrity protected; retention 90 days |
| **CA — Assessment, Authorization & Monitoring** | CA-2, CA-7, CA-9 | Partially Implemented | No formal assessment conducted; continuous monitoring via CloudWatch in place |
| **CM — Configuration Management** | CM-2, CM-6, CM-7, CM-8 | Partially Implemented | Baseline configurations defined; asset inventory incomplete |
| **CP — Contingency Planning** | CP-2, CP-4, CP-9, CP-10 | Partially Implemented | Automated RDS backups enabled; no tested recovery plan or DR runbook |
| **IA — Identification & Authentication** | IA-2, IA-5, IA-6, IA-11 | Implemented | MFA required for all admin accounts; password policy enforced; bcrypt hashing |
| **IR — Incident Response** | IR-1, IR-4, IR-6, IR-8 | Implemented | IR Policy and runbooks documented; tabletop exercise not yet conducted |
| **MA — Maintenance** | MA-2, MA-4 | Inherited | AWS manages physical maintenance; patching responsibility is ShopCore's |
| **MP — Media Protection** | MP-2, MP-3, MP-6 | Partially Implemented | Data classification policy in place; media sanitization procedures not documented |
| **PE — Physical & Environmental** | PE-1 through PE-20 | Inherited | AWS data center physical controls; ShopCore has no physical infrastructure |
| **PL — Planning** | PL-2, PL-4 | Implemented | This SSP constitutes the system security plan; Rules of Behavior defined |
| **PS — Personnel Security** | PS-3, PS-4, PS-7 | Partially Implemented | Background checks conducted; offboarding procedure defined; not consistently enforced |
| **PT — PII Processing & Transparency** | PT-2, PT-3 | Partially Implemented | PII inventory exists; privacy notice published; formal PII processing records incomplete |
| **RA — Risk Assessment** | RA-2, RA-3, RA-5 | Implemented | Risk register completed using NIST 800-30; vulnerability scanning not yet automated |
| **SA — System & Services Acquisition** | SA-4, SA-9, SA-11 | Partially Implemented | Vendor assessments conducted informally; no formal SDLC security requirements |
| **SC — System & Communications Protection** | SC-5, SC-7, SC-8, SC-28 | Implemented | TLS enforced; network segmentation via VPC/security groups; encryption at rest enabled |
| **SI — System & Information Integrity** | SI-2, SI-3, SI-7, SI-10 | Partially Implemented | Dependabot enabled for dependency scanning; no WAF deployed yet (planned) |

---

## Priority Gaps (Planned Remediation)

| Gap | Control | Priority | Target Date |
|---|---|---|---|
| Security awareness training program not established | AT-2, AT-3 | High | Q1 2027 |
| WAF not yet deployed | SI-10, SC-7 | High | Q4 2026 |
| Quarterly access review not formalized | AC-2 | Medium | Q4 2026 |
| Disaster recovery runbook not tested | CP-4, CP-10 | Medium | Q1 2027 |
| Vulnerability scanning not automated | RA-5 | Medium | Q1 2027 |
| Media sanitization procedures not documented | MP-6 | Low | Q2 2027 |
