# 🐍 Python CLI — HTTPS Server-TLS Telemetry Sender

> 🇹🇭 ตัวอย่าง Python CLI สำหรับจำลอง IoT device ส่ง telemetry ผ่าน HTTPS Server-TLS ไปยัง TESAIoT และใช้ต่อกับ X-brain Platform
> 🇬🇧 Python CLI tool simulating device telemetry ingestion using HTTPS Server-TLS

---

# 🎯 Purpose

* Test device ingestion without firmware
* Validate payload schema
* Simulate real device telemetry
* Prepare data flow for X-brain pipeline

---

# 📂 Files

| File                 | Description                         |
| -------------------- | ----------------------------------- |
| post_https_sample.py | generate payload and POST telemetry |

---

# 🔐 Credential Requirements

Place bundle files in:

```
../certs_credentials/
```

Required:

```
api_key.txt
device_id.txt
ca_certificate.pem
```

---

# 📦 Payload Generator

Script builds telemetry using:

```python
generate_payload(device_id)
```

Example structure:

```json
{
  "device_id": "device123",
  "timestamp": "2025-01-01T00:00:00Z",
  "data": {
    "temperature": 25.1,
    "humidity": 60.0,
    "lux": 500,
    "soil": 20.3
  }
}
```

Values are randomly generated to simulate sensors.

---

# 🚀 Quick Start

```bash
cd CLI_Python

./post_https_sample.py
```

---

## Advanced Usage

```bash
# Single telemetry document to production 
./post_https_sample.py --base-url https://tesaiot.com 

# Override base URL (e.g., staging) and preview payload only
./post_https_sample.py --base-url https://staging.tesaiot.com --dry-run

# periodic sending every 5 seconds for two minutes
./post_https_sample.py --base-url https://tesaiot.com --period 2 --interval 5
```

---

## Flags & Environment Variables / พารามิเตอร์สำคัญ

| Flag / env | 🇹🇭 ความหมาย | 🇬🇧 Description |
| --- | --- | --- |
| `--certs-dir`, `CERTS_DIR` | โฟลเดอร์เก็บ bundle (ค่าเริ่มต้น `../certs_credentials`) | Directory containing the credential bundle |
| `--base-url`, `BASE_URL` | เปลี่ยนฐาน URL หากไม่ได้ใช้ production | Override ingest base URL (falls back to `endpoints.json`) |
| `--endpoint`, `ENDPOINT` | ระบุปลายทาง API (ค่าเริ่มต้น `/api/v1/telemetry`) | API endpoint path to POST to |
| `--period`, `--interval` | ระยะเวลาส่งซ้ำเป็นนาที / วินาที | Duration in minutes and send interval in seconds |
| `--dry-run` | แสดง payload แล้วออก | Print payload and exit without sending |

---

# 🔐 HTTPS Security

* TLS verification enabled
* CA certificate required
* API key via HTTP header

---

# 🧪 Expected Workflow

```
Python CLI
   |
   v
TESAIoT HTTPS API
   |
   v
Application integration → X-brain
```

---

# 🧩 Developer Notes

This script is intended for:

* integration testing
* CI pipelines
* data simulation for AI training

---
