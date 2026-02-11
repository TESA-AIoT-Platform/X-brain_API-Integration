# 🔧 Device → TESAIoT → X-brain Integration (HTTPS Server-TLS)

> 🇹🇭 ตัวอย่างการเชื่อมต่ออุปกรณ์ IoT เข้าสู่ TESAIoT Platform ผ่าน HTTPS (Server-TLS) และเตรียมข้อมูลสำหรับส่งต่อไปยัง X-brain Platform
> 🇬🇧 Example demonstrating secure device-to-platform connectivity using HTTPS Server-TLS, designed for downstream integration with X-brain Platform.

---

# 🎯 Objective

## 🇹🇭 ตัวอย่างนี้สาธิต:

* Device ส่ง telemetry ผ่าน HTTPS Server-TLS
* ใช้ API Key authentication
* ใช้ credential bundle จาก TESAIoT Admin UI
* โครงสร้าง payload แบบ medical / environment sensor schema
* เตรียม data pipeline สำหรับ X-brain analytics

## 🇬🇧 This example demonstrates:

* HTTPS ingestion using Server-TLS
* API key authentication
* Device credential bundle usage
* Structured telemetry payload
* Compatibility with X-brain data ingestion

---

# 🧭 Architecture

```
IoT Device
   |
   | HTTPS + Server TLS
   v
TESAIoT Platform
   |
   | REST Integration
   v
X-brain Platform
```

---

# 📂 Directory Structure

```
device-to-platform/serverTLS/
├── main.c
├── data_schema.c
├── scripts/
├── certs_credentials/
└── CLI_Python/
```

---

# 🔐 Credentials Setup

1. เข้า Admin UI: [https://admin.tesaiot.com/devices](https://admin.tesaiot.com/devices)
2. สร้าง Device
3. Download Server-TLS credentials
4. แตกไฟล์ใส่:

```
certs_credentials/
```

ต้องมีไฟล์:

```
api_key.txt
device_id.txt
mqtt_username.txt
mqtt_password.txt
ca_certificate.pem
```

> HTTPS mode ใช้เฉพาะ:

* api_key.txt
* device_id.txt
* CA certificate


Download Server-TLS bundle from Admin UI and place inside:

```
certs_credentials/
```

Required files:

* api_key.txt
* device_id.txt
* CA certificate

---

# 📦 Telemetry Payload Schema

Payload ถูก generate โดย logic ที่คล้าย:

```python
{
  "device_id": "...",
  "timestamp": "ISO8601 UTC",
  "data": {
    "temperature": float,
    "temperature_unit": "°C",
    "humidity": float,
    "humidity_unit": "%RH",
    "lux": float,
    "lux_unit": "cd",
    "soil": float,
    "soil_unit": "%V/V"
  }
}
```

---

## 🇹🇭 Sensor Fields

* temperature
* humidity
* lux
* soil

## 🇬🇧 Purpose

Structured telemetry for:

* environmental monitoring
* agriculture
* medical lab simulations
* AI pipeline ingestion

---

# 🚀 Run (HTTPS Server-TLS)

```bash
COMM_MODE=HTTPS \
API_BASE_URL=https://tesaiot.com \
CERTS_DIR=./certs_credentials \
./device_client
```

---

# 🔐 Security Model

* TLS encryption
* Server certificate validation
* API Key authentication
* No client certificate required

---

# 🧪 Expected Result

* HTTPS 200 response
* telemetry visible in TESAIoT
* ready for application-to-platform forwarding into X-brain

---

# 🧩 Integration Notes

## TESAIoT Responsibilities

* Device onboarding
* Authentication
* Data ingestion

## X-brain Responsibilities

* AI inference
* Data analytics
* Model deployment


## Troubleshooting (ข้อผิดพลาดพบบ่อย)
* HTTPS 401: API key ไม่อัปเดต → ดาวน์โหลด HTTPS bundle ใหม่โดยเลือก include_api_key=true แล้วรันสคริปต์ซิงค์อีกครั้ง `./scripts/sync_credentials.sh --device-dir ../download`

---
