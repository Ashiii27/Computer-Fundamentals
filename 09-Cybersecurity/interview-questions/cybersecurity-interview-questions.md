# Cybersecurity — Top 30 Interview Questions (with Answers)

> Ordered fundamentals → crypto → web → network → ops. Even non-security SDE roles ask Q1–Q15 regularly.

---

### 1. Explain the CIA triad.
**Confidentiality** — only authorized parties can read data (encryption, access control). **Integrity** — data can't be altered undetected (hashes, signatures, audit trails). **Availability** — systems are usable when needed (redundancy, DDoS protection, backups, DR). Every control you name should map to at least one pillar; extensions include authenticity and non-repudiation.

### 2. Authentication vs authorization?
Authentication verifies *identity* (password, OTP, certificate); authorization checks *permissions* for that identity (RBAC/ABAC policies). AuthN first, AuthZ second — and authorization must be re-checked **server-side on every request and every object** (that's exactly what broken-access-control findings violate).

### 3. Encryption vs hashing vs encoding?
Encryption: reversible with a key — protects confidentiality (AES). Hashing: one-way digest — protects integrity/verification; can't "decrypt" a password hash (SHA-256, bcrypt). Encoding: reversible transformation with **no secret** — for transport/format compatibility (Base64, URL-encoding). Saying "we encrypt passwords with Base64" would be two errors in four words.

### 4. Symmetric vs asymmetric encryption — when is each used?
Symmetric (AES, ChaCha20): one shared key, fast — encrypts bulk data (TLS session, disk). Asymmetric (RSA, ECC): key pair, slow — solves key distribution and digital signatures. Real systems are **hybrid**: asymmetric handshake/key-exchange → symmetric for the traffic. Password storage is neither: salted slow hashing.

### 5. How does HTTPS work? Walk through the TLS handshake.
TCP connect → **ClientHello** (versions, ciphers, key share) → server responds with its **certificate chain** → client validates the cert (CA chain, domain, expiry, revocation) → **ECDHE key agreement** gives both sides the same session secret without sending it → symmetric session keys encrypt all traffic (AES-GCM). TLS gives confidentiality + integrity + server authentication; TLS 1.3 does it in one round-trip with forward secrecy.

### 6. What is a digital signature? How is it different from encryption?
Sign: hash the message, encrypt the hash with your **private** key; anyone verifies with your public key. Provides integrity, authenticity, and non-repudiation (you can't deny signing). Encryption's goal is confidentiality — for that you encrypt *to the recipient's public key*. TLS uses both: signatures for identity, encryption for secrecy.

### 7. What is a certificate and why do we trust it?
A CA-signed structure binding a domain to a public key (subject, SANs, validity, signature). Trust comes from the chain: the site's cert is signed by an intermediate, whose root is pre-installed in your OS/browser trust store. Browsers also check expiry and revocation (CRL/OCSP). You're trusting the CA's vetting, which is why CA compromise is catastrophic.

### 8. Why are MD5 and SHA-1 considered broken? What should be used?
Collision attacks are practical — different inputs can be crafted with the same hash, defeating signatures and integrity checks built on them. Use SHA-256/SHA-3 for general hashing. For **passwords**, use slow, salted, memory-hard KDFs: bcrypt, scrypt, or argon2 — a fast hash (even SHA-256) is wrong because GPUs brute-force it cheaply.

### 9. What is salting? Why do it?
A unique random value per password, hashed together with it and stored alongside. Without salts, attackers precompute **rainbow tables** for common passwords and crack entire leaked databases at once; with unique salts, each hash needs individual brute-force — and identical passwords produce different hashes. Modern KDFs (bcrypt/argon2) salt automatically.

### 10. What is XSS? Types and prevention?
Attacker input becomes JavaScript executing in other users' browsers — stealing sessions, defacing, keylogging. **Reflected** (payload in the request echoed back), **Stored** (persisted — comment field — hits everyone), **DOM-based** (client JS writes unsanitized input into the page). Prevention: context-aware output **escaping**, templating engines with auto-escape, **Content-Security-Policy** to block inline scripts, `HttpOnly` cookies so JS can't read session tokens, sanitizing rich input with vetted libraries.

### 11. What is SQL injection and how do you prevent it?
Untrusted input concatenated into SQL changes its meaning: `' OR '1'='1` in a login field, `; DROP TABLE` beyond. Prevention: **parameterized queries / prepared statements / ORMs** (data can never become code), least-privilege DB accounts, allow-list validation, and a WAF as belt-and-suspenders. Demonstrate awareness: `String q = "SELECT * FROM users WHERE name='" + input + "'"` is the interview red flag.

### 12. XSS vs CSRF — what's the difference?
**XSS**: attacker's *code runs inside* your site's page in the victim's browser (exploits trust a user has in your site). **CSRF**: attacker tricks the *victim's browser into sending* an authenticated request to your site — the session cookie rides along (exploits trust your site has in the user's browser). Fixes respectively: output escaping/CSP vs anti-CSRF tokens & `SameSite` cookies. XSS is strictly worse (it can also defeat CSRF defenses).

### 13. What is CORS? Is it a security feature?
Cross-Origin Resource Sharing — a browser mechanism letting servers **relax** the same-origin policy for chosen origins via headers (`Access-Control-Allow-Origin`). It's a permission grant, not a defense: it constrains browsers, not attackers with curl. Actual protections remain authN/authZ, CSRF tokens, and input validation.

### 14. Name the security headers you'd set and why.
`Content-Security-Policy` (script/style allow-list — the strongest XSS mitigation), `Strict-Transport-Security` (HSTS — force HTTPS, block downgrade), `X-Content-Type-Options: nosniff`, `X-Frame-Options: DENY` / CSP `frame-ancestors` (clickjacking), `Referrer-Policy`, `Permissions-Policy`. Plus cookies: `Secure`, `HttpOnly`, `SameSite`.

### 15. What is the OWASP Top 10? Name several entries.
A prioritized industry list of the most critical web app risks (2021): Broken Access Control (incl. IDOR), Cryptographic Failures, Injection, Insecure Design, Security Misconfiguration, Vulnerable & Outdated Components, Identification & Authentication Failures, Software & Data Integrity Failures, Security Logging & Monitoring Failures, SSRF. Interviewers want the flavor of each plus one fix — see the notes table.

### 16. What is IDOR?
Insecure Direct Object Reference — a form of broken access control where changing an identifier (`/invoices/1042` → `/1043`) reveals another user's data because the server never checked ownership. Fix: object-level authorization on every access, opaque/unpredictable IDs as defense-in-depth, and access-control tests in CI.

### 17. What is SSRF?
Server-Side Request Forgery — attacker supplies a URL your server fetches, aiming it at internal targets (metadata services like `169.254.169.254`, admin panels). Impact: cloud credentials theft, internal port scanning. Fixes: allow-list external domains, block link-local/private ranges, disable redirect following, isolate the fetching service.

### 18. What is a DDoS attack and how is it mitigated?
Distributed Denial of Service — a botnet floods a target (volumetric: bandwidth; protocol: SYN floods; application-layer: HTTP request floods). Mitigation: CDN/anycast scrubbing centers absorb volume, rate limiting + WAF rules for L7, autoscaling for tolerance, and graceful degradation (queues) rather than total collapse.

### 19. What is a MITM attack and what stops it?
An attacker positioned between two parties intercepts/relays/alters traffic (rogue Wi-Fi, ARP spoofing, malicious proxy). Defense: TLS — the certificate check makes silent impersonation infeasible — plus HSTS, certificate pinning for high-value apps, and LAN hardening (dynamic ARP inspection, 802.1X).

### 20. IDS vs IPS?
IDS passively monitors and **alerts**; IPS sits inline and **blocks** in real time. Detection styles: signature-based (precise, blind to zero-days) vs anomaly-based (catches novel behavior, more false positives). Modern reality: much of this lives inside NGFWs and cloud-native detection (GuardDuty-type services) feeding a SIEM.

### 21. What are the firewall types?
Packet-filtering (L3/L4 rules, stateless), stateful (tracks connections — the baseline), application/WAF (inspects HTTP payloads, blocks SQLi/XSS patterns), NGFW (stateful + app awareness + IDS/IPS + TLS inspection + threat intel). Default-deny egress as well as ingress is the mature configuration.

### 22. What is zero trust?
"Never trust, always verify" — drop the assumption that inside-the-network is safe. Every request is authenticated (strong/MFA), authorized (least privilege), and often device-posture-checked; networks are micro-segmented so one compromise doesn't go lateral. Practically: identity-centric access (ZTNA) replacing flat VPN access, per-service mTLS, continuous logging.

### 23. How should passwords be stored? What about password rules?
Store `argon2id` (or bcrypt, cost factor tuned) with a unique salt — never plaintext, never fast hashes, never reversible encryption. Policy wisdom has shifted: enforce **length** (12+), check against breach corpora, allow passphrases/managers, drop forced periodic rotation and composition-theater rules (they drive weak patterns). Add MFA — it rescues most password sins.

### 24. What is MFA? Why can SMS be weak?
MFA requires factors from different categories (knowledge, possession, biometrics), so one phished password isn't enough. SMS OTP's weaknesses: SIM-swap attacks, SS7 interception, and real-time relay phishing (attacker prompts you and uses the code instantly). Stronger: authenticator apps (TOTP) and phishing-resistant **FIDO2/passkeys**, which cryptographically bind to the real domain.

### 25. Malware types — virus vs worm vs trojan vs ransomware?
Virus: attaches to files, spreads via user action. Worm: self-replicates over the network (WannaCry's spread component). Trojan: disguises itself as legit software. Ransomware: encrypts and extorts — the answer is offline, tested backups (3-2-1), plus patching and EDR. Also: spyware, rootkits (deep hiding), botnets (rented DDoS capacity), fileless malware (memory-resident).

### 26. Phishing vs spear phishing? How do organizations fight it?
Phishing: mass, generic lures. Spear phishing: researched, personalized targeting (often the initial access of real breaches); whaling targets executives. Defenses: awareness training + simulated campaigns, mail authentication (SPF/DKIM/DMARC), MFA to blunt credential capture, out-of-band verification for payment/credential requests, easy reporting buttons.

### 27. Walk through the incident response lifecycle.
NIST: **Preparation** (playbooks, tooling, backups) → **Detection & Analysis** (triage SIEM alerts, scope and severity) → **Containment** (isolate hosts, rotate keys, block C2) → **Eradication** (remove persistence, patch the entry point) → **Recovery** (restore, monitor closely for re-infection) → **Lessons Learned** (blameless post-mortem, close control gaps). Speed of containment and quality of logs decide the blast radius.

### 28. What are penetration testing phases? Red team vs pentest vs bug bounty?
Pentest: scoping/rules → **recon** (passive OSINT, then active) → scanning/enumeration → exploitation → post-exploitation (privilege escalation, lateral movement) → reporting. Red team: goal-based, stealthy, emulates a real adversary over weeks. Bug bounty: continuous crowdsourced testing with coordinated disclosure. All require **written authorization** — that's the ethical line.

### 29. What are CVE and CVSS?
CVE: globally unique identifier for a publicly disclosed vulnerability (CVE-2021-44228 = Log4Shell). CVSS: 0–10 severity score from exploitability and impact vectors (9.8 = critical). Operations combine them with exposure and real-world exploitation feeds (CISA KEV) to decide patch priority — "critical CVE on an internet-facing box" jumps the queue.

### 30. What is the 3-2-1 backup rule and why does it matter for ransomware?
Three copies of data, on two different media, one kept **offline/offsite** (immutable ideally) — because modern ransomware actively finds and encrypts connected backups before detonating. Backups convert a catastrophic event into a restore job — but only restores that have actually been **tested** count.

---

## 💡 How to answer security questions well
- Structure answers as: attack → impact → defense (in layers).
- Name the standards (OWASP, NIST, CVSS) — signals professional familiarity.
- For dev-role interviews, be strongest on Q3–Q15 (crypto, TLS, XSS/CSRF/SQLi, headers) — that's where they live.
- Never skip the ethics line: authorization before testing anything.
