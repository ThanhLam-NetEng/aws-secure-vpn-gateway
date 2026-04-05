# 🛠️ Deployment Guide

This guide outlines the high-level steps to reproduce the Secure VPN Gateway infrastructure on an Ubuntu 24.04 LTS instance.

### Phase 1: OS Preparation & Port Conflict Resolution
Ubuntu's default `systemd-resolved` occupies port 53. It must be bypassed before installing AdGuard.
1. Create override directory: `sudo mkdir -p /etc/systemd/resolved.conf.d`
2. Create `adguardhome.conf` and set `DNSStubListener=no`.
3. Relink `/etc/resolv.conf` to bypass the stub.
4. Restart service: `sudo systemctl reload-or-restart systemd-resolved`.

### Phase 2: Core Components Installation
1. **WireGuard:** Install via `apt install wireguard`. Generate public/private key pairs using `wg genkey`. Configure `wg0.conf` for NAT routing (MASQUERADE).
2. **AdGuard Home:** Download the official release binary, unpack, and run `sudo ./AdGuardHome -s install`. Complete the web setup on port 3000, setting it to listen on `10.66.66.1:53`.
3. **Unbound:** Install via `apt install unbound`. Configure `unbound.conf` to listen on `127.0.0.1:5335` and enable DNSSEC validation.

### Phase 3: Integration & Blocklists
1. **VPN DNS Enforcement:** Update WireGuard client configurations to point `DNS = 10.66.66.1`.
2. **Upstream Routing:** In AdGuard Home's DNS settings, remove all default upstreams and add `127.0.0.1:5335` (Unbound).
3. **Optimization:** Add the `OISD Blocklist Small` and `ABPVN` lists to AdGuard Home. Avoid lists exceeding 500,000 rules to prevent OOM errors on the 1GB RAM instance.

*Note: For detailed configurations, please refer to the templates in the `/configs` directory.*
