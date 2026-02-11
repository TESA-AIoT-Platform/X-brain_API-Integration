# 🎓 TESAIoT ↔ X-brain Integration Examples

> **🇹🇭** ตัวอย่างการเชื่อมต่อระหว่าง **TESAIoT Platform** และ **X-brain Platform** สำหรับการรับ-ส่งข้อมูลอุปกรณ์และแอปพลิเคชันผ่าน HTTPS โดยใช้ Server-TLS
>
> **🇬🇧** Integration examples demonstrating secure connectivity between **TESAIoT Platform** and **X-brain Platform** using HTTPS (Server-TLS) for both device-to-platform and application-to-platform

---

# 🎯 Learning Objectives

## 🇹🇭 หลังจากศึกษาตัวอย่างนี้ คุณจะสามารถ:

* เชื่อมต่อ IoT device เข้าสู่ TESAIoT ผ่าน HTTPS (Server-TLS)
* ส่ง telemetry จาก TESAIoT ไปยัง X-brain Platform
* สร้าง application integration ผ่าน API Gateway
* เข้าใจ architecture การเชื่อมต่อระหว่าง IoT Platform และ AI/MLOps Platform

## 🇬🇧 After completing this tutorial, you will be able to:

* Connect IoT devices via HTTPS using Server-TLS authentication
* Forward telemetry from TESAIoT to X-brain Platform
* Build application integrations via REST APIs
* Understand cross-platform architecture between IoT and AI platforms

---

# 🧭 Integration Architecture Overview

```
+-------------------+
| IoT Device        |
| HTTPS (Server-TLS)|
+---------+---------+
          |
          v
+-------------------+
| TESAIoT Platform  |
| API Gateway       |
+---------+---------+
          |
          | REST API / Webhook
          v
+-------------------+
| X-brain Platform  |
| ML / Analytics    |
+-------------------+
```

## 🇹🇭 Concept

* Device ส่งข้อมูลผ่าน HTTPS → TESAIoT
* TESAIoT ทำหน้าที่ ingestion + device management
* Application layer ดึงข้อมูลจาก TESAIoT เพื่อส่งเข้า X-brain

## 🇬🇧 Flow Summary

1. Device sends telemetry to TESAIoT via HTTPS.
2. TESAIoT manages ingestion and authentication.
3. Application layer forwards data into X-brain for processing.

---

# 📂 Repository Structure

```
tutorial/examples/
│
├── device-to-platform/
│   └── serverTLS/
│       ├── main.c
│       ├── scripts/
│       └── CLI_Python/
│
└── application-to-platform/
    ├── app.py
    ├── config.yaml
    ├── run.sh
    └── .env.example
```

---

# 🚀 Quick Start

## 🔧 Device-to-Platform (HTTPS Server-TLS)

### 🇹🇭 Use Case

* Device ไม่มี certificate storage
* ต้องการ onboarding ง่าย
* เหมาะสำหรับ prototype หรือ edge device

### 🇬🇧 Overview

Server validates TLS certificate, device authenticates via API key.

### Connection Concept

```c
// HTTPS POST telemetry
POST https://api.tesaiot.com/device/telemetry

Headers:
Authorization: Bearer <DEVICE_API_KEY>
Content-Type: application/json
```

---

### Steps

```bash
cd device-to-platform/serverTLS

# Configure API key
export TESAIOT_DEVICE_KEY=xxxx

# Build device client
make

# Run simulator
./device_client
```

---

## 🖥️ Application-to-Platform (TESAIoT → X-brain)

### 🇹🇭 Goal

* ดึง telemetry จาก TESAIoT
* ส่งต่อเข้า X-brain เพื่อ AI processing

### 🇬🇧 Workflow

1. Call TESAIoT API
2. Transform payload
3. Send to X-brain ingestion endpoint

---

### Setup

```bash
cd application-to-platform

./run.sh bootstrap
cp .env.example .env
```

Edit `.env`:

```
TESAIOT_API_KEY=xxxx
XBRAIN_API_URL=https://xbrain/api
XBRAIN_TOKEN=xxxx
```

---

### Example Commands

```bash
# List devices
./run.sh run devices list

# Fetch telemetry
./run.sh run devices telemetry <device-id>

# Forward to X-brain
./run.sh run integration forward <device-id>
```

---

# 🔐 Security Model

## HTTPS Server-TLS

* TLS encryption enforced
* Server certificate validation
* API key authentication
* Suitable for:

  * Development
  * Edge devices
  * Gateway integration

---

# 🧪 Testing Checklist

| Step             | Expected Result         |
| ---------------- | ----------------------- |
| HTTPS connection | TLS handshake success   |
| API call         | HTTP 200                |
| Data forwarding  | Data visible in X-brain |

---

# 🧩 Integration Design Notes

## 🇹🇭 Architecture Roles

* TESAIoT → Device lifecycle + ingestion
* X-brain → AI processing + analytics

## 🇬🇧 Responsibilities

* TESAIoT handles connectivity and telemetry ingestion
* X-brain performs model execution and analytics workflows

---

# 🎯 Recommended Learning Path

1. Run application-to-platform example
2. Test telemetry fetch
3. Enable forwarding to X-brain
4. Integrate real device

---

<div align="center">

**Tutorial Version**: 1.0.0
**Integration Scope**: TESAIoT ↔ X-brain

</div>
