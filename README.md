# 🔍 NetScan — Network Intelligence Dashboard

A full-stack, cyberpunk-themed Nmap scanner web application with real-time streaming output, scan history, and export features.

---

## ✨ Features

- **4 Scan Modes**: Quick, Full, OS Detection, Top-10K Port Scan  
- **Real-time terminal output** via Server-Sent Events (SSE)  
- **Structured results**: port table, OS fingerprint, scan stats  
- **Scan history** saved in browser localStorage  
- **Export**: JSON or TXT download, clipboard copy  
- **Rate limiting**: 10 scans per 15 min per IP  
- **Input sanitization**: blocks shell injection characters  
- **Responsive**: works on mobile and desktop  

---

## 🗂 Folder Structure

```
nmap-scanner/
├── client/                  # React frontend
│   ├── public/index.html
│   └── src/
│       ├── App.jsx          # Main application (all pages + components)
│       ├── index.js         # Entry point
│       └── index.css        # Cyberpunk dark theme
├── server/
│   ├── index.js             # Express API + nmap execution + SSE
│   └── package.json
├── package.json             # Root convenience scripts
└── README.md
```

---

## 🚀 Quick Start

### Prerequisites

1. **Node.js** v18+ — https://nodejs.org  
2. **nmap** installed on your system:

   ```bash
   # Ubuntu/Debian
   sudo apt-get install nmap

   # macOS
   brew install nmap

   # Windows (WSL recommended, or download from https://nmap.org/download.html)
   ```

### Installation & Run

```bash
# 1. Clone / download this project
cd nmap-scanner

# 2. Install server dependencies
cd server && npm install && cd ..

# 3. Install client dependencies
cd client && npm install && cd ..

# 4. Start the backend (terminal 1)
cd server && node index.js
# → API running at http://localhost:3001

# 5. Start the frontend (terminal 2)
cd client && npm start
# → App running at http://localhost:3000
```

Open **http://localhost:3000** in your browser.

---

## 🔌 API Reference

### `GET /api/health`
Returns server status.

### `GET /api/nmap-check`
Checks if nmap is installed and returns its version.

### `GET /api/scan/stream?target=<host>&scanType=<type>`
SSE endpoint — streams scan events in real time.

**Parameters:**
| Param | Values | Description |
|-------|--------|-------------|
| `target` | IP or domain | The host to scan |
| `scanType` | `quick`, `full`, `os`, `ports` | Scan mode |

**SSE Events emitted:**
| Event | Payload | Description |
|-------|---------|-------------|
| `start` | `{scanId, target, scanType}` | Scan initiated |
| `progress` | `{percent, message}` | Progress update |
| `output` | `{line}` | Raw nmap stdout line |
| `warning` | `{message}` | nmap warning/note |
| `result` | `{scanId, parsed, rawOutput}` | Final structured results |
| `done` | `{scanId}` | Scan complete |
| `error` | `{message}` | Error occurred |

---

## 🔒 Security Measures

| Measure | Implementation |
|---------|---------------|
| Input validation | Regex check for valid IP/CIDR/domain |
| Shell injection prevention | Blocks `;`, `&`, `|`, `` ` ``, `$`, `<`, `>`, `\`, `'`, `"`, `!` |
| Rate limiting | `express-rate-limit`: 10 req / 15 min / IP |
| Allowed scan types whitelist | Only 4 safe flag sets, no raw socket or aggressive scripts |
| Process timeout | 5-minute max per scan via SIGTERM |
| CORS restriction | Only localhost:3000 allowed |

---

## 🧪 Nmap Commands Used

```bash
# Quick Scan
nmap -T4 -F --open <target>

# Full Scan
nmap -T4 -p- --open -sV <target>

# OS Detection
nmap -T4 -O -sV --open <target>

# Port Scan (Top 10K)
nmap -T4 -p 1-10000 --open -sV <target>
```

> Note: OS detection (`-O`) may require root/sudo on some systems.

---

## 📱 UI Pages

- **Scanner** — Target input, scan type selection, real-time terminal output, structured results
- **History** — All past scans (up to 50), expandable with export options
- **Settings** — Security info, preferences, backend configuration

---

## 🛠 Tech Stack

| Layer | Tech |
|-------|------|
| Frontend | React 18, vanilla CSS (no Tailwind to keep zero build complexity) |
| Backend | Node.js, Express 4 |
| Scan Engine | nmap via `child_process.spawn` |
| Streaming | Server-Sent Events (SSE) |
| Storage | Browser `localStorage` |
| Rate Limiting | `express-rate-limit` |

---

## 🔮 Bonus / Roadmap

- [ ] JWT authentication (login/signup)
- [ ] PostgreSQL scan history database
- [ ] Live CVE lookup per service/version
- [ ] Scheduled recurring scans
- [ ] Network topology map visualization

---

## ⚠️ Legal Notice

Only scan hosts you own or have **explicit written permission** to test.  
Unauthorized port scanning may be illegal in your jurisdiction.
