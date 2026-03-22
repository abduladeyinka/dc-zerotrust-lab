# Detection Report — DC Zero Trust Lab

## Lab Environment
- **Firewall:** pfSense 2.8.1-RELEASE
- **IDS:** Suricata (ET Open + Snort GPLv2 rules)
- **Date:** March 22, 2026
- **Analyst:** Abdulakeem Alabi

---

## Detection Event #1 — Nmap Port Scan

| Field | Value |
|-------|-------|
| Date | 03/22/2026 12:21 PDT |
| Attack Type | Port Scan / Reconnaissance |
| Tool Used | Nmap 7.80 |
| Source IP | 10.10.1.10 (Ubuntu MGMT) |
| Target IP | 10.10.1.1 (pfSense gateway) |
| Protocol | TCP |
| Signature | ET SCAN Possible Nmap User-Agent Observed |
| GID:SID | 1:2024364 |
| Priority | 1 — Critical |
| Class | Web Application Attack |
| Result | DETECTED ✅ |

### Attack Command Used
```
sudo nmap -sS -A --script=default 10.10.1.1
```

### Ports Discovered by Attacker
- 53/tcp — DNS
- 80/tcp — HTTP (nginx)
- 443/tcp — HTTPS (nginx)

### Suricata Response
Suricata fired multiple Priority 1 alerts identifying the scan
tool by its HTTP user-agent signature. The IDS correctly
classified the activity as a Web Application Attack.

### Zero Trust Result
Despite the scan succeeding against pfSense, firewall rules
prevented any lateral movement to PROD or DMZ zones.
The attacker was contained to the MGMT zone only.

---

## Firewall Rule Effectiveness

| Attack Vector | Blocked? | Notes |
|---------------|----------|-------|
| MGMT → PROD | ✅ YES | Default deny rule fired |
| MGMT → DMZ | ✅ YES | Default deny rule fired |
| PROD → MGMT | ✅ YES | Default deny rule fired |
| DMZ → PROD | ✅ YES | Default deny rule fired |
| Any → Internet | ✅ ALLOWED | Expected behavior |

---

## Lessons Learned

1. Suricata correctly identifies Nmap by its user-agent signature
even without deep packet inspection.
2. Zero trust rules successfully contain reconnaissance to a
single zone — no lateral movement possible.
3. Default-deny policies are effective even when an attacker has
foothold in one zone.

---

## Next Steps
- Add Kali Linux in DMZ to simulate external attacker
- Test lateral movement from DMZ to PROD
- Enable Suricata IPS mode for active blocking
- Document evasion techniques and detection gaps
