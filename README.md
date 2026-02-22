# ⚡ WaldonCFscanner-python | Advanced Xray-Core VLESS Verifier

[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![Platform](https://img.shields.io/badge/platform-Windows%20%7C%20Linux%20%7C%20macOS-lightgrey.svg)]()
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

**[🇮🇷 برای مطالعه نسخه فارسی اینجا کلیک کنید (Persian Version)](README_fa.md)**

An incredibly fast, highly-optimized Cloudflare IP Scanner engineered specifically to bypass severe internet censorship infrastructures (such as the Great Firewall) using modern proxy protocols.

Created by [@amirrezas](https://github.com/amirrezas). Inspired by the works of MortezaBashsiz and the global anti-censorship community.

---

## 🚀 Quick Start: Installation & Usage (Zero-Setup Execution)

We believe anti-censorship tools should be accessible to everyone. You do not need to understand Python virtual environments, `pip`, or Go binaries to use this tool. The engine features an autonomous bootstrap sequence.

### 🪟 Windows Setup
1. **Install Python:** Download Python from [python.org](https://www.python.org/downloads/). **Crucial:** During installation, you *must* check the box that says **"Add Python to PATH"**.
2. **Download the Project:** Click the green **"Code"** button at the top of this GitHub repository and select **"Download ZIP"**. Extract the folder to your computer.
3. **Run the Scanner:** Open Command Prompt (`cmd`), navigate to your extracted folder, and run:
   ```cmd
   python scanner.py
   ```

### 🐧 Linux (Ubuntu / Debian)
1. **Install Python:** Open your terminal and install Python and pip:
   ```bash
   sudo apt update && sudo apt install python3 python3-pip
   ```
2. **Clone the Project:**
   ```bash
   git clone [https://github.com/amirrezas/WaldonCFscanner-python.git](https://github.com/amirrezas/WaldonCFscanner-python.git)
   cd WaldonCFscanner-python
   ```
3. **Run the Scanner:**
   ```bash
   python3 scanner.py
   ```

### 🍏 macOS
1. **Install Python:** Open your terminal and run `brew install python` (requires Homebrew).
2. **Clone and Run:**
   ```bash
   git clone [https://github.com/amirrezas/WaldonCFscanner-python.git](https://github.com/amirrezas/WaldonCFscanner-python.git)
   cd WaldonCFscanner-python
   python3 scanner.py
   ```

### ⚙️ How to use the Auto-Config Generator
You can personalize the scanner to automatically generate ready-to-use VPN profiles based on your private server.

* **Via JSON:** Place your server's base VLESS config inside a file named `config.json` in the root folder before running.
* **Via URI (Clipboard):** While the program is running, paste your `vless://...` clipboard link directly into the **VLESS URI** input box inside the Terminal Dashboard.

When the scanner discovers a top-tier IP, it will create an `output_configs/` directory containing customized `.json` client files and a `vless_links.txt` file packed with shareable, high-speed URIs.

---

## 🌍 The Ethical Standpoint: Internet as a Human Right

In 2016, the United Nations Human Rights Council passed a landmark resolution unequivocally condemning internet shutdowns, affirming that *"the same rights that people have offline must also be protected online."* Access to information, freedom of expression, and digital privacy are fundamental human rights. 

This tool exists because no government or entity should have the power to artificially isolate its citizens from the global community. **WaldonCFscanner** empowers individuals to bypass oppressive firewalls, resist Deep Packet Inspection (DPI), and reclaim their inalienable right to the open web.

---

## 🏗️ System Architecture: Why This Scanner Stands Out

### The Core Problem: Deep Packet Inspection (DPI)
Traditional Cloudflare scanners rely on simple ICMP pings or basic TCP handshakes. While those methods verify that an edge node is reachable, they completely fail to account for modern censorship mechanisms. Advanced firewalls deploy Deep Packet Inspection (DPI) and Active Probing. A node might return a 20ms ping, but the firewall will instantly severe the TCP connection the moment it detects unauthorized SNIs, anomalous byte distributions, or legacy VPN signatures (like OpenVPN or WireGuard).

### The VLESS over TLS Solution
**WaldonCFscanner** resolves this by verifying the *actual proxy tunnel protocol* through a cryptographically secure channel. By utilizing the VLESS protocol inside a TLS wrapper, the DPI filter cannot distinguish the proxy traffic from a standard user visiting a secure HTTPS website.

```text
+-------------------+       +--------------------+       +-------------------+       +-------------------+
|                   |       |                    |       |                   |       |                   |
|   User Machine    |       |   Great Firewall   |       |  Cloudflare Edge  |       |    Uncensored     |
|   (Scanner Core)  | =====>|    (DPI Filter)    | =====>|  (Reverse Proxy)  | =====>|     Internet      |
|  [VLESS Client]   |  TLS  | Passes Inspection  | VLESS |  [VLESS Server]   | HTTP  |                   |
|                   |       |                    |       |                   |       |                   |
+-------------------+       +--------------------+       +-------------------+       +-------------------+
```

---

## ⚙️ Deep Dive: The 4-Stage Verification Engine

To find 10 flawless, high-speed IPs out of billions of potential IPv4 and IPv6 combinations without exhausting local system memory or crashing the host router, the scanner acts as an aggressive, asynchronous, hardware-aware assembly line. 

```text
+---------------+    +---------------+    +---------------+    +---------------+    +---------------+
|               |    |   STAGE 1:    |    |   STAGE 2:    |    |   STAGE 3:    |    |   STAGE 4:    |
|   Subnet IP   |    |   TCP Probe   |    |   TLS & SNI   |    |   1MB Speed   |    |  Xray VLESS   |
|   Generator   | -> |  (Port 443)   | -> |  (Handshake)  | -> |  (Throughput) | -> | (Verification)|
|               |    |               |    |               |    |               |    |               |
+---------------+    +---------------+    +---------------+    +---------------+    +---------------+
  (Millions)           (Thousands/sec)      (Hundreds/sec)        (Tens/sec)           (The Winners)
```

### Stage 1: Asynchronous TCP Probing (Layer 4)
* **Objective:** Rapidly eliminate "dead" IP addresses that do not respond on port 443.
* **Mechanism:** Python's `asyncio.open_connection` is utilized to fire off hundreds of concurrent socket requests. Rather than waiting for a full HTTP response, the worker instantly closes the socket the millisecond the `SYN-ACK` packet is received.
* **Concurrency limits:** The engine reads the `os.cpu_count()` to calculate safe socket ceilings (e.g., capping at 1000 on Windows due to OS kernel limitations, but unlocking up to 3000 on Linux/macOS utilizing `epoll` and `kqueue`).

### Stage 2: TLS SNI Injection (Layer 7)
* **Objective:** Cryptographically verify the node belongs to Cloudflare and bypass SNI-based domain blocking.
* **Mechanism:** The engine crafts an unverified `ssl.create_default_context()` and establishes a secure tunnel. Crucially, it injects a "clean" Iranian domain (e.g., `zula.ir`) into the Server Name Indication (SNI) header. If the firewall allows the handshake, the engine sends a raw `GET / HTTP/1.1` request. If the response headers contain Cloudflare signatures (or `403 Forbidden` from Cloudflare's edge), the IP is verified as a valid proxy entry point.

### Stage 3: Pure Python Speed Test (Throughput Benchmarking)
* **Objective:** Filter out IPs that are heavily throttled or suffer from severe packet loss.
* **Mechanism:** A passing IP is subjected to a raw HTTP download of a 1MB payload from `speed.cloudflare.com/__down`. The engine calculates the precise Time-Delta from the first byte to the final chunk. IPs that fail to sustain a high Mbps threshold are discarded immediately, preventing slow nodes from clogging the final Xray queue.

### Stage 4: Headless Xray-Core Verification (The VIP Room)
* **Objective:** The ultimate proof of concept. Verify that the IP can successfully route VLESS websocket traffic through the user's specific proxy configuration.
* **Mechanism:** 1. The engine deeply copies the user's configuration in-memory.
  2. It dynamically strips unnecessary parameters (like `routing` or `dns` blocks) to prevent the `geosite.dat` crash loop.
  3. It binds an isolated, headless instance of the official `Xray-core` binary to a randomized local port (between 20000 and 50000) using `asyncio.create_subprocess_exec`.
  4. An `aiohttp` proxy session routes a live connection through the newly spawned local Xray instance to `cp.cloudflare.com`.
  5. **Strict Teardown:** Once verified, the Python script sends a `SIGTERM` (and fallback `SIGKILL`) to instantly destroy the subprocess, ensuring zero memory leaks or zombie processes.

---

## 🧠 Algorithmic Optimizations Under the Hood

### 1. Stratified Subnet Randomization
Naively randomizing IPs often results in the scanner getting "stuck" in massive, heavily blocked subnets (like the `104.16.x.x` ranges). The algorithm maps the imported `ipv4.txt` and groups networks by their first octet. It then selects randomly *across* these groups, ensuring a truly global distribution of tested datacenters.

### 2. The "Hot-Subnet" Feedback Loop
When a Stage 2 TLS handshake succeeds, the engine recognizes that the `/24` subnet block is currently unblocked by the firewall. It appends this block to a rolling cache of "Hot Subnets". The Stage 1 generator has a 30% probability of pulling an IP from this hot cache rather than the global list, effectively "mining" successful datacenters for more working nodes.

### 3. Asynchronous Backpressure Mitigation
Because Xray-core takes ~1.5 seconds to initialize and route a websocket tunnel, Stage 4 is significantly slower than Stage 1. To prevent the program from consuming gigabytes of RAM by queuing up millions of IPs, the engine utilizes bounded `asyncio.Queue(maxsize=X)`. If the queues fill up, the preceding stages utilize non-blocking drops (`asyncio.TimeoutError`), discarding excess IPs to maintain a perfectly fluid, lag-free pipeline.

### 4. Bi-Directional Configuration Parsing
The scanner features a robust RegEx engine capable of translating back and forth between standard Xray JSON files and URL URI strings (e.g., `vless://`). 
* If a user pastes a URI, the parser extracts the UUID, SNI, ALPN, Path, and Host, mathematically rebuilding the complex nested JSON dictionary required to boot Xray-core. 
* Conversely, when an IP passes, the engine traverses the JSON syntax tree to encode the new IP back into a perfectly formatted, shareable `vless://` string.

---

## 💻 The Textual UI (TUI) Dashboard

Built on the modern `Textual` framework, the Terminal User Interface operates in a completely decoupled thread from the asynchronous scanning engine. 

* **Live Table Refreshing:** As IPs pass Stage 4, they are appended to an internal tuple list. The UI clears the table, recalculates the **Quality Score** (Speed × 1000 / Xray_Latency), sorts the entire list in descending order, and redraws the datatable instantly.
* **Component Reactivity:** Using Textual's `@on(Input.Changed)` decorators, pasting a link or adjusting the Target IP limit immediately updates the internal engine states without requiring a program restart.

---

## 🛠️ Troubleshooting & Contribution

If you encounter issues, please utilize the built-in diagnostic tools before opening a GitHub Issue.

1. **The Professional Logger:** Click the **"Save Log"** button in the TUI. The Python `logging` module runs silently in the background, capturing standard outputs, socket timeouts, JSON parsing errors, and raw Subprocess Tracebacks.
2. **Review `scanner_error.log`:** Check this file in your root directory. It will explicitly tell you if `xray.exe` is missing, if a port failed to bind, or if an `IndexError` occurred during table sorting.
3. **Open an Issue:** Navigate to the [GitHub Issues tab](https://github.com/amirrezas/WaldonCFscanner-python/issues) and attach your `scanner_error.log` file. Community contributions, pull requests, and bug reports are highly encouraged!

---

*Disclaimer: This software is provided as an open-source tool for network diagnostics, latency optimization, and ensuring open access to the free internet. The developers are not responsible for the misuse of this tool. Please adhere to your local networking guidelines responsibly.*