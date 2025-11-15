# n8nai : Telegram Bots with HTTPS (Self‑Signed SSL + Ngrok)

This repository provides a fully working **n8n automation environment** with:

- 📡 Multiple Telegram bot workflows (Short Answer, Translator, Image Transcriber, Text Fixer)  
- 🔐 HTTPS using a **self‑signed internal CA**  
- 🌍 Public exposure using **ngrok**  
- 🐳 A ready‑to‑run **Docker Compose** deployment  
- 🔑 Basic authentication + encryption key support  

---

## 📁 Repository Structure

```
.
├── docker-compose.yml
├── LICENSE
├── README.md
├── ssl
│   ├── ca.crt
│   ├── ca.key
│   ├── ca.srl
│   ├── n8n.crt
│   ├── n8n.csr
│   └── n8n.key
└── Workflows
    ├── Image-Transcribe.json
    ├── n8n-Ttranslate.json
    ├── Short-Answer.json
    └── Text-Fixer.json
---

## ⚙️ Environment Variables (`.env`)

```
N8N_HOST=YOUR_HOST_LINK
N8N_ENCRYPTION_KEY=YOUR_KEY
N8N_BASIC_AUTH_ACTIVE=true
N8N_BASIC_AUTH_USER=admin
N8N_BASIC_AUTH_PASSWORD=admin
```

These control HTTPS, login protection, and encryption inside n8n.

---

## 🐳 Docker Compose

This repository includes a working **docker-compose.yml**:

- Mounts SSL certificates  
- Enables HTTPS  
- Enables basic authentication  
- Exposes n8n on port 8080  
- Stores persistent data in a Docker volume  

---

## 🔐 SSL Setup (Self‑Signed CA + Certificates)

To enable HTTPS locally:

```bash
mkdir -p ssl

# 1) Internal CA
openssl genrsa -out ssl/ca.key 4096
openssl req -x509 -new -key ssl/ca.key -days 3650 -sha256 -subj "/CN=asir-internal-ca" -out ssl/ca.crt

# 2) n8n server certificate
openssl genrsa -out ssl/n8n.key 2048
openssl req -new -key ssl/n8n.key -subj "/CN=n8n" -out ssl/n8n.csr
openssl x509 -req -in ssl/n8n.csr -CA ssl/ca.crt -CAkey ssl/ca.key -CAcreateserial   -days 825 -sha256 -extfile <(printf "subjectAltName=DNS:n8n") -out ssl/n8n.crt
```

Place all generated files inside the `ssl/` folder.

---

## 🌍 Public HTTPS Access Using Ngrok

After n8n is running on HTTPS locally:

```bash
ngrok http https://localhost:8080
```

Ngrok will generate a public HTTPS URL, which must be placed inside **.env → N8N_HOST**.

This is required for **Telegram** webhooks to work.

---

## 🤖 Workflows Included

The **Workflows/** folder contains Telegram bots such as:

### 🟦 Short-Answer Bot  
A bot that responds with **short, direct, summarized answers** using Groq + LLM.

### 🟩 Image Transcriber  
Uploads an image → extracts text → sends output back to Telegram.

### 🟧 Text Fixer  
Corrects grammar, improves formatting, and returns a clean text.

### 🟪 Translator  
Translates incoming Telegram messages.

Each workflow uses:
- `Telegram Trigger`
- Groq API / LLM Chains / gemini API
- `Send Telegram Message` nodes

---

## ▶️ Run the Stack

```bash
docker compose up -d
```

n8n will now be available at:

```
https://localhost:8080
```
