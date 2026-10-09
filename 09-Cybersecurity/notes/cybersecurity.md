# Cybersecurity — Complete Revision Notes

> Covers: Fundamentals & CIA → Authentication & Access Control → Cryptography → TLS/HTTPS → Network Security → Web Security & OWASP → Malware & Social Engineering → Security Operations → Cheat Sheet

## Table of Contents
1. [Security Fundamentals](#1-security-fundamentals)
2. [Authentication & Access Control](#2-authentication--access-control)
3. [Cryptography](#3-cryptography)
4. [TLS & HTTPS](#4-tls--https)
5. [Network Security](#5-network-security)
6. [Web Application Security & OWASP](#6-web-application-security--owasp)
7. [Malware & Social Engineering](#7-malware--social-engineering)
8. [Security Operations](#8-security-operations)
9. [Ethics & Law](#9-ethics--law)
10. [Cheat Sheet](#10-cheat-sheet)

---

## 1. Security Fundamentals

### CIA Triad (the pillars)
| Property | Meaning | Controls |
|---|---|---|
| **Confidentiality** | Only authorized parties can read | Encryption, access control, classification |
| **Integrity** | Data isn't altered undetected | Hashes, digital signatures, checksums, versioning |
| **Availability** | Systems accessible when needed | Redundancy, backups, DDoS protection, DR |

Extensions: **Authenticity** (verified origin), **Non-repudiation** (sender can't deny sending — signatures + audit logs).

### Vocabulary that interviewers test
- **Threat** — potential danger (a hacker group). **Vulnerability** — weakness (unpatched CVE). **Risk** = likelihood × impact of threat exploiting vulnerability. **Exploit** — code/technique using a vulnerability.
- **AAA**: Authentication (who are you) → Authorization (what may you do) → Accounting (what did you do — logs).
- Defense in depth: layered controls — assume any single layer fails.
- Least privilege: every identity gets the minimum access needed.
- Zero trust: "never trust, always verify" — no implicit trust by network location; authenticate + authorize every request (micro-segmentation, MFA everywhere).

---

## 2. Authentication & Access Control

- **Authentication factors**:
  1. Something you **know** (password, PIN)
  2. Something you **have** (OTP token, phone, smart card)
  3. Something you **are** (fingerprint, face — biometrics)
  - **MFA/2FA** = factors from *different* categories. SMS OTP is weak (SIM-swap) — TOTP apps/passkeys are better; phishing-resistant = FIDO2/passkeys.
- **Password storage** (dev-interview favorite): never store plaintext or plain MD5/SHA — store `bcrypt/argon2/scrypt(password + unique random salt)`; salts kill rainbow tables, slow KDFs kill brute force. Enforce length > complexity-theater; check against breach lists.
- **Session security**: secure session IDs (high entropy), `HttpOnly` + `Secure` + `SameSite` cookies, expiry & rotation on login, invalidate server-side on logout.
- **Tokens**: SSO via OAuth 2.0 (delegated authorization) + OIDC (authentication layer); **JWT** = header.payload.signature (signed, self-contained, stateless — but not revocable before expiry; keep TTLs short, validate `aud`/`iss`).
- **Authorization models**: **RBAC** (roles → permissions — most common), **ABAC** (attribute/policy-based — finer grained), ACLs (per-object lists), least privilege as the guiding rule.
- Biometrics cautions: can't be rotated if leaked; use as a factor, not the only one.

---

## 3. Cryptography

### The trio (always confuse-proof this)
| | Encryption | Hashing | Encoding |
|---|---|---|---|
| Reversible? | Yes, with key | **No (one-way)** | Yes, no secret |
| Purpose | Secrecy | Integrity/verification (passwords) | Data transport (Base64) |
| Examples | AES, RSA, ChaCha20 | SHA-256, bcrypt (passwords: Argon2) | Base64, URL-encode |

**Base64 is NOT encryption** — a favorite trap question.

### Symmetric vs asymmetric
| | Symmetric | Asymmetric |
|---|---|---|
| Keys | One shared secret | Key pair (public + private) |
| Speed | Fast (bulk data) | Slow (~1000×) |
| Key exchange | Hard problem | Public keys solve it |
| Examples | **AES** (standard), ChaCha20, 3DES (legacy) | **RSA**, ECC/ECDSA, Diffie-Hellman |
| Use | Data encryption (TLS session data, disk) | Handshakes, signatures, key exchange |

**Hybrid systems** (TLS, PGP): asymmetric crypto to agree on a key → symmetric crypto for the actual data.

- **Block vs stream ciphers**: block = fixed chunks with modes (ECB is insecure — patterns leak; use GCM/CBC); stream = keystream XOR (fast, used in TLS 1.3's ChaCha20).
- **Hashes**: MD5 & SHA-1 are **broken** for security (collisions demonstrated); use SHA-256/SHA-3; for passwords use **slow, salted** KDFs (bcrypt/scrypt/argon2) — fast hashes are wrong for passwords.
- **Salt**: random per-password value stored alongside the hash — defeats precomputed rainbow tables (identical passwords hash differently).
- **HMAC**: hash + secret key → integrity + authenticity of messages.
- **Digital signature**: hash the message → encrypt hash with sender's **private** key; anyone verifies with the public key → integrity + authenticity + **non-repudiation**. (Encryption direction is opposite: encrypt *to* the recipient's public key.)
- **PKI & certificates**: CA (trusted third party) signs the server's public key inside a **certificate** (subject, SAN domains, validity, signature chain to a root in your trust store). Revocation: CRL/OCSP.
- **Diffie-Hellman**: two parties derive a shared secret over a public channel (g^a mod p math); ECDHE = ephemeral DH on elliptic curves → **forward secrecy** (compromised long-term key can't decrypt past sessions).
- Steganography: hiding *existence* of a message (in images/media) — distinct from cryptography.

---

## 4. TLS & HTTPS

**HTTPS = HTTP over TLS** → provides confidentiality (encryption), integrity (AEAD), and authentication (certificates). Port 443.

### Handshake (TLS 1.2 simplified; TLS 1.3 = 1-RTT)
1. **ClientHello**: supported TLS versions, cipher suites, client random, key share.
2. **ServerHello** + **certificate chain** + key share: client verifies cert (trusted CA chain, domain match, expiry, not revoked).
3. **Key agreement**: ECDHE → both sides independently compute the same session secret (forward secrecy).
4. Both derive symmetric session keys; a `Finished` MAC proves the handshake wasn't tampered with.
5. Application data flows encrypted with AES-GCM/ChaCha20.

Key points to say out loud: asymmetric crypto only authenticates & agrees keys (slow), symmetric crypto protects the data (fast); certificates bind domains to public keys via CAs; **HSTS** forces browsers to use HTTPS; mixed content undermines it.

---

## 5. Network Security

### Firewalls
| Type | Inspects | Notes |
|---|---|---|
| Packet filter | L3/L4 headers, stateless (ACLs) | Fast, crude |
| **Stateful** | Connection state tables | Default modern firewall |
| Application/WAF | L7 payloads (HTTP) | Blocks SQLi/XSS patterns at the edge |
| NGFW | All + TLS inspection, IDS/IPS, threat intel | Enterprise standard |

### IDS vs IPS
- **IDS**: passive monitoring → **alerts** (NIDS on the wire, HIDS on a host's logs/files).
- **IPS**: inline → **blocks** in real time (risk: false positives block legit traffic).
- Detection styles: **signature-based** (known patterns; blind to zero-days) vs **anomaly-based** (baseline deviations; noisy but catches new attacks).

### Secure architecture pieces
- **VPN**: encrypted tunnel over untrusted networks (WireGuard, IPsec, OpenVPN) — remote access & site-to-site. Under zero trust, per-app access (ZTNA) is replacing flat VPN access.
- **DMZ**: buffer subnet for public-facing servers; compromise there doesn't reach the internal LAN.
- **Segmentation/micro-segmentation**: VLANs, security zones — contain lateral movement.
- **NAT**: not a security control (but hides internal topology).

### Classic network attacks
| Attack | How | Defense |
|---|---|---|
| **MITM** | Intercept/alter traffic (rogue Wi-Fi, ARP spoofing) | TLS everywhere, cert pinning, Dynamic ARP inspection |
| **DDoS** | Flood from botnets (volumetric, protocol, application-layer) | CDN/scrubbing, rate limiting, autoscaling, anycast |
| DNS spoofing/poisoning | Fake DNS responses cached | DNSSEC, DoH/DoT |
| Port scanning / recon | nmap sweeps to map services | Minimal exposed surface, monitoring, honeypots |
| IP/TCP attacks | SYN flood, spoofing | SYN cookies, egress filtering |

---

## 6. Web Application Security & OWASP

### OWASP Top 10 (2021) — know the list + one example & fix each
1. **Broken Access Control** — user A reads user B's data by changing an ID (**IDOR**). Fix: server-side authorization on every object, deny by default.
2. **Cryptographic Failures** — plaintext storage, weak algorithms, missing TLS. Fix: modern crypto, TLS, salted password hashing.
3. **Injection** (SQLi, command injection, LDAP) — untrusted input becomes code. Fix: **parameterized queries/ORMs**, least-priv DB users, input validation, no `eval`.
4. **Insecure Design** — flawed architecture (missing rate limits on OTP). Fix: threat modeling, secure design patterns.
5. **Security Misconfiguration** — default creds, verbose errors, open S3 buckets, debug on in prod. Fix: hardening baselines, IaC review.
6. **Vulnerable & Outdated Components** — old libraries with known CVEs (Log4Shell). Fix: SCA scanning, patch cadence, SBOM.
7. **Identification & Authentication Failures** — weak passwords, missing MFA, predictable sessions, credential stuffing. Fix: MFA, lockouts/rate limits, secure session handling.
8. **Software & Data Integrity Failures** — unsigned updates/CI (supply chain: SolarWinds), insecure deserialization. Fix: signed artifacts, verify dependencies, avoid deserializing untrusted data.
9. **Security Logging & Monitoring Failures** — breaches linger months undetected. Fix: central logs, alerting on anomalies, IR runbooks (see §8).
10. **SSRF** — server tricked into fetching internal URLs (metadata endpoints!). Fix: allow-list egress, block link-local IPs, no raw URL fetching from users.

### The web-attack classics (be able to contrast)
| Attack | What | Prevention |
|---|---|---|
| **XSS** (reflected / stored / DOM) | Attacker's JavaScript runs in *victim's browser* via injected input | Context-aware output escaping, CSP, HttpOnly cookies, sanitization frameworks |
| **CSRF** | Victim's browser *sends authenticated request* to a site that trusts the session cookie | Anti-CSRF tokens, `SameSite=Lax/Strict` cookies, checking Origin |
| **SQLi** | Input becomes SQL (`' OR 1=1 --`) → dump/modify DB | Parameterized queries, ORMs, least-privilege DB accounts, WAF as extra |
| **IDOR** | Direct object references without ownership checks | Server-side authz per object, opaque IDs |
| Clickjacking | Victim clicks a hidden iframe button | `X-Frame-Options: DENY`, CSP `frame-ancestors` |
| Open redirect | Trusted site redirects to attacker URL | Allow-list redirect targets |

### Security headers (dev-interview favorite)
`Content-Security-Policy` (script allow-list — the XSS killer), `Strict-Transport-Security` (force HTTPS), `X-Content-Type-Options: nosniff`, `X-Frame-Options`/`frame-ancestors`, `Referrer-Policy`, plus CORS done right (CORS is *relaxation* of same-origin — not a defense itself).

---

## 7. Malware & Social Engineering

### Malware taxonomy
| Type | Defining trait |
|---|---|
| Virus | Attaches to files; needs user action to spread |
| **Worm** | Self-propagates over networks (no host file) |
| **Trojan** | Disguised as legitimate software |
| **Ransomware** | Encrypts data, demands payment (WannaCry) — backups are the antidote |
| Spyware/keylogger | Stealth data theft |
| Rootkit | Hides at kernel/deep level; survives many cleanups |
| **Botnet** | Enslaved devices used for DDoS/spam |
| Fileless | Lives in memory/scripts — evades file-based AV |

Defenses: EDR/AV, patching, least privilege, app allow-listing, offline + tested backups (3-2-1 rule), email filtering.

### Social engineering — humans are the #1 vector
- **Phishing** (mass email), **spear phishing** (targeted), **whaling** (executives), **vishing/smishing** (voice/SMS), baiting (infected USB), **tailgating** (physical door), pretexting (fake authority).
- Countermeasures: awareness training + simulated phishes, MFA (limits stolen-password damage), verification procedures for money/credential requests, report buttons and fast takedowns.

---

## 8. Security Operations

- **SOC** (Security Operations Center) — the team; **SIEM** (Splunk, Sentinel, Elastic) — centralizes logs, correlates, alerts; **SOAR** — automates response playbooks.
- **Incident response lifecycle (NIST)**: 1) **Preparation** (playbooks, tooling, backups) → 2) **Detection & Analysis** (triage alerts, scope, severity) → 3) **Containment** (isolate hosts, revoke keys) → 4) **Eradication** (remove malware, patch the hole) → 5) **Recovery** (restore, monitor for re-infection) → 6) **Lessons Learned** (post-mortem, improve controls).
- **Vulnerability management**: scan (Nessus/OpenVAS) → prioritize (**CVSS** severity score; exploitability & exposure) → patch/mitigate → verify. **CVE** = public vuln ID; **CVE + CVSS + KEV** drive patching priority.
- **Penetration testing** (authorized, scoped): 1) Recon (passive OSINT → active scanning) → 2) Scanning/enumeration → 3) Exploitation → 4) Post-exploitation (privilege escalation, lateral movement, persistence) → 5) Reporting. Distinguish **red team** (goal-based, stealthy) vs **pentest** (scope-bounded) vs **bug bounty** (continuous, crowdsourced, coordinated disclosure).
- **Backup hygiene**: 3-2-1 — 3 copies, 2 media, 1 **offline/offsite** (ransomware hunts backups first); test restores.
- Logging essentials: centralized, time-synced (NTP), immutable, enough context (who/what/when/where), retained per policy.

---

## 9. Ethics & Law

- **Hat colors**: white (authorized), grey (unauthorized but benign intent), black (malicious).
- Golden rule: **written authorization** before testing anything you don't own. Scope, rules of engagement, safe handling of found data.
- Responsible/Coordinated disclosure: report privately, allow fix time, publish after patch.
- Legal landscape (know the names): India — **IT Act 2000** (with amendments; Sections 43, 66, 66C/D cover unauthorized access, identity theft), **DPDP Act 2023** (data protection). EU — **GDPR** (privacy, breach notification, fines). US — HIPAA (health), PCI-DSS (card data, an industry standard), SOX.
- Careers/paths: SOC analyst → pentester/appsec → security engineer → CISO; blue team (defense) vs red team (offense) vs purple (both).

---

## 10. Cheat Sheet

| Concept | One-liner |
|---|---|
| CIA | Confidentiality, Integrity, Availability (+ non-repudiation) |
| AuthN vs AuthZ | Who you are vs what you may do |
| MFA | Factors from *different* categories (know + have + are) |
| Encryption vs hashing vs encoding | Reversible w/ key vs one-way digest vs reversible w/o secret |
| Symmetric vs asymmetric | AES fast shared-key vs RSA/ECC key-pairs for handshake/signatures |
| Salt | Random per-password value that kills rainbow tables |
| Why MD5/SHA1 broken | Practical collisions; use SHA-256; passwords → bcrypt/argon2 |
| Digital signature | Hash signed with *private* key → integrity + authenticity + non-repudiation |
| TLS handshake | Cert auth → ECDHE key exchange → symmetric session encryption |
| Forward secrecy | Ephemeral keys ⇒ past sessions safe even if long-term key leaks |
| IDS vs IPS | Detect & alert (passive) vs detect & block (inline) |
| Firewall types | Packet filter → stateful → WAF/app-layer → NGFW |
| XSS vs CSRF | Malicious JS runs *in your page* vs forged request *sent as you* |
| SQLi fix | Parameterized queries/ORM — never string-concatenate SQL |
| CSP | Header allow-listing scripts/styles — strongest XSS mitigation |
| IDOR | Missing object-level authorization check |
| SSRF | Server tricked into fetching internal URLs (block link-local) |
| OWASP #1 (2021) | Broken Access Control |
| Zero trust | Never trust network location; verify every request |
| 3-2-1 backups | 3 copies, 2 media, 1 offline/offsite — and test restores |
| IR lifecycle | Prepare → Detect → Contain → Eradicate → Recover → Learn |
| CVSS/CVE | Severity score / vulnerability identifier for prioritization |
| Pentest phases | Recon → Scan → Exploit → Post-exploit → Report |
