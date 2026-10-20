# 🛠️ Personal Homelab & Automation Hub

This repository contains the infrastructure configuration, deployment manifests, and documentation for my VPS running on **Ubuntu 24.04 LTS**. 

The architecture is highly optimized to run a multi-user VPN gateway and continuous game-reward farming automation simultaneously. The entire stack is carefully tuned to maintain high performance and rock-solid stability within a resource-constrained hardware environment.

---

### 🖥️ Server Specifications (Hardware & OS)

The project operates on a single cloud VPS instance. The resource allocation demonstrates efficient memory management and proactive monitoring under active real-world load.

| Component | Specification | Operational Status / Real-Time Load |
| :--- | :--- | :--- |
| **OS** | Ubuntu 24.04 LTS | Active 🟢 |
| **CPU** | 1 vCPU (1 Core / 1 Thread @ 2.39 GHz) | ~7.2% average utilization |
| **RAM** | 1 GB RAM (961.47 MB detected) | ~80.9% load (Highly optimized footprint) |
| **Swap Space**| 2 GB Configured (1.91 GB) | ~22.4% active usage (OOM protection enabled) |
| **Storage** | 15 GB SSD (14.66 GB formatted) | ~82.3% capacity used (~2.59 GB free space) |
| **Bandwidth** | 3 TB monthly limit | ~0.81 TB consumed (Active tracking per inbound) |
| **Efficiency**| Budget VPS (~392 RUB/mo) | Maximized ROI for automated tasks |

---

### 🌐 Networking & Privacy (Bare-Metal Deployment)

To completely eliminate container virtualization overhead, reduce latency, and ensure direct kernel-level packet routing, the core networking stack is deployed **directly on the host OS** rather than inside Docker.

*   **Native 3x-ui (Xray v26.10.10):** Implemented as a high-performance bare-metal service. Direct port binding on Ubuntu guarantees maximum throughout and zero network encapsulation overhead.
*   **Capacity & Traffic Routing:** The node securely tunnels traffic for **20 configured clients** (supporting up to 6 concurrent active sessions).
*   **Cryptographic Protocols Used:**
    *   `VLESS + Reality (TCP, Port 443)`: Primary high-speed stealth inbound mimicking standard HTTPS traffic to bypass deep packet inspection (DPI).
    *   `XHTTP + Reality (Port 8443)`: Next-generation transport layer designed for stable performance behind highly restrictive firewalls.
    *   `gRPC + Reality (Port 53000)`: Multiplexed, low-overhead streaming tunnel for persistent connections.
    *   *Pre-staged fallbacks (Disabled/Standby)*: Configured inbounds for `Shadowsocks (TCP/UDP)`, `TUIC (UDP)`, and `AmnesiaWG (UDP)` are embedded in the host system for immediate redundancy.

---

### 🤖 Automation & Farming (Docker Infrastructure)

Application services are decoupled and isolated inside Docker. By leveraging strict headless (text-only/CLI) configurations, the automation tools consume minimal memory overhead, preventing system thrashing.

#### 1. 🚂 ArchiSteamFarm (ASF)
*   **Purpose:** 24/7 automated Steam trading card farming and account management.
*   **Integration:** Configured with the IPC interface (Web UI) exposed on a secure local port. This allows dynamic configuration updates and hot-reloading of account modules without restarting the core Docker container.

#### 2. 💜 Twitch Drops Farms (2 Independent Instances)
*   **Purpose:** Simultaneous automated Drops farming for two distinct Twitch accounts.
*   **Optimization:** Configured to run strictly in low-memory headless environments (no browser rendering or virtual displays), which is critical for staying within the 1 GB physical RAM ceiling.
*   **Resource Ceilings:** Deployed with explicit memory caps (`250M` max per container) via docker-compose constraints to ensure network services always retain RAM priority.

---

### 📂 Repository Architecture

Because the VPN architecture runs natively on the host system via automated install scripts, this repository specifically tracks the Docker orchestration configs and defensive layout templates:

```text
├── .gitignore               # Strict exclusion matrix for tokens and sessions
├── README.md                # Main architecture documentation (this file)
└── docker/
    ├── .env.example         # Template for environment credentials
    └── docker-compose.yml   # Unified multi-service deployment manifest
```

---

### ⚠️ Security & Privacy Compliance

To adhere to industry-standard security practices and protect the infrastructure from external vectors, the following hardening guidelines and privacy controls have been strictly implemented:

1.  **Server Hardening (SSH Security):**
    *   **Root Login Disabled:** Direct `root` authentication via SSH is completely deactivated (`PermitRootLogin no`) to mitigate automated brute-force scripts.
    *   **Key-Based Authentication:** Standard password authentication is blocked. Access to the management user (`admin`) is strictly restricted to cryptographic SSH keys secured with a strong **passphrase**.
2.  **Server Identifiers:** The public IP address of the VPS is dynamically masked across all public deployment manifests and operational guidelines (substituted with local loops or loopback placeholders).
3.  **Steam Session States:** Critical Steam Guard tokens, binary active sessions (`*.bin`), and plaintext client configuration structures (`ASF.json`) are completely excluded via `.gitignore` to protect account ownership.
4.  **Twitch Tokens:** Personal browser connection tokens (`OAuth`) are externalized entirely into runtime-only local `.env` files.
5.  **Database Protection:** The SQLite backend database of the VPN panel (`/etc/x-ui/x-ui.db`), which contains client UUIDs, cryptographic salts, and administrative web-interface hashes, is isolated from any backup logic tied to this public repository.
