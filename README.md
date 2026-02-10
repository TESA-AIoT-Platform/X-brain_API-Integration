# 🎓 TESAIoT Integration Examples

> **🇹🇭** ชุดตัวอย่างการเชื่อมต่อที่พร้อมใช้งานจริง สำหรับการเชื่อมต่ออุปกรณ์และแอปพลิเคชันเข้ากับ TESAIoT Platform
>
> **🇬🇧** Production-ready integration examples demonstrating device-to-platform and application-to-platform connectivity patterns

---

## 🎯 Learning Objectives

**🇹🇭 หลังจากศึกษาตัวอย่างเหล่านี้ คุณจะสามารถ:**
- เข้าใจการเชื่อมต่ออุปกรณ์ IoT ผ่าน MQTT (mTLS และ Server-TLS)
- สร้างแอปพลิเคชันที่เรียกใช้ TESAIoT API ผ่าน APISIX Gateway
- ใช้ Node-RED สร้าง dashboard และ custom nodes
- เข้าใจรูปแบบความปลอดภัยและการจัดการใบรับรอง (Certificate Management)

**🇬🇧 After completing these examples, you will be able to:**
- Connect IoT devices via MQTT using both mTLS and Server-TLS authentication
- Build applications that consume TESAIoT APIs through the APISIX Gateway
- Create Node-RED dashboards and custom nodes for platform integration
- Understand security patterns and certificate lifecycle management

---

## 📂 Example Repository Structure

```
tutorial/examples/
├── 📄 README.md                      # This guide (TH/EN)
│
├── 📱 device-to-platform/            # C-based IoT device examples
│   ├── common-c/                     # Shared utilities (Mongoose helpers, TLS setup)
│   ├── mTLS/                         # Mutual TLS authentication example
│   │   ├── main.c                    # Device client with cert-based auth
│   │   ├── data_schema.c             # Telemetry data formatting
│   │   ├── scripts/                  # CSR generation, credential sync
│   │   └── CLI_Python/               # Python-based testing tools
│   └── serverTLS/                    # Server-TLS + API key authentication
│       ├── main.c                    # Device client with password auth
│       ├── scripts/                  # Setup and deployment scripts
│       └── CLI_Python/               # REST API testing samples
│
├── 🖥️ application-to-platform/       # Python CLI for API Gateway integration
│   ├── app.py                        # Typer-based CLI application
│   ├── config.yaml                   # Configuration (base_url, timeout, schemas)
│   ├── requirements.txt              # Python dependencies
│   ├── run.sh                        # Bootstrap and execution helper
│   └── .env.example                  # Environment variable template
│
├── 🔗 nodered-to-platform/           # Node-RED custom nodes + dashboard
│   ├── src/                          # TypeScript custom nodes
│   │   ├── lib/client.ts             # Axios client with retry logic
│   │   └── nodes/                    # TESAIoT-specific nodes
│   ├── flows/tesaiot-flow.json       # Pre-built dashboard flow
│   ├── settings.js                   # Node-RED runtime configuration
│   ├── run.sh                        # Build and start automation
│   └── Dockerfile                    # Container-ready deployment
│
└── 🧪 psoc-to-platform/              # PSoC E84 microcontroller examples
    ├── firmware/                     # MCU firmware with MQTT/HTTPS
    ├── scripts/                      # Device provisioning automation
    └── README.md                     # PSoC-specific setup guide
```

---

## 🚀 Quick Start by Use Case

### 🔧 IoT Device Developer (Embedded C)

**Goal**: Connect a device to TESAIoT via MQTTS

**🇹🇭 เลือกตัวอย่าง:**
- **mTLS** — ใช้ใบรับรองอุปกรณ์ (สูงสุดความปลอดภัย)
- **Server-TLS** — ใช้ username/password (เหมาะสำหรับต้นแบบ)

**🇬🇧 Choose your authentication method:**
- **mTLS** — Certificate-based (highest security, industrial-grade)
- **Server-TLS** — Username/password (quick prototyping, testing)

```bash
# Navigate to device examples
cd device-to-platform/mTLS

# Generate CSR and provision device
./scripts/generate_csr.sh --profile industrial --plan

# Build device client
make device_client

# Run device simulator
./build/device_client
```

### 🐍 Application Developer (Python/REST API)

**Goal**: Build a backend service that queries TESAIoT APIs

**🇹🇭 ใช้ Python CLI ตัวอย่าง:**

```bash
cd application-to-platform

# Setup environment
./run.sh bootstrap
cp .env.example .env
# Edit .env and add your TESAIOT_API_KEY

# List devices
./run.sh run devices list --limit 10

# Get telemetry for specific device
./run.sh run devices telemetry <device-id> --since 1h

# Stream real-time data
./run.sh run devices stream <device-id> --duration 60
```

**🇬🇧 Key features:**
- ✅ Type-safe Python client with Rich CLI output
- ✅ Configurable timeouts and retry logic
- ✅ Real-time WebSocket streaming support
- ✅ Comprehensive error handling

### 🎨 Integration Architect (Node-RED)

**Goal**: Create visual dashboards and automation flows

**🇹🇭 สร้าง Dashboard ด้วย Node-RED:**

```bash
cd nodered-to-platform

# Install dependencies and build custom nodes
./run.sh bootstrap
./run.sh build

# Start Node-RED
./run.sh start

# Access dashboard at http://localhost:1880
# Import flows/tesaiot-flow.json for pre-built dashboard
```

**🇬🇧 Included custom nodes:**
- `tesaiot-api-gateway` — Gateway configuration node
- `tesaiot-device-lists` — Fetch device inventory
- `tesaiot-device-profile` — Get device details
- `tesaiot-device-data` — Query telemetry data
- `tesaiot-api-usage` — Platform statistics

---

## 📚 Detailed Walkthrough by Example

### 1️⃣ Device-to-Platform (C/Embedded)

#### 🔐 Common Security Building Blocks

**🇹🇭 โมดูล `common-c/` มีฟังก์ชันพื้นฐาน:**
- `tesa_mg_connect()` — Mongoose MQTT connection with TLS
- `tesa_json_format()` — JSON payload builder with schema validation
- `tesa_retry_backoff()` — Exponential backoff for reconnection

**🇬🇧 Shared utilities in `common-c/`:**
- Defensive programming patterns for resource-constrained devices
- TLS certificate loading and validation
- Error handling and retry mechanisms
- Apache 2.0 licensed with detailed comments

#### 🔑 mTLS Authentication Example

**Security Level**: ⭐⭐⭐⭐⭐ (Industrial/Medical-grade)

**🇹🇭 กระบวนการ:**
1. **สร้าง CSR** — `scripts/generate_csr.sh --profile medical`
2. **ลงทะเบียน** — Upload CSR ผ่าน Admin UI
3. **ดาวน์โหลดใบรับรอง** — Sync credentials to device
4. **เชื่อมต่อ** — MQTTS บน port 8883 ด้วย client cert

**🇬🇧 Workflow:**
1. **Generate CSR** with device-specific Subject Alternative Names (SANs)
2. **Sign via Vault PKI** through Admin UI or API
3. **Download certificate bundle** (cert + key + CA chain)
4. **Connect to EMQX** on port 8883 with mTLS enabled

**Key files:**
- `main.c:245` — Certificate loading and TLS setup
- `scripts/generate_csr.sh:78` — Multi-SAN support with validation
- `CLI_Python/publish_mqtt_sample.sh` — Testing tool with dry-run mode

#### 🔓 Server-TLS Authentication Example

**Security Level**: ⭐⭐⭐ (Development/Prototyping)

**🇹🇭 เหมาะสำหรับ:**
- การพัฒนาต้นแบบ
- อุปกรณ์ที่ไม่มีพื้นที่จัดเก็บใบรับรอง
- การทดสอบและ debugging

**🇬🇧 Use cases:**
- Rapid prototyping and testing
- Devices with limited secure storage
- Integration testing before production deployment

**Connection setup:**
```c
// Server-TLS: username/password over encrypted channel
mg_mqtt_connect(conn, &opts);
// Port: 8884 (Server-TLS)
// Auth: Device API key + password
// TLS: Server certificate validation only
```

---

### 2️⃣ Application-to-Platform (Python CLI)

#### 📊 Rich CLI Features

**🇹🇭 คุณสมบัติ:**
- **Typer framework** — Type-safe command arguments
- **Rich tables** — Colorized output with pagination
- **Config file** — YAML-based defaults (base_url, timeout, schema)
- **Environment support** — `.env` file for secrets
- **WebSocket streaming** — Real-time telemetry with keep-alive

**🇬🇧 Available commands:**

| Command | Purpose | Example |
|---------|---------|---------|
| `devices list` | 🇹🇭 แสดงรายชื่ออุปกรณ์<br>🇬🇧 List device inventory | `--limit 50 --status active` |
| `devices show` | 🇹🇭 รายละเอียดอุปกรณ์<br>🇬🇧 Device profile details | `<device-id>` |
| `devices telemetry` | 🇹🇭 ข้อมูล telemetry<br>🇬🇧 Historical telemetry | `--since 30m --limit 100` |
| `devices schema` | 🇹🇭 โครงสร้างข้อมูล<br>🇬🇧 Schema definitions | `--schema-type industrial_device` |
| `devices stream` | 🇹🇭 ข้อมูลแบบ real-time<br>🇬🇧 Live WebSocket stream | `--duration 60` |
| `stats summary` | 🇹🇭 สถิติภาพรวม<br>🇬🇧 Platform KPIs | Total/Active/Secure devices |

#### 🔧 Configuration Reference

**`config.yaml` structure:**
```yaml
api_gateway:
  base_url: https://admin.tesaiot.com/api/v1/external
  timeout: 30
  verify_tls: true

telemetry:
  default_limit: 100
  time_formats:
    - iso8601
    - rfc3339

schemas:
  default_type: industrial_device
```

**Environment variables** (`.env`):
```bash
TESAIOT_API_KEY=tesa_ak_xxxxxxxxxxxxxxxx  # Required
TESAIOT_ORGANIZATION_ID=org-123           # Optional (multi-org)
TESAIOT_USER_EMAIL=user@example.com       # Optional (audit trail)
TESAIOT_REALTIME_WS_URL=wss://...         # Optional (custom WS endpoint)
```

---

### 3️⃣ Node-RED Integration

#### 🎨 Custom Nodes Architecture

**🇹🇭 สถาปัตยกรรม:**
- **TypeScript** — Type-safe node development
- **Config node pattern** — Centralized API key and client
- **Axios client** — Retry logic with exponential backoff
- **FlowFuse Dashboard** — Pre-styled components matching Admin UI

**🇬🇧 Node registry:**

| Node | Icon | Function |
|------|------|----------|
| `tesaiot-api-gateway` | ⚙️ | Configuration node (API key, base URL) |
| `tesaiot-device-lists` | 📋 | Fetch device inventory with filters |
| `tesaiot-device-profile` | 👤 | Get detailed device profile |
| `tesaiot-device-data` | 📊 | Query telemetry data with time range |
| `tesaiot-api-usage` | 📈 | Platform statistics and health |

#### 🖼️ Dashboard Components

**Pre-built cards in `flows/tesaiot-flow.json`:**

| Card | Data Source | Visualization |
|------|-------------|---------------|
| **Device Snapshot** | `/devices` API | Table (25 rows) + summary stats |
| **Device Profile** | `/devices/:id` | Key-value pairs with badges |
| **Telemetry Chart** | TimescaleDB query | Line chart (heart rate, temp, SpO₂) |
| **API Usage** | Dashboard stats API | Metric cards with trends |

**🇹🇭 การปรับแต่ง theme:**
- สไตล์อยู่ใน `theme/custom.css`
- ใช้ตัวแปร CSS ตาม design system ของ TESAIoT
- รองรับ light/dark mode

**🇬🇧 Theme customization:**
- Styles defined in `theme/custom.css`
- CSS variables aligned with TESAIoT design tokens
- Light/dark mode support

---

## 🧪 Testing & Validation

### ✅ Pre-Flight Checklist

**🇹🇭 ก่อนรัน production:**

| Step | Command | Expected Result |
|------|---------|-----------------|
| 1. **C compilation** | `make device_client` | Binary: `build/device_client` |
| 2. **CSR validation** | `./scripts/generate_csr.sh --plan` | Preview without file creation |
| 3. **Python syntax** | `python3 -m compileall app.py` | No syntax errors |
| 4. **API connectivity** | `./run.sh run devices list` | HTTP 200 + device table |
| 5. **MQTT connection** | `./CLI_Python/publish_mqtt_sample.sh --dry-run` | Connection success log |

**🇬🇧 Environment prerequisites:**

- **C examples**: GCC, make, libssl-dev, Mongoose library
- **Python examples**: Python ≥ 3.9, pip, virtualenv
- **Node-RED**: Node.js ≥ 18.18, npm, TypeScript
- **Network**: Access to `admin.tesaiot.com` (ports 443, 8883, 8884, 8765)

### 🐛 Common Issues & Solutions

**🇹🇭 ปัญหาที่พบบ่อย:**

| Issue | Symptom | Solution |
|-------|---------|----------|
| **TLS handshake fail** | `SSL certificate verify failed` | Check CA bundle path in code |
| **MQTT auth fail** | `Connection refused (5)` | Verify API key / cert in Admin UI |
| **WebSocket disconnect** | `Connection closed by peer` | Check firewall, use WSS not WS |
| **Python import error** | `ModuleNotFoundError` | Run `./run.sh bootstrap` again |
| **Node-RED blank dashboard** | No data displayed | Re-import `tesaiot-flow.json` |

**🇬🇧 Debug commands:**

```bash
# Test TLS connection
openssl s_client -connect admin.tesaiot.com:443 -showcerts

# Verify MQTT broker
mosquitto_sub -h admin.tesaiot.com -p 8883 \
  --cafile ca.crt --cert device.crt --key device.key \
  -t 'test/topic' -v

# Check Python dependencies
pip list | grep -i tesa

# Node-RED debug logs
DEBUG=tesaiot:* ./run.sh start
```

---

## 🤝 Contributing Guidelines

### 📝 Code Standards

**🇹🇭 กฎการเขียนโค้ด:**

1. **Apache 2.0 header** — ทุกไฟล์ source (`.c`, `.h`, `.py`, `.sh`, `.ts`)
2. **Why/What/How comments** — อธิบายก่อนโค้ดที่ซับซ้อน
3. **Placeholder credentials** — ใช้ `tesa_ak_<redacted>` ใน README
4. **Session logging** — บันทึกการเปลี่ยนแปลงใน `.codex/sessions/`
5. **Gitignore profiles** — ใช้ `scripts/dev/switch-gitignore.sh` ก่อน push

**🇬🇧 Required practices:**

- **Bilingual docs** — TH/EN for all public-facing content
- **Error handling** — Graceful failures with actionable messages
- **Security first** — Never commit real API keys, certificates, or tokens
- **Reproducibility** — All examples must work with minimal setup

### 🔄 Git Workflow

```bash
# 1. Create feature branch
git checkout -b feature/new-example

# 2. Make changes and test
./scripts/dev/validate-example.sh

# 3. Commit with descriptive message
git commit -m "feat(examples): add BLE gateway integration example"

# 4. Push to both remotes (Wiroon + Mirror)
./scripts/dev/push-both-remotes.sh
```

### 📬 Reporting Issues

**🇹🇭 หากพบปัญหา:**
1. เปิด GitHub Issue ระบุ:
   - ตัวอย่างที่ใช้ (mTLS, Server-TLS, Node-RED, etc.)
   - ขั้นตอนที่ทำให้เกิดปัญหา
   - Log หรือ error message
   - Environment (OS, Python version, Node.js version)

**🇬🇧 When opening issues:**
- Use template: `[Example Name] Brief description`
- Include reproduction steps
- Attach relevant logs (redact sensitive info)
- Tag with appropriate labels

---

## 📖 Additional Resources

### 🔗 Quick Links

- **📘 Platform Documentation** → [../../docs/README.md](../../docs/README.md)
- **🏛️ Architecture Guide** → [../../docs/en/architecture/platform-architecture.md](../../docs/en/architecture/platform-architecture.md)
- **🔐 PKI Operations** → [../../docs/en/security/vault-pki-operations.md](../../docs/en/security/vault-pki-operations.md)
- **🎮 API Playground** → https://admin.tesaiot.com/api/docs (interactive API testing)

### 📚 External References

**🇹🇭 เอกสารอ้างอิง:**
- [Node-RED Creating Nodes](https://nodered.org/docs/creating-nodes/) — Official guide
- [FlowFuse Dashboard](https://dashboard.flowfuse.com/) — Component library
- [Mongoose Networking Library](https://mongoose.ws/) — Embedded MQTT/HTTP client
- [Typer CLI Framework](https://typer.tiangolo.com/) — Python CLI builder

**🇬🇧 Recommended reading:**
- **MQTT 5.0 Specification** — Understanding QoS, retained messages
- **X.509 Certificate Standards** — PKI and TLS fundamentals
- **TimescaleDB Best Practices** — Time-series query optimization

---

## 🎯 Next Steps

### 🚀 Beginner Path

1. **Start with application-to-platform** (easiest)
   - No hardware required
   - Quick API familiarization
   - Rich CLI output for learning

2. **Move to Node-RED** (visual learning)
   - Pre-built dashboard
   - Drag-and-drop flow creation
   - Real-time data visualization

3. **Progress to device examples** (embedded development)
   - Understand certificate lifecycle
   - Learn MQTT patterns
   - Firmware integration skills

### 🧑‍💻 Advanced Path

1. **Customize device firmware**
   - Add sensors and custom telemetry schemas
   - Implement OTA update handlers
   - Optimize for power efficiency

2. **Build production integrations**
   - Extend Python CLI with custom commands
   - Create enterprise dashboards in Node-RED
   - Integrate with third-party systems

3. **Contribute back**
   - Share your example code
   - Improve documentation
   - Help community members

---

<div align="center">

**🎓 Tutorial Version**: 2.0.0
**🔄 Last Updated**: 2025-10-03
**📧 Questions?** Open an issue or reach out via team chat

*Happy integrating! 🚀*

</div>
