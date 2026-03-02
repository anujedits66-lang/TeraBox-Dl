<div align="center">

# ☁️ TeraBox Downloader — Telegram Bot

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.12-3776AB?style=for-the-badge&logo=python&logoColor=white"/>
  <img src="https://img.shields.io/badge/python--telegram--bot-21.6-blue?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/Pyrogram-2.0-orange?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/Docker-ready-2496ED?style=for-the-badge&logo=docker&logoColor=white"/>
</p>

A modular Telegram bot that resolves TeraBox share links, uploads files directly to Telegram, and sends a direct download link — all in one message.

---
</div>

## ✨ Features

- 🔗 Resolves any TeraBox share URL to a direct `dlink`
- 📤 Uploads files directly into Telegram chat (no third-party app needed)
- 🛡️ Cloudflare bypass via `curl_cffi` Chrome impersonation
- 📊 Download progress shown live while fetching large files
- 🖼️ Thumbnail preview when available
- 💾 TTL cache to avoid re-fetching the same link
- 🐳 Docker ready

---

## 📁 Project Structure

```
terabox_bot/
├── main.py               ← Entry point
├── config.py             ← All env vars loaded once
├── requirements.txt
├── Dockerfile
├── .env.example
│
├── bot/
│   ├── app.py            ← Builds and wires the PTB Application
│   └── handlers.py       ← /start, /help, link message handler
│
├── core/
│   ├── terabox.py        ← TeraBox API client (jsToken scrape → share/list)
│   ├── cache.py          ← In-memory TTL cache
│   └── uploader.py       ← File size routing + download + Pyrogram upload
│
└── utils/
    ├── url.py            ← URL validation, surl extraction
    └── formatting.py     ← format_bytes()
```

---

## 📦 File Size Handling

| File Size | Method |
|-----------|--------|
| ≤ 50 MB | Uploaded instantly via Bot API |
| 50 MB – 2 GB | Downloaded on server → uploaded via Pyrogram MTProto (bot session) |
| 50 MB – 4 GB | Same, but using owner's **user session** (`SESSION_STRING`) |
| > limit | Direct download link sent only |

The 4 GB mode is unlocked by setting `SESSION_STRING` in your `.env` at deploy time. If it's not set, the limit is 2 GB.

---

## 🚀 Setup

### Prerequisites

- Python 3.12+
- A bot token from [@BotFather](https://t.me/BotFather)
- `API_ID` + `API_HASH` from [my.telegram.org](https://my.telegram.org) *(required for files > 50 MB)*
- The `ndus` cookie from [terabox.com](https://www.terabox.com)

### Getting the `ndus` Cookie

1. Log in at [https://www.terabox.com](https://www.terabox.com)
2. Open DevTools (`F12`) → **Application** → **Cookies** → `terabox.com`
3. Copy the value of the `ndus` cookie

### Generating a Session String (for 4 GB support)

```bash
python -c "from pyrogram import Client; Client('tmp', api_id=YOUR_ID, api_hash='YOUR_HASH').run()"
```
Log in with your phone number when prompted. Copy the printed session string into `SESSION_STRING` in `.env`.

---

### Local Setup

```bash
git clone https://github.com/yourname/terabox-tg-bot.git
cd terabox-tg-bot

python -m venv venv && source venv/bin/activate

pip install -r requirements.txt

cp .env.example .env
# Fill in BOT_TOKEN, NDUS_COOKIE, API_ID, API_HASH
# Optionally set SESSION_STRING for 4 GB support

python main.py
```

### Docker

```bash
docker build -t terabox-tg-bot .

docker run -d --env-file .env --name terabox-bot terabox-tg-bot
```

---

## ⚙️ Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `BOT_TOKEN` | ✅ | Bot token from @BotFather |
| `NDUS_COOKIE` | ✅ | `ndus` cookie from terabox.com |
| `API_ID` | For files > 50 MB | From [my.telegram.org](https://my.telegram.org) |
| `API_HASH` | For files > 50 MB | From [my.telegram.org](https://my.telegram.org) |
| `SESSION_STRING` | No | Owner user session — raises upload limit from 2 GB → 4 GB |
| `CACHE_TTL` | No | Seconds to cache resolved links (default: `7200`) |
| `LOG_LEVEL` | No | `DEBUG` / `INFO` / `WARNING` (default: `INFO`) |

---

## 🌐 Supported Domains

`terabox.com` · `terabox.app` · `1024terabox.com` · `teraboxshare.com` · `teraboxlink.com` · `dm.terabox.app`

---

## 🤝 Credits

Based on the original TeraBox API logic by:

🌟 **[@cantarella_wuwa](https://t.me/cantarella_wuwa)**  
🌟 **[@cantarellabots](https://t.me/cantarellabots)**

---
<p align="center"><i>Developed with ❤️ for the open-source community.</i></p>
