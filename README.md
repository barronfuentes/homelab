# Project: Secure Home Network & High-Performance Plex DMZ

## Objective
To migrate home services from a Raspberry Pi to a Windows-based server architecture, establish an isolated DMZ for secure remote Plex access for family, and implement a hardened IoT VLAN to isolate untrusted smart devices.

---

## 1. Target State Network Topology



| Network Segment | Description | Purpose |
| :--- | :--- | :--- |
| **LAN (VLAN 1)** | Trusted devices (Windows PC, NAS, Mobile) | Core storage and computing |
| **DMZ (VLAN 20)** | Raspberry Pi 5 (Nginx Proxy, CrowdSec) | Public-facing gateway for Plex |
| **IoT (VLAN 30)** | Smart Bulbs, Plugs, Cameras, TV | Untrusted "chatterbox" devices |

---

## 2. High-Level Project Roadmap

### Phase 1: Homebridge Migration & IoT Isolation
- [ ] **Backup:** Export Homebridge `.tar.gz` from Raspberry Pi 5.
- [ ] **Deploy:** Install Node.js and Homebridge on Windows PC (`10.67.0.60`).
- [ ] **Restore:** Verify HomeKit pairing and plugin functionality on Windows.
- [ ] **VLAN Setup:** Create "IoT" Network in UniFi (VLAN 30) with a dedicated SSID.
- [ ] **Firewall (IoT):** - [ ] Block IoT VLAN from accessing Gateway Management (Port 80/443).
    - [ ] Block IoT VLAN from accessing LAN/DMZ (Default Drop).
    - [ ] Allow Windows PC (LAN) to IoT VLAN (Established/Related).

### Phase 2: Raspberry Pi 5 Infrastructure (Alpine Linux)
- [ ] **OS:** Flash Alpine Linux 64-bit to SD card.
- [ ] **Storage:** Format 32GB USB drive (ext4) and mount to `/var/lib/docker`.
- [ ] **Hardening:**
    - [ ] Create non-root admin user.
    - [ ] Deploy Ed25519 SSH keys; disable password authentication.
    - [ ] Change SSH port to custom high-range port.
    - [ ] Configure `awall` host firewall for "Default Deny."

### Phase 3: Docker Security Stack Deployment
- [ ] **Engine:** Install Docker and Docker Compose on Alpine.
- [ ] **Services:** Deploy Nginx Proxy Manager, CrowdSec (WAF), and Watchtower.
- [ ] **SSL:** Configure DNS-01 challenge via DuckDNS API for Let's Encrypt automation.
- [ ] **Persistence:** Ensure all config/SSL data is mapped to the USB drive volumes.

### Phase 4: UniFi DMZ Integration
- [ ] **VLAN Setup:** Create "DMZ" Network in UniFi (VLAN 20); assign Pi 5 port.
- [ ] **Firewall (DMZ):**
    - [ ] Allow Pi 5 (DMZ) to Windows PC (LAN) on Port 32400 only.
    - [ ] Block DMZ from accessing all other LAN/IoT resources.
- [ ] **Port Forward:** Update UDM-SE to forward Port 443 (WAN) to Pi 5 (DMZ).

### Phase 5: Final Plex Handshake & Validation
- [ ] **NPM Config:** Create Proxy Host (HTTPS) for `yourname.duckdns.org` -> `10.67.0.60:32400`.
- [ ] **Plex settings:** Update "Custom server access URL" to your domain.
- [ ] **Testing:** Verify "Direct Connection" status on 5G/Mobile data (No Relay).
- [ ] **Automation:** Finalize `unattended-upgrades` (OS) and Watchtower (Containers) schedules.

---

## 3. Maintenance & Automation Strategy
* **Daily (03:00 AM):** OS Security Patching & Auto-Reboot (if required).
* **Daily (04:00 AM):** Watchtower container image audit and updates.
* **On-Demand:** CrowdSec Global Blocklist updates via hub-upgrade.