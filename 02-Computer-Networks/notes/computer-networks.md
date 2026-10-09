# Computer Networks — Complete Revision Notes

> Covers: Network basics → Layered models → Data Link → Network Layer & IP → Transport (TCP/UDP) → Application protocols → Devices → End-to-end URL story

## Table of Contents
1. [Networking Basics](#1-networking-basics)
2. [Layered Models: OSI & TCP/IP](#2-layered-models-osi--tcpip)
3. [Data Link Layer](#3-data-link-layer)
4. [Network Layer (IP)](#4-network-layer-ip)
5. [Transport Layer (TCP & UDP)](#5-transport-layer-tcp--udp)
6. [Application Layer](#6-application-layer)
7. [Network Devices](#7-network-devices)
8. [Network Security Basics](#8-network-security-basics)
9. [What Happens When You Type a URL](#9-what-happens-when-you-type-a-url)
10. [Cheat Sheet](#10-cheat-sheet)

---

## 1. Networking Basics

- **Network**: interconnection of devices that can exchange data. Types by scale: **PAN → LAN → MAN → WAN** (the Internet is the biggest WAN).
- **Topologies**: bus, ring, star (most common LAN), mesh (max reliability, n(n−1)/2 links), tree, hybrid.
- **Switching**:
  | Circuit switching | Packet switching |
  |---|---|
  | Dedicated path reserved first (telephone) | Data split into packets, routed independently |
  | Wasteful when idle; guaranteed bandwidth | Efficient sharing; possible congestion/delay |
  | No per-packet headers | Headers on every packet |
- **Client–server vs P2P**: dedicated servers vs peers acting as both client and server (torrents).

---

## 2. Layered Models: OSI & TCP/IP

**Why layering?** Divide-and-conquer: each layer offers services to the layer above using the layer below; protocols can evolve independently; interoperability across vendors.

### OSI — 7 layers (memorize function + protocol + device)
| # | Layer | Function | Protocols | Devices/PDUs |
|---|---|---|---|---|
| 7 | Application | Network services to apps | HTTP, FTP, SMTP, DNS, DHCP | Data |
| 6 | Presentation | Format, encryption, compression | TLS*, JPEG, ASCII | Data |
| 5 | Session | Dialog control, sync/checkpoints | NetBIOS, RPC | Data |
| 4 | Transport | End-to-end delivery, ports, reliability | TCP, UDP, QUIC | Segment (TCP) / Datagram (UDP) |
| 3 | Network | Logical addressing & routing | IP, ICMP, OSPF, BGP | Routers, Packet |
| 2 | Data Link | Framing, MAC addressing, error detection, hop-to-hop delivery | Ethernet, ARP, PPP, VLAN | Switches, bridges; Frame |
| 1 | Physical | Bits on the wire/air | Ethernet PHY, Wi-Fi radio, DSL | Hubs, repeaters, cables; Bits |

Mnemonic: **P**lease **D**o **N**ot **T**hrow **S**ausage **P**izza **A**way (bottom-up).

### TCP/IP model (what the Internet actually runs)
Application ↔ (OSI 5–7), Transport (4), Internet (3), Network Access/Link (1–2).
OSI is the teaching/reference model; TCP/IP is the implementation. TCP/IP merged session/presentation into applications.

**Encapsulation**: data → segment (+TCP hdr) → packet (+IP hdr) → frame (+Ethernet hdr/trailer) → bits. Each hop decapsulates to its layer and re-encapsulates (routers work at L3, switches at L2).

---

## 3. Data Link Layer

- **MAC address**: 48-bit hardware address (e.g., `AA:BB:CC:12:34:56`); first 24 bits = vendor OUI. Local (LAN) delivery.
- **Framing**: wrap payload with header/trailer; preamble + destination/source MAC + type + payload + **CRC** (error detection).
- **Error detection**: parity (1-bit), **checksum**, **CRC** (strong, hardware-friendly). Error correction: Hamming code.
- **Flow control (link level)**:
  - Stop-and-Wait: send 1 frame, wait for ACK — simple, wasteful on long links.
  - **Sliding Window**: send up to *window* frames without ACK.
    | Go-Back-N (GBN) | Selective Repeat (SR) |
    |---|---|
    | Receiver accepts only in-order; discards out-of-order | Receiver buffers out-of-order frames |
    | On error: retransmit frame + all after it | Retransmit only the missing frame |
    | 1 receiver buffer | W receiver buffers; ACKs individual frames |
- **Media access control**:
  - **CSMA/CD** (wired Ethernet): listen while transmitting; on collision, abort, send jam signal, backoff exponentially. (Largely obsolete — modern switches are full duplex.)
  - **CSMA/CA** (Wi-Fi): avoid collisions — wait for channel idle + random backoff, ACK each frame (collisions can't be detected on radio).
- **ARP (Address Resolution Protocol)**: IP → MAC within a LAN. Broadcast "who has 192.168.1.5?" → owner replies unicast with its MAC. Replies are cached (arp table). **Gratuitous ARP / ARP spoofing** are interview follow-ups.

---

## 4. Network Layer (IP)

### IPv4
- 32-bit address, dotted decimal (`192.168.1.1`); header: version, IHL, TTL (decrement per hop, prevents loops), protocol (6=TCP, 17=UDP), source/dest IP, checksum.
- **Classful (legacy)**: A /8, B /16, C /24 — wasteful → replaced by **CIDR**.

### CIDR & Subnetting
- `a.b.c.d/p` → p network bits, (32−p) host bits.
- **Block size = 2^(32−p)** addresses; usable hosts = block − 2 (network + broadcast).
- Example `/26`: 64 addresses, **62 usable hosts**, mask `255.255.255.192`.
- VLSM: different subnet sizes in one network (subnet the subnets).

| Private ranges (RFC 1918) | |
|---|---|
| 10.0.0.0/8 | 172.16.0.0/12 | 192.168.0.0/16 |

- Special: `127.0.0.1` loopback, `169.254.x.x` APIPA (DHCP failed), `255.255.255.255` broadcast.

### NAT (Network Address Translation)
- Router rewrites private source IP:port → its public IP:port, keeping a translation table; replies are mapped back.
- Solves IPv4 exhaustion; breaks true end-to-end reachability (why NAT traversal/P2P needs tricks; why "port forwarding" exists).

### DHCP — DORA (all UDP)
1. **Discover** (client broadcast) → 2. **Offer** (server proposes IP) → 3. **Request** (client asks for offered IP) → 4. **Acknowledge** (server confirms + lease, gateway, DNS). Ports 67 (server) / 68 (client).

### ICMP
Control & diagnostics: **ping** (echo request/reply), **traceroute** (TTL-expiry messages reveal each hop). No ports — carried directly in IP.

### Routing
| Distance Vector | Link State | Path Vector |
|---|---|---|
| Neighbor's table + hop counts (RIP) | Full topology map, Dijkstra (OSPF) | Full path policies (BGP) |
| Slow convergence, **count-to-infinity** (fix: split horizon, poison reverse) | Fast, more memory/CPU | Internet backbone |

### IPv6
128-bit, hex colon notation (`2001:db8::1`), no broadcast (multicast/anycast), no header checksum, built-in IPsec support, SLAAC auto-config. Migration: dual-stack, tunneling, NAT64.

---

## 5. Transport Layer (TCP & UDP)

- **Ports** identify applications (0–65535); well-known 0–1023. Socket = IP:port pair.

| | UDP | TCP |
|---|---|---|
| Connection | Connectionless | Connection-oriented (3-way handshake) |
| Reliability | None (fire-and-forget) | ACKs, retransmission, dedup |
| Ordering | None | Sequence numbers reorder |
| Flow/congestion control | None | Sliding window + congestion control |
| Header | 8 bytes | 20–60 bytes |
| Speed/overhead | Minimal | Higher |
| Use | DNS, DHCP, VoIP, video, gaming, QUIC base | Web, email, file transfer, SSH |

### TCP Connection Management
- **3-way handshake**: `SYN (seq=x)` → `SYN+ACK (seq=y, ack=x+1)` → `ACK (ack=y+1)`. Why three? Both sides must independently confirm *their* send *and* receive capability; two-way can't do that safely (and avoids duplicate old SYNs creating phantom connections).
- **4-way termination**: `FIN` → `ACK` → `FIN` → `ACK`. The active closer enters **TIME_WAIT** (2×MSL) to (a) re-ACK a lost final FIN, (b) let old duplicate segments die out.
- Half-close: one side can finish sending while still receiving (FIN/ACK asymmetry).

### TCP Reliability Machinery
- **Sequence numbers & cumulative ACKs**; retransmission on timeout or **3 duplicate ACKs → fast retransmit**.
- **Flow control**: receiver advertises window (rwnd) so sender never overflows the *receiver's buffer*.
- **Congestion control** (protects the *network*):
  1. **Slow start**: cwnd starts small, doubles per RTT (exponential) until **ssthresh**.
  2. **Congestion avoidance**: grow cwnd linearly (+1 MSS per RTT) — **AIMD**.
  3. On **3 dup ACKs** (Reno): fast retransmit + fast recovery (halve cwnd).
  4. On **timeout**: ssthresh = cwnd/2, cwnd resets to 1, slow start again.
- Flow control is about the receiver; congestion control is about the network. Both shape the send window = min(rwnd, cwnd).
- **QUIC** (HTTP/3): UDP-based, TLS 1.3 built-in, no head-of-line blocking between streams, faster handshake. Google-developed, IETF-standardized.

---

## 6. Application Layer

### DNS (UDP/TCP 53)
- Hierarchical: root `.` → TLD (`.com`) → authoritative (`example.com`) → records.
- **Recursive resolver** (your ISP / 8.8.8.8 / 1.1.1.1) does the legwork; caching at every level (browser → OS → resolver) with TTL.
- Records: **A** (name→IPv4), **AAAA** (IPv6), **CNAME** (alias), **MX** (mail), **NS** (nameserver), **TXT** (SPF/verification).
- Iterative vs recursive query: resolver asks each level "who knows .com?" (iterative referrals) on behalf of the client (recursive).

### HTTP
- Request: method, path, headers, body. Response: status, headers, body. Stateless → **cookies** add session state (`Set-Cookie`, `HttpOnly`, `Secure`, `SameSite`).
- Methods & properties: **GET** (safe, idempotent), **POST** (neither), **PUT** (idempotent replace), **DELETE** (idempotent), **PATCH** (partial, not guaranteed idempotent), HEAD, OPTIONS.
- Status codes: `2xx` OK, `3xx` redirect (301 permanent / 302 temp / 304 cache), `4xx` client error (400, 401 unauthenticated, 403 forbidden, 404), `5xx` server error (500, 502 bad gateway, 503 unavailable).
- Versions:
  | Ver | Key ideas |
  |---|---|
  | 1.0 | One request per TCP connection |
  | 1.1 | Persistent connections, pipelining (head-of-line blocking!), Host header, chunked |
  | 2 | Binary framing, multiplexing many streams over 1 connection, HPACK, server push (HOL at TCP level remains) |
  | 3 | Runs on **QUIC/UDP**, kills TCP-level HOL blocking, TLS 1.3 integrated |

### HTTPS = HTTP over TLS
TLS provides **encryption** (privacy), **integrity** (MAC/AEAD), **authentication** (server certificate chain signed by a CA).
Simplified handshake (TLS 1.2): ClientHello (versions, ciphers, random) → ServerHello + **certificate** → key exchange (ECDHE) → both derive symmetric session keys → encrypted application data. TLS 1.3: 1-RTT (0-RTT on resume), only forward-secret ciphers.
(If TLS terminates inside the presentation layer — that's why HTTPS is "HTTP + presentation-layer security".)

### Email
- **SMTP (25/587)** — push, sending & relaying between servers.
- **POP3 (110)** — download (usually delete) to one device.
- **IMAP (143/993)** — sync mail on server, multi-device (default today).

### Others
- **FTP (20/21)** legacy file transfer; **SFTP/SSH (22)** secure replacement. SSH vs Telnet: encrypted vs plaintext.
- **CDN**: geographically distributed edge caches serving static content close to users (reduces latency & origin load).
- **WebSockets**: full-duplex persistent channel after an HTTP upgrade — chat, live feeds.
- **Proxy vs VPN**: proxy typically app-layer relay (no system-wide encryption); VPN encrypts all traffic at IP layer (tunnel).

---

## 7. Network Devices

| Device | Layer | Job |
|---|---|---|
| Repeater/Hub | L1 | Regenerate/broadcast bits; one collision domain |
| Bridge/Switch | L2 | Learn MAC table, forward frames per port; each port its own collision domain |
| Router | L3 | Route packets between networks using IP; separates broadcast domains |
| Gateway | L4–7 (loosely) | Protocol translation between different systems |
| Load balancer | L4/L7 | Distribute traffic across servers (round-robin, least-connections, IP-hash) |
| Firewall | L3–7 | Filter traffic by rules (see security notes) |
| Access point | L2 | Bridge Wi-Fi clients onto wired LAN |

---

## 8. Network Security Basics

- **Firewall types**: packet-filtering (L3/L4 rules), **stateful** (tracks connections), application/WAF (L7 payload inspection), next-gen (NGFW).
- **IDS vs IPS**: detection (alert) vs prevention (block); signature vs anomaly based.
- **VPN**: encrypted tunnel over public network (IPsec, WireGuard, OpenVPN).
- **DMZ**: buffer subnet holding public-facing servers.
- Common attacks: **MITM** (fix: TLS), **ARP spoofing** (fix: dynamic ARP inspection), **DNS spoofing** (fix: DNSSEC/DoH), **DDoS** (fix: rate limiting, CDNs/scrubbing), port scanning (recon phase of attacks).

---

## 9. What Happens When You Type a URL

The single most-asked CN interview question. Tell it in layers:

1. **Browser** parses URL, checks its cache (HSTS list too).
2. **DNS resolution**: browser cache → OS cache → recursive resolver → root/TLD/authoritative (cached short-circuits most of this) → **IP address**.
3. **ARP** (if needed) to find the gateway's MAC on the LAN.
4. **TCP connection**: 3-way handshake with server:443 (SYN → SYN-ACK → ACK).
5. **TLS handshake**: certificate verified against CAs, keys exchanged, session keys derived.
6. **HTTP request**: `GET / HTTP/1.1` (or 2/3) with headers/cookies.
7. **Server side** (load balancer → web server → app → DB) returns response `200 OK` + HTML.
8. **Browser rendering**: parse HTML → fetch CSS/JS/images (repeat steps for each host) → DOM/CSSOM → render tree → paint.
9. Connection reuse (keep-alive) or close (4-way FIN).

---

## 10. Cheat Sheet

| Item | Value |
|---|---|
| FTP data/control | 20 / 21 |
| SSH / Telnet | 22 / 23 |
| SMTP | 25 (587 submission) |
| DNS | 53 |
| DHCP | 67 server / 68 client (UDP) |
| HTTP / HTTPS | 80 / 443 |
| POP3 / IMAP | 110 / 143 (993 TLS) |
| RDP | 3389 |
| Ping/traceroute | ICMP (+TTL trick) |
| TCP flags | SYN, ACK, FIN, RST, PSH, URG |
| Send window | min(receiver window, congestion window) |
| ARP | IP→MAC (broadcast ask, unicast reply) |
| DHCP | DORA over UDP |
| IPv4 vs IPv6 | 32-bit vs 128-bit |
| Count-to-infinity fix | Split horizon / poison reverse (RIP) |
| HTTP/3 transport | QUIC over UDP |
