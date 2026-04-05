# Cloud-Native Secure VPN Gateway & DNS Sinkhole

A comprehensive, privacy-first VPN Gateway built on AWS EC2. This project integrates **WireGuard**, **AdGuard Home**, and **Unbound** to provide an encrypted tunnel, enterprise-grade ad/malware blocking, and a self-hosted recursive DNS resolver to completely eliminate third-party DNS tracking.

**Author:** Pham Thanh Lam | Network Security Student @ University of Information Technology (UIT)

---

## 🏗️ System Architecture & Packet Flow

The system is designed with a strict, closed-loop packet flow to ensure zero DNS leaks and maximum privacy:

`Client Device` ➔ `[Encrypted Tunnel]` ➔ `WireGuard (AWS EC2)` ➔ `AdGuard Home (Port 53)` ➔ `Unbound (Port 5335)` ➔ `Root Servers`

1. **WireGuard:** Establishes a lightweight, state-of-the-art encrypted VPN tunnel from the client to the AWS cloud.
2. **AdGuard Home:** Acts as a DNS Sinkhole. It intercepts all DNS queries from the VPN interface (`10.66.66.1`), blocking telemetry, ads, and malicious domains using custom rule lists (e.g., OISD, ABPVN).
3. **Unbound:** A validating, recursive, and caching DNS resolver. Instead of forwarding clean queries to public DNS providers (like Google or Cloudflare), Unbound queries the ICANN Root Servers directly, ensuring complete privacy.

---

## ⚙️ Key Features & Configurations

* **Cloud Infrastructure (FinOps Optimized):** Deployed on an AWS `t3.micro` instance. Managed Elastic IPs and Security Groups to ensure the system operates entirely within the AWS Free Tier.
* **Port Conflict Resolution:** Overcame the default Ubuntu `systemd-resolved` port 53 conflict by safely modifying `resolved.conf.d` and re-linking the `resolv.conf` stub, allowing AdGuard Home to act as the primary local resolver.
* **Optimized Blocklists:** Configured with ~240,000 rules specifically chosen to balance maximum tracking prevention with the 1GB RAM limitation of the micro-instance to prevent Out-Of-Memory (OOM) crashes.
* **Enforced Safe Search:** Implemented strict DNS-level parental controls and SafeSearch enforcement for search engines.

---

## 📊 Dashboard & Performance

*AdGuard Home effectively filtering traffic originating from the WireGuard tunnel.*

![AdGuard Dashboard](dashboard.png)

---

## 🚀 Deployment Summary

* **OS:** Ubuntu 24.04 LTS (AWS EC2)
* **VPN:** WireGuard
* **DNS Filtering:** AdGuard Home (Listening on `10.66.66.1:53` and `127.0.0.1:53`)
* **Recursive DNS:** Unbound (Listening on `127.0.0.1:5335` with DNSSEC enforced)

*Note: This repository serves as documentation for the infrastructure deployed. For security reasons, actual configuration files containing private keys and Elastic IPs are not published.*
