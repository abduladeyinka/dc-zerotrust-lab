# Data Center Network Segmentation & Zero Trust Lab

![Security Lab](https://img.shields.io/badge/Type-Homelab-blue) ![pfSense](https://img.shields.io/badge/Firewall-pfSense_2.8.1-orange) ![Suricata](https://img.shields.io/badge/IDS-Suricata-red) ![Status](https://img.shields.io/badge/Status-Active-green)

## Overview

A hands-on data center security lab simulating enterprise-grade network segmentation, zero trust architecture, and intrusion detection. Built entirely in VirtualBox on a Windows host.

This lab mirrors the physical infrastructure security architecture used in real data centers at companies like Equinix, AWS, and Google.

---

## Architecture
```
┌─────────────────────────────────────────────┐
│              INTERNET / WAN                  │
│              10.0.0.128/24                   │
└──────────────────┬──────────────────────────┘
                   │
          ┌────────▼────────┐
          │    pfSense      │
          │  2.8.1-RELEASE  │
          │  (4 interfaces) │
          └──┬──────┬───────┘
        em2  │      │  em3
             │      │
   ┌─────────▼──┐  ┌▼──────────┐
   │  MGMT      │  │  PROD     │
   │ 10.10.1.x  │  │ 10.10.2.x │
   │            │  │           │
   │ Ubuntu     │  │ (reserved)│
   │ 10.10.1.10 │  │           │
   └────────────┘  └───────────┘
```

---

## Components

| Component | Version | Role |
|-----------|---------|------|
| pfSense | 2.8.1-RELEASE | Core firewall, router, IDS host |
| Suricata | Latest | Intrusion Detection System |
| Ubuntu Server | 24.04 LTS | MGMT zone server |
| VirtualBox | 7.x | Hypervisor |

## Network Zones

| Zone | Subnet | Purpose |
|------|--------|---------|
| MGMT | 10.10.1.0/24 | Administration and monitoring |
| PROD | 10.10.2.0/24 | Production servers |
| DMZ  | 10.10.3.0/24 | Public-facing services |

---

## Zero Trust Firewall Policy

| Source | Destination | Action |
|--------|-------------|--------|
| Any zone | Internet | ALLOW |
| MGMT | PROD | BLOCK |
| MGMT | DMZ | BLOCK |
| PROD | MGMT | BLOCK |
| PROD | DMZ | BLOCK |
| DMZ | MGMT | BLOCK |
| DMZ | PROD | BLOCK |

Default-deny between all zones. No lateral movement possible.

---

## Suricata IDS Configuration

- **Mode:** IDS (Detection only)
- **Interface:** MGMT (em2)
- **Rulesets:** ET Open Emerging Threats + Snort GPLv2 Community
- **Signatures:** 30,000+
- **Active categories:** emerging-scan, emerging-exploit, emerging-malware, emerging-dos

---

## Detection Events

### Event 1 — Nmap Port Scan Detection
- **Date:** March 22, 2026
- **Attack:** Nmap -sS -A --script=default scan
- **Source:** 10.10.1.10 (Ubuntu MGMT)
- **Target:** 10.10.1.1 (pfSense gateway)
- **Signature:** ET SCAN Possible Nmap User-Agent Observed
- **Priority:** 1 (Critical)
- **Class:** Web Application Attack
- **Result:** DETECTED ✅

---

## Skills Demonstrated

- Network segmentation and 3-tier DC architecture
- pfSense firewall administration and rule management
- Zero trust security implementation
- Suricata IDS deployment and tuning
- Ubuntu Server administration and static IP configuration
- SSH remote management
- VirtualBox network configuration (Bridged, Host-Only, Internal)
- Attack simulation and detection verification

---

## Repository Structure
```
dc-zerotrust-lab/
├── README.md
├── screenshots/        ← Suricata alerts, pfSense GUI, topology
├── configs/            ← pfSense exported config
└── findings/           ← Detection reports
```

---

## Author

**Abdulakeem Alabi**
- GitHub: [@abduladeyinka](https://github.com/abduladeyinka)
- Email: abdul.alabi004@gmail.com
