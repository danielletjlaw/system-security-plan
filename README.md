# System Security Plan — ShopCore E-Commerce Application

**Author:** Danielle Law  
**Framework:** NIST SP 800-18 Rev. 2  
**System:** ShopCore (fictional e-commerce web application)  
**Status:** Complete

---

## About This Project

A System Security Plan (SSP) is the document that formally describes a system's security posture — its boundaries, the data it handles, who is responsible for it, and which controls protect it. It is the foundational document for any formal authorization process (ATO), FedRAMP package, or compliance audit.

This SSP covers ShopCore, the fictional e-commerce application used throughout this portfolio. It is structured per NIST SP 800-18 Rev. 2 and references controls from NIST SP 800-53 Rev. 5.

---

## Files in This Repo

| File | Description |
|---|---|
| `ssp-shopcore.md` | The full System Security Plan |
| `controls-summary.md` | Control implementation summary — one row per control family |
| `system-diagram.md` | System boundary and data flow description (text-based) |

---

## How This Connects to the Rest of the Portfolio

The SSP is the document that ties everything else together:

| Portfolio Project | Role in the SSP |
|---|---|
| [Risk Register](https://github.com/danielletjlaw/risk-register-lab) | Informs the risk assessment section (Section 7) |
| [Security Policies](https://github.com/danielletjlaw/security-policies-lab) | Referenced as implemented policies in each control family |
| [IR Runbooks](https://github.com/danielletjlaw/incident-response-runbook) | Referenced under the IR control family (IR-8) |

---

## Why the SSP Matters

Every organization running a formal IT system needs some version of this document. In federal and FedRAMP contexts it is called the SSP directly. In SOC 2 contexts it appears as the System Description. In healthcare it underlies HIPAA security rule documentation. The structure differs but the concept is identical: formally state what the system does, what data it holds, and what controls protect it.

---

*This is a fictional scenario created for portfolio purposes. No real customer data or systems are involved.*
