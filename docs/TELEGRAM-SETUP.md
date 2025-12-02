# 📱 Telegram Bot Setup Guide

**Setup Telegram notifications untuk workflow otomatis**

---

## Why Telegram?

Workflow sekarang jalan otomatis setiap 6 jam. Telegram akan mengirim notifikasi ketika video selesai dibuat, lengkap dengan download links!

**Keuntungan:**
- ✅ Real-time notifications
- ✅ Akses video URLs langsung dari HP
- ✅ No need to check n8n manually
- ✅ Group notifications support

---

## Step 1: Create Telegram Bot (3 minutes)

### 1.1 Open BotFather

1. Buka Telegram
2. Search: `@BotFather`
3. Start chat dengan `/start`

### 1.2 Create New Bot

1. Send command: `/newbot`
2. BotFather akan minta nama bot
3. Berikan nama: `YouTube Shorts AI Bot` (atau terserah kamu)
4. BotFather akan minta username
5. Berikan username: `yourname_shorts_bot` (harus unique & ends dengan `_bot`)

### 1.3 Get Bot Token

Setelah sukses, BotFather akan send message seperti ini:

```
Done! Congratulations on your new bot. 
You will find it at t.me/yourname_shorts_bot. 
You can now add a description...

Use this token to access the HTTP API:
123456789:ABCdefGHIjklMNOpqrsTUVwxyz1234567890
```

**COPY TOKEN INI!** ✅

Format: `123456789:ABCdefGHIjklMNOpqrsTUVwxyz1234567890`

⚠️ **Keep this secret! Don't share with anyone!**

---

## Step 2: Get Your Chat ID (2 minutes)

### Option A: Personal Chat (Recommended)

1. Search bot kamu di Telegram: `@yourname_shorts_bot`
2. Send message `/start` ke bot kamu
3. Now, search: `@userinfobot`
4. Start chat: `/start`
5. `@userinfobot` akan reply dengan info kamu
6. **Copy your ID** (contoh: `123456789`)

### Option B: Group Chat

Jika mau send ke group:

1. Create Telegram group
2. Add bot kamu ke group
3. Add `@RawDataBot` ke group
4. `@RawDataBot` akan send JSON data
5. Cari value di `"chat" > "id"` (contoh: `-987654321`)
6. **Copy Chat ID** (negative number untuk group)
7. Remove `@RawDataBot` dari group (sudah tidak perlu)

---

## Step 3: Configure n8n (2 minutes)

### 3.1 Add to Environment Variables

**Edit your `.env` file:**

```bash
# Buka .env
nano .env

# Atau
vim .env
```

**Add these lines:**

```bash
# Telegram Configuration
TELEGRAM_BOT_TOKEN=123456789:ABCdefGHIjklMNOpqrsTUVwxyz1234567890
TELEGRAM_CHAT_ID=123456789
```

Replace with YOUR values!

### 3.2 Restart n8n

**If using Docker:**

```bash
docker restart n8n
```

**If using npm:**

```bash
# Stop (Ctrl+C)
# Then restart:
n8n start
```

### 3.3 Setup Telegram Credential in n8n

1. Open n8n: `http://localhost:5678`
2. Go to: **Credentials** (left sidebar)
3. Click: **Add Credential**
4. Select: **Telegram API**
5. Fill in:
   - **Name:** `Telegram Bot` (or anything)
   - **Access Token:** Paste your bot token
6. Click: **Create**

---

## Step 4: Test the Setup! (1 minute)

### 4.1 Test Bot Responds

1. Message your bot: `/start`
2. If bot doesn't respond, bot token might be wrong

### 4.2 Test Workflow

1. Open workflow: `YouTube Shorts - Blueprint V2 (CometAPI)`
2. Click **Execute Workflow** (manual test)
3. Wait 2-3 minutes
4. Check Telegram - you should receive message! 🎉

**Expected message:**

```
🎬 Video Baru Selesai!

📝 Judul: AI Revolution in Gaming!
🎯 Topic: AI Revolution in Gaming
📊 Total Scenes: 5
⏰ Created: 02/12/2025 12:00

📹 Download Video Clips:

1. Scene 1:
https://storage.cometapi.com/...

2. Scene 2:
https://storage.cometapi.com/...

... (all 5 scenes)

✅ Status: completed

💡 Next Step: Download all clips and combine in video editor
```

---

## Troubleshooting

### Bot Doesn't Send Messages

**Check 1: Bot Token**
```bash
# Verify in .env
cat .env | grep TELEGRAM_BOT_TOKEN

# Should show your token
```

**Fix:** Make sure token is correct, no extra spaces

**Check 2: Chat ID**
```bash
# Verify in .env  
cat .env | grep TELEGRAM_CHAT_ID

# Should show your ID
```

**Fix:** Make sure ID is correct number

**Check 3: n8n Restarted**
```bash
# Restart n8n to load new env vars
docker restart n8n
```

**Check 4: Bot is Started**
- Message your bot `/start` first
- Bot can only send to users who started conversation

### Error: "Forbidden: bot was blocked by the user"

**Fix:** You blocked the bot. Unblock dan send `/start` again.

### Error: "Bad Request: chat not found"

**Fix:** Chat ID salah. Get ID lagi dari `@userinfobot`

### Messages in Wrong Format

**Fix:** Telegram credentials mungkin tidak tersimpan di n8n. Re-add credential.

---

## Customization

### Change Message Format

Edit node **"Format Telegram Message"** di workflow:

```javascript
// Customize message here
let message = `🎬 *Video Baru Selesai!*\n\n`;
message += `📝 *Judul:* ${data.title}\n`;
// Add more fields as needed...
```

### Add Emojis

```javascript
message += `🎥 *Video Ready!*\n`;
message += `🔥 *Topic:* ${data.topic}\n`;
message += `⚡ *Scenes:* ${data.total_scenes}\n`;
```

### Send to Multiple Chats

**Option 1:** Add multiple Chat IDs separated by comma in code node

**Option 2:** Create group, add bot, use group ID

---

## Schedule Settings

### Change Schedule Interval

Edit node **"Schedule Trigger"** in workflow:

**Current: Every 6 hours**

**Options:**

- Every hour: `"hoursInterval": 1`
- Every 3 hours: `"hoursInterval": 3`
- Every 12 hours: `"hoursInterval": 12`
- Daily: `"hoursInterval": 24`

Or edit `.env`:

```bash
SCHEDULE_INTERVAL_HOURS=3  # Run every 3 hours
```

### Disable Schedule (Manual Only)

Replace **Schedule Trigger** with **Manual Trigger** node:

1. Delete Schedule Trigger node
2. Add Manual Trigger node
3. Connect to "Set Topic" node

---

## Advanced: Multiple Bots

Jika mau notifications untuk different purposes:

**Create separate bots:**
1. Success notifications: `@yourname_success_bot`
2. Error notifications: `@yourname_error_bot`

**Add error handling:**
- Add error trigger node
- Connect to separate Telegram node
- Send errors to error bot

---

## FAQ

**Q: Apakah bot perlu online terus?**
A: Tidak! Bot jalan di server Telegram. Kamu tidak perlu online.

**Q: Bisa send gambar/video langsung?**
A: Bisa! Edit Telegram node, attach files. But URLs lebih efisien.

**Q: Bot bisa reply command?**
A: Yes! Tapi perlu separate workflow dengan Telegram Trigger. (Advanced)

**Q: Bisa send ke WhatsApp instead?**
A: Bisa! Ganti Telegram node dengan WhatsApp Business API node. (Berbayar)

**Q: Limit berapa message per hari?**
A: Telegram: 30 messages/second per bot. More than enough!

---

## Security Best Practices

### ✅ DO:
- Keep bot token SECRET
- Add `.env` to `.gitignore`
- Only share bot dengan people you trust
- Use private groups for sensitive data
- Rotate token if compromised

### ❌ DON'T:
- Commit bot token to Git
- Share token in public
- Use same bot for prod & test
- Leave bot accessible to strangers

---

## Next Steps

1. ✅ Bot setup complete
2. ✅ Test notifications working
3. ✅ Customize message format (optional)
4. ✅ Adjust schedule (optional)
5. ✅ Let it run automatically! 🎉

**Your workflow now runs every 6 hours and sends results to Telegram!**

---

## Resources

- [Telegram Bot API Docs](https://core.telegram.org/bots/api)
- [BotFather Commands](https://core.telegram.org/bots/features#botfather)
- [n8n Telegram Node Docs](https://docs.n8n.io/integrations/builtin/app-nodes/n8n-nodes-base.telegram/)

---

**Happy Automating! 🤖**

*Now you can create videos on autopilot and get notified on Telegram!*
