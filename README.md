# Web Security Study Guide

*Security Study Guide · 2024 Edition*

**Comprehensive reference for OWASP Top 10, secure SDLC, authentication, injection, XSS, CSRF, SSRF, password storage, HTTP headers, containers & DevSecOps**

- OWASP Top 10 (2021)
- STRIDE / DREAD
- JWT Security
- SQL Injection
- XSS / CSRF
- SSRF / IDOR
- bcrypt / Argon2
- CSP / HSTS
- Docker Hardening
- DevSecOps

---

## Table of Contents

- [OWASP Top 10 (2021)](#owasp-top-10-2021)
- [Secure SDLC & Threat Modeling](#secure-sdlc--threat-modeling)
- [Authentication, JWT & Session Security](#authentication-jwt--session-security)
- [Injection Vulnerabilities](#injection-vulnerabilities)
- [XSS & CSRF Protections](#xss--csrf-protections)
- [SSRF, IDOR & Broken Access Control](#ssrf-idor--broken-access-control)
- [Secure Password Storage](#secure-password-storage)
- [HTTP Security Headers](#http-security-headers)
- [Docker Security Basics & Container Hardening](#docker-security-basics--container-hardening)
- [DevSecOps & Software Composition Analysis](#devsecops--software-composition-analysis)

---

## OWASP Top 10 (2021)

*The industry standard awareness document for web application security risks*

The Open Web Application Security Project (OWASP) Top 10 is a regularly updated report outlining security concerns for web application security, focusing on the 10 most critical risks. Understanding these is foundational to any security assessment or secure development practice.

**OWASP Top 10 (2021)**

| ID   | Category                                   | Description |
|------|--------------------------------------------|-------------|
| A01  | Broken Access Control                      | Restrictions on authenticated users are not properly enforced. Attackers can exploit these flaws to access unauthorized functionality or data. |
| A02  | Cryptographic Failures                     | Failures related to cryptography that lead to exposure of sensitive data (passwords, credit cards, PII). Formerly "Sensitive Data Exposure". |
| A03  | Injection                                  | User-supplied data is not validated, filtered, or sanitized. SQL, NoSQL, OS, and LDAP injection. |
| A04  | Insecure Design                            | Missing or ineffective control design. Emphasizes threat modeling and secure design patterns. |
| A05  | Security Misconfiguration                  | Missing hardening, unnecessary features, default credentials, verbose errors, unpatched systems. |
| A06  | Vulnerable & Outdated Components           | Using components with known vulnerabilities. Previously "Using Components with Known Vulnerabilities". |
| A07  | Identification & Auth Failures             | Incorrectly implemented authentication and session management. |
| A08  | Software & Data Integrity Failures         | Code and infrastructure without integrity verification. Insecure deserialization and insecure CI/CD pipelines. |
| A09  | Security Logging & Monitoring Failures     | Insufficient logging/monitoring allows attackers to pivot and persist. |
| A10  | Server-Side Request Forgery (SSRF)         | Application fetches a remote resource without validating the user-supplied URL. Bypasses firewalls/ACLs. |

> **Exam Tip:** Broken Access Control moved from #5 to **#1** in 2021, reflecting that 94% of applications were tested for some form of broken access control.

---

## Secure SDLC & Threat Modeling

*STRIDE · DREAD · Security Gates · Trust Boundaries*

### Secure Software Development Lifecycle (SSDLC)

The Secure SDLC integrates security practices into every phase of software development.

| Phase           | Security Activities |
|-----------------|----------------------|
| ① Requirements  | Define security requirements, compliance needs (PCI-DSS, HIPAA, GDPR), abuse/misuse cases. |
| ② Design        | Threat modeling, trust boundaries, data flows, security controls (defense in depth, least privilege). |
| ③ Implementation| Secure coding standards (CERT, OWASP), SAST, security-focused code reviews. |
| ④ Testing       | DAST, IAST, fuzz testing, penetration testing, SCA for vulnerable dependencies. |
| ⑤ Deployment    | Harden infrastructure/containers, enable WAF, logging, monitoring, secrets management. |
| ⑥ Maintenance   | Monitor CVEs, patch management, incident response, red team exercises. |

### Threat Modeling with STRIDE

STRIDE is a threat categorization model developed by Microsoft.

| Letter | Threat                | Violated Property  | Example |
|--------|----------------------|--------------------|----------|
| **S**  | Spoofing              | Authentication     | Using another user's credentials or IP |
| **T**  | Tampering             | Integrity          | Modifying data in transit or at rest |
| **R**  | Repudiation           | Non-repudiation    | Denying performing an action without logs |
| **I**  | Information Disclosure| Confidentiality    | Exposing data to unauthorized parties |
| **D**  | Denial of Service     | Availability       | Crashing or overloading a service |
| **E**  | Elevation of Privilege| Authorization      | Gaining admin rights without permission |

### Risk Scoring with DREAD

DREAD scores each factor 1–10 and averages them.

| Factor | Question                     | Score 10 means |
|--------|------------------------------|----------------|
| **D**amage | How bad if exploited?        | Full system takeover, data breach |
| **R**eproducibility | How easy to reproduce? | Always reproducible, no special timing |
| **E**xploitability | How easy to exploit?   | No skill required, script kiddie level |
| **A**ffected Users | How many are impacted? | All users / the entire system |
| **D**iscoverability | How easy to find?     | Publicly known, visible in browser |

> **Note:** DREAD has been deprecated by Microsoft internally but remains widely taught.

### Data Flow Diagrams (DFD) in Threat Modeling

1. **Decompose the application** – identify components, data flows, entry/exit points, trust boundaries.
2. **Apply STRIDE** – brainstorm threats for each component/flow.
3. **Rate threats (DREAD)** – prioritize by risk score.
4. **Mitigate** – define controls (prevent, detect, respond).

**Key Exam Points:**
- Security should be **shift-left** – integrated early.
- STRIDE maps to OWASP Top 10 (e.g., Spoofing → A07, Tampering → A08).
- DREAD score = (D+R+E+A+D)/5; High ≥7, Medium 4–6, Low ≤3.
- Other frameworks: PASTA, LINDDUN, VAST.

---

## Authentication, JWT & Session Security

*Identity · Tokens · Cookies · OAuth · MFA*

### Authentication vs. Authorization

- **Authentication (AuthN)** – *Who are you?* Verifies identity (passwords, MFA, biometrics, certificates).
- **Authorization (AuthZ)** – *What can you do?* Determines permissions (RBAC, ABAC, DAC, MAC).

### JSON Web Tokens (JWT)

Structure: `Header.Payload.Signature`
