# ShopCore — System Boundary and Data Flow Description

**Document Type:** System Boundary Description (supports SSP Section 3)  
**System:** ShopCore (fictional e-commerce web application)  
**Last Updated:** 2026-11-12

---

## System Boundary

The ShopCore authorization boundary includes the following components. Everything inside this boundary is covered by the SSP. Everything outside is a third-party service or external dependency subject to its own authorization or vendor assessment.

### Inside the Boundary

| Component | Type | Description |
|---|---|---|
| Web Application (Frontend) | Software | React-based customer-facing storefront; hosted on AWS CloudFront + S3 |
| Application Server | Software / Compute | Node.js API layer; runs on AWS EC2 (Auto Scaling Group) in us-east-2 |
| Application Database | Data store | PostgreSQL on AWS RDS; holds customer PII, order history, product data |
| Admin Portal | Software | Internal management interface; restricted to corporate VPN access |
| Authentication Service | Software | JWT-based session management; MFA enforcement layer |
| Cloud Infrastructure | Infrastructure | AWS VPC, subnets, security groups, IAM roles, CloudTrail, CloudWatch |
| CI/CD Pipeline | Infrastructure | GitHub Actions + AWS CodeDeploy; used for application deployments |
| Secrets Management | Infrastructure | AWS Secrets Manager; holds database credentials and API keys |

### Outside the Boundary (Third-Party / Inherited)

| Component | Provider | Relationship |
|---|---|---|
| Payment Processing | Stripe (fictional) | Third-party processor; PCI-DSS certified; subject to vendor assessment |
| Email Delivery | AWS SES | AWS-managed service; AWS responsible for infrastructure security |
| DNS | AWS Route 53 | AWS-managed service |
| CDN | AWS CloudFront | AWS-managed service; ShopCore responsible for configuration |
| Source Code Repository | GitHub | Third-party SaaS; not in authorization boundary |

---

## Data Flow Description

### Customer Purchase Flow

```
Customer Browser
    → CloudFront (CDN / TLS termination)
    → Application Load Balancer (AWS, in-boundary)
    → EC2 Application Server (in-boundary)
    → RDS PostgreSQL Database (in-boundary)
    → Stripe Payment API (out-of-boundary, TLS, tokenized card data only)
    → Response back to customer browser
```

**Data in transit:** All traffic encrypted via TLS 1.2 minimum. Card data is tokenized before leaving the application server — raw card numbers never touch the ShopCore database.

**Data at rest:** Customer PII and order data encrypted at rest using AWS RDS encryption (AES-256). S3 buckets encrypted with AWS-managed keys.

### Admin Access Flow

```
Admin Employee Workstation
    → Corporate VPN (required)
    → Admin Portal (restricted subnet, in-boundary)
    → EC2 Application Server (in-boundary)
    → RDS PostgreSQL Database (in-boundary, DBA role only)
```

**Authentication:** Username + password + TOTP MFA required. Sessions expire after 15 minutes of inactivity.

### Deployment Flow

```
Developer Workstation
    → GitHub (out-of-boundary)
    → GitHub Actions (out-of-boundary)
    → AWS CodeDeploy (in-boundary)
    → EC2 Application Server (in-boundary)
```

**Controls:** No developer has direct SSH access to production EC2 instances. All deployments go through the CI/CD pipeline. Deployment approvals required for production.

---

## Data Types and Sensitivity

| Data Type | Classification | Location | Encrypted |
|---|---|---|---|
| Customer PII (name, email, address) | Confidential | RDS PostgreSQL | Yes (at rest + in transit) |
| Payment card data | Restricted | Stripe (tokenized) — NOT in ShopCore DB | N/A — not stored |
| Order history | Confidential | RDS PostgreSQL | Yes |
| Session tokens | Restricted | Application memory / Redis cache | Yes (in transit) |
| Admin credentials | Restricted | AWS Secrets Manager | Yes |
| Application logs | Internal | AWS CloudWatch Logs | Yes |
| Static assets (images, CSS, JS) | Public | S3 / CloudFront | In transit only |

---

## Network Architecture Summary

```
Internet
    ↓
AWS CloudFront (Global Edge)
    ↓
Application Load Balancer (Public Subnet, us-east-2)
    ↓
EC2 Auto Scaling Group (Private Subnet, us-east-2)
    ↓
RDS PostgreSQL (Isolated Subnet, us-east-2 — no internet access)

Admin Access:
Corporate VPN → Admin Portal (Private Subnet, restricted security group)
```

**Security Groups:**
- Public subnet: allows 443 inbound from internet only
- Private subnet (app): allows traffic from ALB security group only
- Isolated subnet (DB): allows traffic from app security group only; no internet access
- Admin subnet: allows traffic from VPN IP range only
