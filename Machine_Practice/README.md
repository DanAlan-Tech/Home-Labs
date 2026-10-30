# Secure Multi-Tenant Educational Lab Infrastructure & Public Web Portal

An advanced, scalable enterprise-grade lab architecture built to provide secure, browser-accessible learning environments for groups of technical learners. Extending an isolated virtualization foundation, this phase introduces **zero-trust public edge ingestion, identity-gated application publishing, isolated multi-zone DMZ containment, and automated infrastructure template workflows**. 

## Project Motivation & Learned Skills
The core objective of this project was to transition a standalone private laboratory environment into a secure, multi-tenant learning platform. Designing and deploying this architecture provided deep, hands-on engineering experience in:
* **Zero Trust Remote Access Architecture:** Setting up secure edge tunnels to securely publish applications to the public internet without opening a single inbound firewall port.
* **Identity & Access Management (IAM):** Integrating cloud-based Identity Providers (IdP) and configuring contextual access control mechanisms to guard back-end assets.
* **Network Segmentation & DMZ Engineering:** Structuring advanced, multi-interface firewall logic across newly decoupled segments (Production DMZ vs. Student Lab Zones).
* **Multi-Tenant Orchestration:** Designing baseline operating system golden templates and optimizing resource utilization metrics via linked-cloning methods.
* **Security Operations & Controls:** Writing functional verification check scripts, managing strict resource quotas, and planning critical business safeguards for early-stage pilot teams.

---

## Enhanced Architecture Diagram

The infrastructure segregates administrative boundaries, internet-facing web proxies, and student-accessible computing cells across isolated software-defined virtual network rings:

```
[ Public Web Visitors ]                [ Authenticated Students ]
           │                                        │
           ▼                                        ▼
    [ Public Domain ]                        [ Public Domain ]
           │                                        │
           └───────────────────┬────────────────────┘
                               │ (Cloudflare Edge Protection)
                               ▼
                    [ Identity-Gated Policy ]
                               │ (Allows Listed Emails via One-Time PIN)
                               ▼
                     [ Outbound Tunnel ]
                               │ (Cryptographic Ingress Connection)
  =============================│=====================================
  [ PROXMOX VE HYPERVISOR ]    │ (Runs locally)
                               ▼
                ┌──────────────────────────────┐
                │          pfSense VM          │
                │  • Handles Inter-Zone Routing│
                └──────────────┬───────────────┘
                               │
       ┌───────────────────────┴───────────────────────┐
       ▼ (DMZ Network Zone)                            ▼ (Student Network Zone)
 ┌───────────┐                                   ┌───────────┐
 │  edge01   │ ── [ Apache Guacamole ] ────────> │ Student1  │
 │ (Reverse  │     (Converts SSH/RDP to Web)     │ (VM Linux)│
 │   Proxy)  │                                   ├───────────┤
 └───────────┘                                   │ Student2  │
                                                 │ (VM Win)  │
                                                 └───────────┘
```

---

## Technical Component Execution Breakdown

### Phase 1: DMZ Isolation & Multi-Interface Perimeter Routing
* **Software-Defined Switching:** Built independent virtual software-defined network bridges on the underlying bare-metal hypervisor to map out distinct broadcast domains.
* **Dynamic Boundary Allocation:** Scaled the primary core security gateway VM by provisioning supplemental virtual interface adapters mapped exclusively to the isolated networks.
* **Multi-Segment DHCP Topology:** Concocted isolated DHCP addressing scopes for each network segment, reserving static operational boundaries for structural host components.

### Phase 2: Hierarchical Policy Firewalls
To ensure maximum containment, traffic was restricted dynamically across all virtual boundaries using strict, top-to-bottom processing rule logic:
* **Administrative Zone:** Enabled explicit remote administrative access (SSH/RDP/VNC) exclusively flowing *from* the private lab *into* the public hosting layers.
* **DMZ Containment:** Permitted inbound connections to traverse the edge gateway only to proxy interactive protocols into the student segment. All default outbound routes attempting to reach private core address spaces (RFC 1918) are hard-blocked.
* **Student Lab Zone Restrictions:** Locked the student runtime network down to strict web-browsing ports and internal gateway name resolution queries. The default configuration explicitly drops, logs, and denies any cross-zone connection requests.

### Phase 3: Zero-Trust Public Edge Ingress (The Proxy Layer)
* **Microservices Orchestration:** Provisioned an immutable web gateway stack (`edge01`) utilizing containerized runtimes managed via environment configuration files.
* **Cryptographic Tunneling:** Integrated an endpoint connector running as an automated runtime container. This creates a secure outbound mesh connection to the public content network, dropping the need for risky inbound port-forwarding rules on the physical perimeter router.
* **Identity Injection Gateway:** Configured a validation wall requiring One-Time PIN (OTP) matching over an encrypted browser connection. Only pre-vetted, listed user identities are issued permission codes to interact with back-end login pages.

### Phase 4: Scalable Multi-Tenant Virtualization Workflows
* **Golden Base Engineering:** Formed optimized, high-performance base templates for both Debian and Red Hat enterprise ecosystem variants. Integrated native virtualization runtime agents and cleared standard systemic network identities to ensure unique platform initializations upon provisioning.
* **Linked Cloning Workflows:** Built high-efficiency, multi-tenant learning cells using rapid hypervisor linked-cloning methods. This process shares structural base disks to conserve disk space, keeping storage requirements minimal even across dozen-user arrays.
* **Gateway Proxy Mapping:** Configured an interactive clientless gateway to transform raw target console connections directly into browser-accessible web interfaces. Saved credentials within strict, individual user mapping sheets so student groups only interact with their designated machines.

---

## Exhaustive Structural Index of Tools Used

### Infrastructure & Operations
* **Proxmox VE:** Server virtualization management engine used to run corporate firewalls, isolated network switches, production proxies, and distinct user templates.
* **pfSense:** Virtual router used to parse networks, provide dynamic network configurations, and write access management rule lists between different lab groups.
* **Docker / Docker Compose:** Multi-container management platform used to cleanly instantiate database, gateway, reverse proxy, and zero-trust connector nodes.

### Security, Zero Trust & Edge Gateway Ingestion
* **Cloudflare Tunnels (`cloudflared`):** A lightweight agent that establishes a secure outbound connection from local networks to the public edge, obfuscating local source infrastructure IPs.
* **Cloudflare Zero Trust Access:** Access policy firewall that provides identity verification layers, validation checks, and usage audit logs before hitting web properties.
* **Apache Guacamole:** An open-source clientless remote desktop gateway. It encapsulates traditional administrative network protocols (SSH, RDP, VNC) into standard browser web sessions via HTML5.
* **Nginx Reverse Proxy:** High-performance web server acting as a frontend proxy to present public resources safely while directing back-end traffic paths appropriately.
* **PostgreSQL:** An enterprise-grade object-relational database architecture utilized as the backend data store to maintain users, connections, and authorization maps.

### Configuration & Automation
* **Bash / Shell Scripting:** Used to write lightweight, conditional test suites built right into image baselines to evaluate student progress and verify system settings instantly.
* **QEMU Guest Agent:** An interface daemon providing low-level configuration handshakes between virtual kernels and hypervisor management dashboards.

---

## Administrative Ground Rules & Safety Safeguards
The platform enforces a multi-layered model to maintain structural health and baseline integrity:
* **The Master Kill Switch:** Outside internet connectivity to internal resources can be severed instantaneously from the proxy terminal by execution of a quick service termination command:
  ```bash
  docker compose stop cloudflared
  ```
* **Strict Session Expiry:** System access windows are locked to temporary lengths (e.g., 8 hours) to prevent unauthorized, long-term stale credential reuse.
* **Hypervisor Resource Limits:** Explicit execution constraints are applied directly to CPU and memory profiles within guest hardware settings to prevent runaway user applications from starving adjacent processing structures.
