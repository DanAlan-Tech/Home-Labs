# Isolated Cybersecurity Home Lab Architecture

A comprehensive, multi-tiered home lab environment designed for secure penetration testing, vulnerable target practice, and secure remote management. The core objective of this project was to **gain hands-on personal experience in enterprise-grade network isolation, virtualization management, firewall configuration, and secure VPN deployment**.

## Project Motivation & Learned Skills
Building this lab from scratch provided deep, practical experience across several critical IT and cybersecurity domains:
* **Hypervisor Management:** Configuring type-1 hypervisors, updating free repositories, managing storage pools, and orchestrating virtual machines.
* **Network Segregation:** Implementing strict network isolation using unmapped virtual bridges to ensure vulnerable CTF targets can never impact production systems.
* **Firewall Engineering:** Writing and structuring sequential firewall rules (DNS allowances, RFC 1918 private network blocking, and allow-any outbound rules) via a dedicated virtual appliance.
* **Secure Remote Operations:** Setting up encrypted external entry points using a peer-to-peer VPN tunnel, automated dynamic DNS scripting, and public-facing port forwarding configurations.

---

## Network Architecture & Design

The environment relies on strict physical and virtual segregation to separate daily household internet use from the vulnerable lab segment.

```
       [ Public Internet ]
                │
         [ Home Gateway ] ── (Home LAN Segment)
                │
        ┌───────┴────────────────────────┐
        │       Proxmox VE Host          │ ── [ WireGuard Server (Pi) ]
        │      (Management Node)         │     (Secure Remote Entry Point)
        │                                │
        │   ┌────────────────────────┐   │
        │   │     pfSense VM         │   │
        │   │  WAN: Maps to Home LAN │   │
        │   │  LAN: Maps to Lab Net  │   │
        │   └───────────┬────────────┘   │
        └───────────────┼────────────────┘
                        │
             [ Isolated Lab Network ] (Virtual Software Bridge)
                        │
         ┌──────────────┴──────────────┐
         │                             │
  [ Kali Linux ]              [ Target Machines ]
(Attacker Instance)       (Debian/DVWA, Metasploitable 2)
```

---

## Core Components & Deployment Phases

### Phase 1: Bare-Metal Hypervisor Configuration (Proxmox VE)
* **Hardware Preparation:** Enabled hardware virtualization (SVM Mode) inside the AMD Ryzen platform BIOS to allow high-performance VM execution.
* **Operating System Installation:** Provisioned Proxmox VE utilizing an automated graphical installer configured to an isolated fully qualified domain name.
* **Package Management Optimization:** Switched system infrastructure from corporate update streams over to the community-supported `No-Subscription` repository to maintain reliable patch updates without enterprise licensing.

### Phase 2: Secure Ingress Tunneling (WireGuard & DDNS)
* **Server Setup:** Deployed a streamlined headless Linux OS on dedicated hardware acting as an external network gateway.
* **Asymmetric Key Exchange:** Generated standalone public/private key matrices for both the ingress server and authenticated mobile endpoint profiles.
* **Automation:** Developed a custom Bash synchronization engine configured alongside system timers (`cron`) to update a dynamic DNS provider with variable public WAN addresses automatically.
* **Network Hardening:** Opened only a single cryptographic endpoint port to minimize the public attack surface.

### Phase 3: Defensive Boundary Implementation (pfSense)
* **Virtual Networking Topology:** Created an unmapped, standalone virtual software bridge (`vmbr1`) intentionally stripped of any physical interface links to define the boundaries of the isolated target network.
* **Interface Allocation:** Provisioned a pfSense security instance mapping virtual adapter `vtnet0` to the routing layer (`vmbr0`) and adapter `vtnet1` explicitly to the isolated target tier (`vmbr1`).
* **Sequential Rule Logic:** Designed hierarchical processing constraints within the packet inspection engine to prevent lab traffic from crossing into private home network boundaries:
  1. **Pass:** Traffic targeted directly to the local security gateway for required local name resolution (DNS).
  2. **Block:** All network flows traversing the target segment addressed to structural network aliases defining RFC 1918 private allocations.
  3. **Pass:** Remaining outbound requests to accommodate secure, verified external updates.

### Phase 4: Attack Platform & Vulnerability Deployments
* **Penetration Testing Node:** Staged a specialized Kali Linux virtual instance connected solely to the isolated virtual bridge. Validated network segregation metrics via strict host-to-host ICMP testing sequences.
* **Containerized Targets:** Provisioned minimalist Debian server matrices running automated Docker runtimes hosting intentionally vulnerable configurations.
* **Legacy Vulnerability Playgrounds:** Imported standalone virtual disk formatting maps (`.vmdk`) into local LVM hypervisor storage volumes to deploy foundational penetration testing platforms like Metasploitable 2.

---

## Explanatory Index of Tools Used

This project utilized industry-standard tools to build, defend, and test the network architecture:

### Infrastructure & Virtualization
* **Proxmox VE:** A complete open-source platform for enterprise virtualization. It acts as the Type-1 hypervisor running directly on the bare-metal hardware to manage VMs and containers with maximum efficiency.
* **pfSense:** An open-source firewall and routing platform. It was deployed as a dedicated virtual machine to act as a security gateway, handling DHCP for the lab network and enforcing network isolation via rule sets.
* **Raspberry Pi Imager:** A utility tool used on the management laptop to burn custom operating system images and setup parameters onto physical microSD cards and flash drives.

### Networking & Security
* **WireGuard:** A modern, fast, and lightweight VPN protocol that utilizes state-of-the-art cryptography. It provides a highly secure tunnel into the home network from public environments without complex overhead.
* **DuckDNS:** A free Dynamic DNS service. It maps a changing public home IP address to a static domain name, ensuring remote VPN connectivity remains active even when the internet service provider rotates public IPs.
* **iptables:** A built-in Linux command-line utility for configuring packet filtering rules. Used on the VPN gateway to manage Network Address Translation (NAT) and masquerade traffic traveling through the secure tunnel.

### Operating Systems & Runtimes
* **Kali Linux:** A specialized, Linux-based operating system pre-packaged with advanced security, digital forensics, and penetration testing tools. It serves as the primary offensive workhorse in the lab.
* **Docker:** A containerization platform used to bundle applications and their dependencies into lightweight packages. Used to cleanly deploy and run web application testing environments instantly.
* **QEMU Guest Agent:** A helper daemon installed inside the virtual machines. It exchanges information seamlessly between the Proxmox hypervisor and the guest operating systems for clean shutdowns and monitoring.

### Target Applications & Testing Tools
* **DVWA (Damn Vulnerable Web Application):** A PHP/MySQL web application that is unhardened on purpose. It serves as a safe environment for web developers and security professionals to practice vulnerability discovery (like SQL Injection and XSS).
* **Metasploitable 2:** An intentionally broken Linux virtual machine image designed to practice network-level exploitation, vulnerability scanning, and configuration auditing.
* **Nmap:** A powerful open-source network scanner used for network discovery and vulnerability scanning. Used within the lab to discover active hosts and audit listening services on target machines.

---

## Lab Ground Rules & Operations Checklist
To maintain operational integrity and strict boundaries, the lab follows an immutable maintenance protocol:
* **Fresh Deployments:** Roll back target architectures to pre-attack baseline states (`clean-install` snapshots) via the virtualization control panel before beginning any testing session.
* **Zero Exposure Policy:** Never establish port forwarding routes leading from the public internet directly to target infrastructure zones. 
* **Single Path Access:** Keep management interfaces entirely off the public web; secure access is routed exclusively through cryptographic VPN configurations.
