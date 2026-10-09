# Computer Networks — Top 30 Interview Questions (with Answers)

> Ordered basics → advanced. Q1 and Q9 are near-guaranteed in any interview.

---

### 1. Explain the OSI model and what each layer does.
A 7-layer reference model: **Physical** (bits on media), **Data Link** (framing, MAC addresses, hop-to-hop delivery, error detection — Ethernet), **Network** (logical addressing & routing — IP), **Transport** (end-to-end delivery, ports, reliability — TCP/UDP), **Session** (dialog setup/sync), **Presentation** (format, encryption, compression), **Application** (network services to apps — HTTP, DNS, SMTP). Know one function + one protocol per layer, and which device lives there (hub L1, switch L2, router L3).

### 2. OSI vs TCP/IP model?
TCP/IP is the 4-layer model the Internet actually implements: Application (merges OSI 5–7), Transport, Internet, Network Access. OSI is a reference/teaching model; TCP/IP predates and practically drives it. Protocols were standardized after the TCP/IP layers existed, whereas OSI was a model first.

### 3. TCP vs UDP — when would you choose each?
TCP: connection-oriented, reliable, ordered, flow + congestion control, ~20–60 byte header — use when every byte matters (web, email, file transfer, SSH). UDP: connectionless, unreliable, unordered, tiny 8-byte header, minimal latency — use when speed beats completeness (DNS, VoIP, video calls, gaming) or when you build reliability yourself (QUIC, TFTP).

### 4. Explain the TCP 3-way handshake. Why not 2-way?
SYN (client, seq=x) → SYN+ACK (server, seq=y, ack=x+1) → ACK (client, ack=y+1). Three exchanges are needed so **both** sides confirm both directions work and agree on initial sequence numbers; with 2-way the server couldn't verify the client can receive, and an old duplicate SYN could create a bogus half-open connection.

### 5. What is TIME_WAIT in TCP termination?
After the active closer sends the final ACK of the 4-way FIN exchange, it waits 2×MSL (typically 60s): (a) if the peer's final FIN retransmits (its ACK was lost), it can re-ACK; (b) old duplicate segments from the connection die out so they can't corrupt a future connection reusing the same ports.

### 6. What happens when you type a URL in the browser?
Cache checks → **DNS** resolution to an IP → optional **ARP** for gateway MAC → **TCP** 3-way handshake → **TLS** handshake (cert verification, key exchange) → **HTTP** GET request → server (LB → web/app/DB) responds → browser parses and renders HTML/CSS/JS (fetching subresources) → keep-alive or FIN. Explain it layer by layer — this one question lets you demonstrate the whole syllabus.

### 7. How does DNS resolution work step by step?
Browser/OS caches miss → query the **recursive resolver** (ISP or 8.8.8.8) → resolver asks a **root server** (who handles .com?) → **TLD server** (who handles example.com?) → **authoritative server** returns the A record. Resolver caches per TTL and answers the client. UDP 53 normally; TCP 53 for large responses/zone transfers.

### 8. What is ARP? How does it work?
Address Resolution Protocol maps an IP to a MAC inside a LAN. Host broadcasts "who has 192.168.1.7?" → owner replies (unicast) with its MAC → sender caches the mapping. Routers don't forward ARP — it's strictly local. (Attack variant: ARP spoofing/MITM.)

### 9. TCP vs IP — difference between them?
IP (network layer) does best-effort delivery of packets between hosts using logical addresses — no guarantees. TCP (transport layer) sits on top of IP and adds reliability: connections, sequencing, ACKs, retransmission, flow/congestion control, and ports to multiplex applications. IP gets it there eventually; TCP makes it correct and in-order.

### 10. MAC address vs IP address?
MAC: 48-bit physical, burned into the NIC, used for delivery *within* a LAN, changes meaning as frames cross networks get re-written per hop. IP: 32/128-bit logical, hierarchical, used for *end-to-end* routing across networks, can change (DHCP/roaming). Analogy: IP is the postal address; MAC is the person's name on the door.

### 11. IPv4 vs IPv6?
IPv4: 32-bit, ~4.3B addresses, NAT required to stretch supply, broadcast exists, header checksum. IPv6: 128-bit, effectively unlimited, no NAT needed, multicast replaces broadcast, simplified header (no checksum), built-in SLAAC autoconfiguration, IPsec support. Transition via dual-stack and tunneling.

### 12. Do a quick subnetting question: how many hosts in 192.168.1.0/26?
/26 → 32−26 = 6 host bits → 2⁶ = 64 addresses → **62 usable hosts** (subtract network .0 and broadcast .63). Subnets step every 64: .0–.63, .64–.127, .128–.191, .192–.255. Mask: 255.255.255.192. Practice until this takes 20 seconds.

### 13. What is NAT and why do we need it?
Network Address Translation lets many private-IP devices share one public IP: the router rewrites source IP:port to its own and remembers the mapping for return traffic. Needed because IPv4 addresses ran out (RFC 1918 private ranges + NAT). Trade-off: breaks end-to-end reachability (inbound connections need port forwarding).

### 14. Circuit switching vs packet switching?
Circuit: dedicated path reserved end-to-end before transfer (classic telephony) — guaranteed bandwidth, wasteful when idle. Packet: messages split into packets routed independently, links shared statistically — efficient, resilient, but with queuing delay and congestion risk. The Internet is packet-switched.

### 15. Flow control vs congestion control?
Flow control protects the **receiver** — the advertised window stops the sender from overflowing the receiver's buffer. Congestion control protects the **network** — the congestion window (slow start, AIMD, fast recovery) stops the sender from flooding routers. Effective send window = min(rwnd, cwnd).

### 16. Explain TCP slow start and congestion avoidance.
Slow start: cwnd starts at ~1–10 MSS and **doubles every RTT** (exponential probe) until it hits ssthresh. Then congestion avoidance grows cwnd **linearly** (+1 MSS/RTT) — AIMD. Three duplicate ACKs → fast retransmit + halve cwnd; a full timeout → ssthresh = cwnd/2 and back to slow start from 1.

### 17. Go-Back-N vs Selective Repeat?
Both are sliding-window protocols. GBN: receiver only accepts in-order; a single lost frame forces retransmission of that frame **and everything after** (cheap receiver, wasteful sender). SR: receiver buffers out-of-order frames and ACKs each individually; only the missing frame is retransmitted (more receiver buffers, efficient links).

### 18. Why is HTTP stateless and how do sessions work then?
Each HTTP request is independent — the server keeps no per-client memory between requests (simplifies scaling). State is added at the application layer: server issues a **cookie** (session ID) that the browser echoes back on every request; the server maps that ID to session data. Alternatives: tokens (JWT) in headers.

### 19. HTTP vs HTTPS? What does TLS give you?
HTTPS = HTTP over TLS. TLS provides **encryption** (no eavesdropping), **integrity** (tamper detection), and **authentication** (server proves identity via a CA-signed certificate). Default port 443. Costs: handshake latency (mitigated by session resumption/TLS 1.3) and CPU — negligible today.

### 20. Explain the TLS handshake.
ClientHello (TLS versions, cipher suites, random) → ServerHello + **certificate chain** + key-share → client verifies certificate against trusted CAs (name match, expiry, chain) → both perform ECDHE key exchange to derive the same **symmetric session keys** (asymmetric crypto only for the handshake) → encrypted application data flows. TLS 1.3 compresses this to 1-RTT.

### 21. HTTP methods and idempotency?
GET (read, safe), POST (create/process, not idempotent), PUT (replace, idempotent), DELETE (idempotent), PATCH (partial update, not guaranteed idempotent), HEAD/OPTIONS (metadata). **Idempotent** = same request repeated has the same effect as once — matters for retries on flaky networks.

### 22. Common HTTP status codes you should know.
200 OK · 201 Created · 301 Moved Permanently · 302 Found (temporary) · 304 Not Modified (cache) · 400 Bad Request · **401 Unauthorized** (not authenticated) · **403 Forbidden** (authenticated but not allowed) · 404 Not Found · 429 Too Many Requests · 500 Internal Server Error · 502 Bad Gateway · 503 Service Unavailable.

### 23. HTTP/1.1 vs HTTP/2 vs HTTP/3?
1.1: persistent connections, pipelining — but **head-of-line blocking** (one slow response delays the rest on that connection). 2: binary framing + **multiplexing** many streams over one connection, header compression (HPACK) — fixes HTTP-level HOL but TCP-level HOL remains. 3: moves to **QUIC over UDP** — streams are independent, TLS 1.3 built in, faster handshake, no TCP HOL blocking.

### 24. Cookie vs session vs token (JWT)?
Cookie: browser storage automatically sent per request — the transport. Session: server-side state keyed by a cookie session ID — easy to revoke, needs shared state across servers. JWT: self-contained signed token (header.payload.signature) stored client-side — stateless and horizontally scalable, but can't be revoked before expiry (needs blacklists/short TTLs).

### 25. SMTP vs POP3 vs IMAP?
SMTP **pushes** mail between clients→servers and server→server (sending). POP3 downloads mail to one device, usually deleting the server copy. IMAP keeps mail on the server and syncs folders/state across devices — the default for modern multi-device email.

### 26. Hub vs switch vs router?
Hub (L1): repeats bits to every port — one big collision domain, obsolete. Switch (L2): learns source MACs into a table and forwards frames only to the right port — each port its own collision domain. Router (L3): forwards packets between different networks using routing tables; separates broadcast domains.

### 27. What is a socket? Name well-known ports.
A socket is the (IP, port, protocol) endpoint of a transport connection. Ports: 20/21 FTP, 22 SSH, 23 Telnet, 25 SMTP, 53 DNS, 67/68 DHCP, 80 HTTP, 110 POP3, 143 IMAP, 443 HTTPS, 3389 RDP.

### 28. How do ping and traceroute work?
**Ping** sends ICMP Echo Request and measures Echo Reply round-trip time — tests reachability. **Traceroute** exploits the IP TTL field: sends packets with TTL=1,2,3…; each router that drops a packet at TTL=0 returns ICMP "Time Exceeded", revealing its address — thus mapping the path hop by hop.

### 29. IDS vs IPS? Firewall types?
IDS passively monitors and **alerts**; IPS sits inline and **blocks**. Both use signature (known patterns) or anomaly (deviations) detection. Firewalls: packet-filtering (L3/L4 rules), stateful (tracks connection state), application/WAF (inspects L7 payloads), NGFW (all of it + TLS inspection, threat intel).

### 30. What is a VPN and how is it different from a proxy?
A VPN creates an encrypted tunnel from your device to a VPN server at the IP layer — **all** traffic is protected and you inherit the server's IP; strong privacy/integrity (IPsec, WireGuard, OpenVPN). A proxy usually relays only specific application traffic (e.g., HTTP) and often without encryption — fine for IP masking per-app, weaker for privacy.

---

## 💡 How to answer CN questions well
- Anchor answers in **layers** ("at the transport layer this happens…") — instant structure.
- Draw the packet journey when stuck: app → TCP → IP → Ethernet → wire.
- Memorize the port table and the status-code buckets — they're free marks.
- Practice 5–10 subnetting sums; it's the only "math" in CN interviews.
