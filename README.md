# 🌉 HackBridge

**HackBridge** is a lightweight MCP (Model Context Protocol) bridge that connects AI clients (e.g., Claude Desktop, [5ire](https://github.com/nanbingxyz/5ire)) to a Linux terminal API server — enabling **AI-assisted penetration testing**, **CTF challenge solving**, and **automated recon/exploitation** in real time.

Run terminal commands like `nmap`, `nxc`, `sqlmap`, or any other tool. Interact with web applications using `curl`, `wget`, `gobuster`. Let the AI drive your offensive security workflow — from enumeration to exploitation.

---

## 📝 My Medium Article on This Tool

[![How MCP is Revolutionizing Offensive Security](https://miro.medium.com/v2/resize:fit:828/format:webp/1*g4h-mIpPEHpq_H63W7Emsg.png)](https://yousofnahya.medium.com/how-mcp-is-revolutionizing-offensive-security-93b2442a5096)

👉 [**How MCP is Revolutionizing Offensive Security**](https://yousofnahya.medium.com/how-mcp-is-revolutionizing-offensive-security-93b2442a5096)

---

## 🔍 Use Case

HackBridge enables AI-driven offensive security by:

- Connecting MCP-compatible AI clients (OpenAI, Claude, DeepSeek, Gemini, etc.) to a Linux terminal.
- Exposing a controlled API to execute commands on your Linux machine.
- Letting AI suggest, run, and analyze terminal commands to solve CTF challenges or automate recon/exploitation tasks.
- Allowing MCP apps to send structured requests (e.g., `nmap`, `ffuf`, `curl`) and receive parsed outputs.

Here are some examples from my testing (using Google's AI `Gemini 2.0 Flash`):

### Example: Solving a Web CTF Challenge in RamadanCTF

https://github.com/user-attachments/assets/dc93b71d-9a4a-4ad5-8079-2c26c04e5397

### Example: Attempting to Solve Machine "Code" from HTB

https://github.com/user-attachments/assets/3ec06ff8-0bdf-4ad5-be71-2ec490b7ee27

---

## 🚀 Features

- 🧠 **AI Integration via MCP** — Connect your Linux machine to any MCP-compatible client like Claude Desktop or 5ire.
- 🖥️ **Command Execution API** — Controlled API to execute terminal commands on your Linux machine.
- 🛠️ **Built-in Tool Support** — Pre-configured endpoints for Nmap, Gobuster, Dirb, SQLMap, Hydra, and WPScan.
- 🕸️ **Web Challenge Support** — AI can interact with websites and APIs, capture flags via `curl` and any other tool.
- ⏱️ **Configurable Timeouts** — Per-tool timeout support to handle long-running scans without interruption.
- 🔐 **Built for Offensive Security** — Ideal for red teamers, bug bounty hunters, and CTF players automating common tasks.
- 🔄 **Arbitrary Command Execution** — Execute any terminal command beyond the built-in tools for maximum flexibility.

---

## 📁 Project Structure

```
MCP-Kali-Server/
├── kali_server.py          # Flask API server (runs on Linux/Kali)
├── mcp_server.py           # MCP client bridge (connects AI to API server)
├── mcp-kali-server.json    # Example MCP client configuration
├── requirements.txt        # Python dependencies
└── README.md               # This file
```

---

## 🛠️ Installation & Setup

### Prerequisites

- **Python 3.8+**
- **pip** (Python package manager)
- The following Python packages (installed via `requirements.txt`):
  - `Flask>=3.0.0`
  - `requests>=2.31.0`
  - `mcp>=1.0.0`

### 1. On Your Linux Machine (API Server)

Install HackBridge via apt:

```bash
sudo apt install hackbridge
```

Or install manually:

```bash
git clone <your-repo-url>
cd MCP-Kali-Server
pip install -r requirements.txt
python3 kali_server.py
```

**Command Line Options:**
- `--ip <address>` — IP to bind the server to (default: `127.0.0.1` for localhost only)
  - `127.0.0.1` — Local connections only (secure, recommended)
  - `0.0.0.0` — Allow connections from any interface (⚠️ very dangerous; use with caution)
  - Specific IP — Bind to a particular network interface
- `--port <port>` — Port number (default: `5000`)
- `--debug` — Enable debug mode for verbose logging

**Examples:**

```bash
# Run on localhost only (secure, default)
python3 kali_server.py

# Run on all interfaces (less secure, useful for remote access)
python3 kali_server.py --ip 0.0.0.0

# Run on a specific IP and custom port
python3 kali_server.py --ip 192.168.1.100 --port 8080

# Run with debug mode
python3 kali_server.py --debug
```

### 2. On Your MCP Client Machine (Can Be Local or Remote)

```bash
git clone <your-repo-url>
cd MCP-Kali-Server
pip install -r requirements.txt
```

**MCP Client Command Line Options:**
- `--server <url>` — Kali API server URL (default: `http://localhost:5000`)
- `--timeout <seconds>` — Request timeout in seconds (default: `600`)
- `--debug` — Enable debug logging

**If running the client and server on the same machine:**

```bash
python3 mcp_server.py --server http://127.0.0.1:5000
```

**If on separate machines**, create an SSH tunnel to your Linux machine, then launch the client:

```bash
ssh -L 5000:localhost:5000 user@LINUX_IP
python3 mcp_server.py --server http://127.0.0.1:5000
```

> **Note:** If you're openly hosting the API server on your network (`kali_server.py --ip 0.0.0.0`), you don't need the SSH tunnel — but ⚠️ **this is highly discouraged** for security reasons.

```bash
python3 mcp_server.py --server http://LINUX_IP:5000
```

---

## ⚙️ MCP Client Configuration

### Claude Desktop

Edit `C:\Users\USERNAME\AppData\Roaming\Claude\claude_desktop_config.json`:

```json
{
    "mcpServers": {
        "hackbridge": {
            "command": "python3",
            "args": [
                "/absolute/path/to/mcp_server.py",
                "--server",
                "http://LINUX_IP:5000/"
            ],
            "timeout": 600
        }
    }
}
```

### [5ire](https://github.com/nanbingxyz/5ire) Desktop Application

Simply add an MCP server with the command:
```
python3 /absolute/path/to/mcp_server.py --server http://LINUX_IP:5000
```
5ire will automatically generate the needed configuration files.

---

## 🧰 Supported Tools

| Tool | MCP Function | Description |
|---|---|---|
| **Nmap** | `nmap_scan` | Network scanning & version detection |
| **Gobuster** | `gobuster_scan` | Directory, DNS, and vhost brute-forcing |
| **Dirb** | `dirb_scan` | Web content scanning |
| **SQLMap** | `sqlmap_scan` | SQL injection testing |
| **Hydra** | `hydra_attack` | Password brute-forcing |
| **WPScan** | `wpscan_analyze` | WordPress vulnerability scanning |
| **Any command** | `execute_command` | Execute any arbitrary terminal command |

---

## 🔌 API Endpoints

The Flask API server (`kali_server.py`) exposes the following endpoints:

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/api/command` | Execute any arbitrary command |
| `POST` | `/api/tools/nmap` | Run an Nmap scan |
| `POST` | `/api/tools/gobuster` | Run a Gobuster scan |
| `POST` | `/api/tools/dirb` | Run a Dirb scan |
| `POST` | `/api/tools/sqlmap` | Run a SQLMap scan |
| `POST` | `/api/tools/hydra` | Run a Hydra attack |
| `POST` | `/api/tools/wpscan` | Run a WPScan scan |
| `GET` | `/health` | Server health check |

---

## 🔮 Other Possibilities

Since the AI model can now execute commands on the terminal, the possibilities go beyond just pentesting:

- **Memory Forensics with Volatility** — Automate memory analysis: process enumeration, DLL injection checks, registry extraction from memory dumps.
- **Disk Forensics with SleuthKit** — Automate disk image analysis, timeline generation, file carving, and hash comparisons.
- **OSINT & Recon** — Automate recon workflows using tools like `theHarvester`, `amass`, `subfinder`, etc.
- **Log Analysis** — Parse and analyze security logs, access logs, or system logs.

---

## ⚠️ Disclaimer

This project is intended solely for **educational and ethical testing purposes**. Any misuse of the information or tools provided — including unauthorized access, exploitation, or malicious activity — is **strictly prohibited**.

The author assumes no responsibility for misuse.
