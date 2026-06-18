# 🤖 COPUX V2.0
**Advanced Telegram AI Assistant untuk Komunitas Tech & Emulator**

COPUX adalah bot Telegram berbasis Node.js yang dirancang khusus untuk mengelola grup komunitas, menyediakan troubleshooting instan, dan mencari solusi akurat secara real-time.

## ✨ Fitur Overpowered
- 🛡️ **Hybrid Rate Limit:** Sistem cooldown otomatis anti-spam.
- 🧠 **Smart Group Sessions:** Membedakan konteks obrolan masing-masing member.
- 🔍 **Web Search 3-Tier:** Mencari solusi via Serper, Tavily, dan DuckDuckGo.
- 🕷️ **Firecrawl Integration:** Scrape dan baca isi link secara langsung.
- 👁️ **Vision & YouTube Engine:** Deteksi teks dari gambar (screenshot error) & bedah video.
- 💾 **Auto-Save History:** Memori percakapan aman meskipun server restart mendadak.

---

## 🚀 Cara Deploy ke VPS/RDP

**1. Persiapan Sistem**
Pastikan server Anda sudah terinstall Node.js (v18+), Git, dan PM2:
```bash
npm install -g pm2
```

**2. Clone Repository**
```bash
git clone [https://github.com/NOYSZ/Bot-Telegram.git](https://github.com/NOYSZ/Bot-Telegram.git)
cd Bot-Telegram
npm install
```

**3. Konfigurasi Environment**
Buat file bernama `.env` di folder utama dan isi dengan variabel berikut:
```env
TELEGRAM_BOT_TOKEN=
AI_API_KEY=
FIRECRAWL_API_KEY=
SERPER_API_KEY=
```

**4. Jalankan Bot (24/7)**
Gunakan PM2 agar bot tetap hidup di background meskipun terminal ditutup:
```bash
pm2 start bot.js --name "copux"
pm2 save
pm2 startup
```

