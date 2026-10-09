# Computer Networks — Curated Learning Resources

> Books → Courses → YouTube → Websites → Tools & Practice. Starred items = best value.

## Books
| Resource | Why | Link |
|---|---|---|
| **Computer Networking: A Top-Down Approach** — Kurose & Ross | The standard text; starts from applications (how interviews are structured) | Companion site with interactive problems: https://gaia.cs.umass.edu/kurose_ross/ |
| **Computer Networking: A Systems Approach** — Peterson & Davie | **Free to read online**, solid second perspective | https://book.systemsapproach.org |
| **Computer Networks** — Tanenbaum & Wetherall | Classic bottom-up reference | Print / Pearson |
| **High Performance Browser Networking** — Ilya Grigorov | **Free online**; superb on HTTP/TLS/browsers — great for web-dev interviews | https://hpbn.co |
| **TCP/IP Illustrated Vol. 1** — W. Richard Stevens | Deep protocol internals (advanced) | Print / Addison-Wesley |

## Free Courses
| Course | Why | Link |
|---|---|---|
| **Stanford CS144: Introduction to Computer Networks** | Lectures + build-your-own-TCP labs | https://cs144.github.io |
| **Kurose & Ross companion resources** | Interactive problems & applets per chapter | https://gaia.cs.umass.edu/kurose_ross/ |

## YouTube
| Channel / Playlist | Best for | Link |
|---|---|---|
| **Gate Smashers — CN playlist** | Exam/interview-oriented concept videos, short | https://www.youtube.com/results?search_query=gate+smashers+computer+networks+playlist |
| **Neso Academy — CN** | Full theory from scratch | https://www.youtube.com/results?search_query=neso+academy+computer+networks |
| **PowerCert Animated Videos** | Visual animations of subnetting, NAT, switches, firewalls — ideal for intuition | https://www.youtube.com/@PowerCertAnimatedVideos |
| **Practical Networking** | "Networking fundamentals" series, real-world framing | https://www.youtube.com/@PracticalNetworking |
| **NetworkChuck** | Engaging hands-on networking + hacking basics | https://www.youtube.com/@NetworkChuck |
| **Computerphile** | Individual deep-dives (DNS, TLS, packet life) | https://www.youtube.com/@Computerphile |

## Websites & Docs
| Resource | Why | Link |
|---|---|---|
| **Cloudflare Learning Center** | Crisp free explainers on DNS, TLS, DDoS, HTTP/3 | https://www.cloudflare.com/learning/ |
| **GeeksforGeeks — CN Last Minute Notes** | Rapid revision before interviews | https://www.geeksforgeeks.org/computer-network-tutorials/ |
| **Julia Evans' blog** | Deep-dive zines/posts on DNS, TCP, TLS internals | https://jvns.ca |
| **MDN — HTTP docs** | Authoritative HTTP reference (methods, headers, status codes) | https://developer.mozilla.org/en-US/docs/Web/HTTP |

## Tools & Hands-on
| Tool | What to try |
|---|---|
| **Wireshark** | Capture a real 3-way handshake, TLS handshake, DNS query — see everything you studied |
| `dig` / `nslookup` | Trace DNS records for any site (`dig example.com ANY`) |
| `ping`, `traceroute`/`tracert` | Watch TTL hops to a far-away server |
| `curl -v https://example.com` | See the TLS + HTTP exchange in the terminal |
| `netstat` / `ss` | Live TCP states (SYN_SENT, TIME_WAIT, ESTABLISHED) |
| **Subnetting practice** | Drill until instant: https://subnettingpractice.com |

## Suggested path
1. **First pass:** PowerCert (intuition) + Gate Smashers/Neso (syllabus coverage) alongside the notes in this repo.
2. **Second pass:** Kurose & Ross chapters 1–5 + 8; do the interactive problems.
3. **Interview polish:** GfG last-minute notes, this repo's Q&A file, one full "type a URL" narration, 5 subnetting sums.
4. **Bonus credibility:** one Wireshark capture of a real handshake you can describe from memory.
