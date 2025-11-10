# AI Agent - Intelligent Personal Assistant

> A powerful multi-agent AI system built with n8n that understands Indonesian language and manages your daily tasks through email, calendar, and financial tracking.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![n8n](https://img.shields.io/badge/n8n-powered-blue.svg)](https://n8n.io)
[![Google Gemini](https://img.shields.io/badge/AI-Google%20Gemini-orange.svg)](https://ai.google.dev)

## Overview

AI Agent is an intelligent personal assistant that combines multiple specialized AI agents to help you manage your daily activities. Built with n8n workflow automation and powered by Google Gemini, it understands casual Indonesian commands and can:

- **Send and manage emails** through Gmail
- **Schedule and update calendar events** in Google Calendar
- **Track financial transactions** automatically to Google Sheets
- **Understand casual Indonesian conversations** - no rigid commands needed!

## Key Features

### 1. Conversational AI in Bahasa Indonesia
Talk naturally in Indonesian without worrying about strict command formats:
```
"catat kan di sheet ku aku baru saja makan siang pada pukul 8 a m seharga 15.000"
"schedule kan aku main bola besok pada 8 a m"
"kirim email ke Budi tentang meeting besok"
```

### 2. Multi-Agent Architecture
The system uses specialized sub-agents for different tasks:

- **Email Agent**: Draft, send, reply, label, and manage Gmail messages
- **Calendar Agent**: Create, update, delete, and view Google Calendar events
- **Money Management Agent**: Parse Indonesian number formats and log expenses/income
- **Marketing Agent**: Plan and execute marketing campaigns
- **Think Agent**: Complex reasoning and decision verification

### 3. Smart Parsing
Understands various Indonesian number formats:
- `15.000` or `15,000` → 15000
- `15rb` or `15k` → 15000
- `1,5jt` or `1.5 juta` → 1500000

### 4. Contextual Memory
Maintains conversation history to understand context and follow-up requests.

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                         Webhook Endpoint                         │
│                    (receives user messages)                      │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                        Main AI Agent                             │
│              (Google Gemini + Intent Recognition)                │
└──┬──────────────┬──────────────┬──────────────┬─────────────────┘
   │              │              │              │
   ▼              ▼              ▼              ▼
┌────────┐  ┌──────────┐  ┌────────────┐  ┌──────────┐
│ Email  │  │ Calendar │  │   Money    │  │Marketing │
│ Agent  │  │  Agent   │  │Management  │  │  Agent   │
└───┬────┘  └────┬─────┘  └─────┬──────┘  └────┬─────┘
    │            │              │              │
    ▼            ▼              ▼              ▼
┌────────┐  ┌──────────┐  ┌────────────┐  ┌──────────┐
│ Gmail  │  │ Google   │  │  Google    │  │ External │
│  API   │  │ Calendar │  │  Sheets    │  │   APIs   │
└────────┘  └──────────┘  └────────────┘  └──────────┘
```

## Use Cases

### Personal Productivity
- Schedule meetings with natural language
- Log daily expenses without opening spreadsheets
- Quick email drafts and replies

### Financial Tracking
```
User: "tadi beli kopi 25rb sama nasi goreng 20rb"
Agent: Baik, saya catat 2 pengeluaran:
        1. Beli kopi: Rp 25.000
        2. Nasi goreng: Rp 20.000
        Total: Rp 45.000
```

### Calendar Management
```
User: "schedule kan meeting dengan tim besok jam 2 siang"
Agent: Sudah saya jadwalkan meeting tim besok jam 14:00
```

### Email Automation
```
User: "kirim email ke klien tentang update project"
Agent: Email berhasil dikirim ke klien dengan update terbaru
```

## Installation

### Prerequisites
- [n8n](https://n8n.io) instance (cloud or self-hosted)
- Google Account with:
  - Gmail API enabled
  - Google Calendar API enabled
  - Google Sheets API enabled
- Google Gemini API key

### Setup Steps

1. **Clone or Download Workflow**
   ```bash
   git clone https://github.com/YOUR_USERNAME/ai-creation-hackaton.git
   cd ai-creation-hackaton
   ```

2. **Import to n8n**
   - Open your n8n instance
   - Go to **Workflows** → **Import from File**
   - Select `index.json`

3. **Configure Credentials**

   You'll need to set up these credentials in n8n:

   - **Gmail OAuth2** (`gmailOAuth2`)
   - **Google Calendar OAuth2** (`googleCalendarOAuth2Api`)
   - **Google Sheets OAuth2** (`googleSheetsOAuth2Api`)
   - **Google Gemini API** (`googlePalmApi`)

4. **Update Google Sheets Document ID**

   In the "Append row in sheet in Google Sheets" node:
   - Replace the `documentId` with your Google Sheets ID
   - Ensure sheet has columns: `uang yg terpakai`, `di gunakan untuk`, `waktu penggunaan`

5. **Update Calendar IDs**

   In Calendar Agent nodes:
   - Replace `calendar` values with your Google Calendar ID

6. **Activate Workflow**
   - Click **Active** toggle in n8n
   - Copy the webhook URL

7. **Test the Webhook**
   ```bash
   curl -X POST https://YOUR_N8N_URL/webhook/66f5746b-8fa0-4f83-a47c-df5c89ba6a66 \
     -H "Content-Type: application/json" \
     -d '{
       "message": "catat pengeluaran 50rb untuk bensin",
       "user": {"id": "test-user", "name": "Test User"},
       "history": []
     }'
   ```

## Configuration

### Customizing Agents

Each agent can be customized by editing its system prompt in the workflow:

- **Email Agent** (`index.json:193`): Email management behavior
- **Calendar Agent** (`index.json:357`): Calendar scheduling logic
- **Money Management** (`index.json:521`): Transaction parsing rules
- **Marketing Agent** (`index.json:471`): Campaign planning strategies

### Time Zone Configuration

The workflow is currently set to Jakarta timezone (`Asia/Jakarta`). To change:
1. Find the Code Tool node in Calendar Agent
2. Update the timezone in the JavaScript code

## API Reference

### Webhook Endpoint

**POST** `/webhook/{webhook-id}`

**Request Body:**
```json
{
  "message": "catat pengeluaran 15rb untuk kopi",
  "user": {
    "id": "user-123",
    "name": "John Doe"
  },
  "history": [
    {
      "role": "user",
      "content": "previous message"
    },
    {
      "role": "assistant",
      "content": "previous response"
    }
  ]
}
```

**Response:**
```json
{
  "response": "Baik, saya catat pengeluaran untuk kopi sebesar Rp 15.000..."
}
```

## Examples

### Financial Tracking Examples

```bash
# Expense tracking
"catat pengeluaran 50rb untuk bensin"
"tadi beli kopi 25rb"
"habis bayar iklan facebook 500 ribu"

# Income tracking
"dapat transfer dari klien 2 juta"

# Multiple transactions
"tadi beli kopi 25rb sama nasi goreng 20rb"
```

### Calendar Examples

```bash
# Create event
"schedule meeting besok jam 2 siang"
"buat event main bola sabtu pagi jam 8"

# View events
"apa jadwal ku besok?"

# Update event
"reschedule meeting ke jam 3 sore"
```

### Email Examples

```bash
# Send email
"kirim email ke Budi tentang project update"

# Draft email
"buatkan draft email untuk klien"

# Reply to email
"balas email terakhir dari manager"
```

## Troubleshooting

### Common Issues

**Issue**: Workflow not responding
- **Solution**: Check if workflow is activated and webhook URL is correct

**Issue**: Google API errors
- **Solution**: Verify all OAuth2 credentials are properly configured and authorized

**Issue**: Number parsing errors
- **Solution**: Check if the amount is in supported format (e.g., `15rb`, `15.000`, `15k`)

**Issue**: Calendar timezone issues
- **Solution**: Verify timezone in Code Tool matches your location

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## Roadmap

- [ ] Add WhatsApp integration
- [ ] Support for multiple languages beyond Indonesian
- [ ] Voice input support
- [ ] Analytics dashboard for tracked expenses
- [ ] AI-powered budget recommendations
- [ ] Slack integration
- [ ] SMS notifications for important events

## Tech Stack

- **n8n**: Workflow automation platform
- **Google Gemini AI**: Natural language processing
- **Gmail API**: Email management
- **Google Calendar API**: Event scheduling
- **Google Sheets API**: Data storage
- **Node.js**: Runtime environment

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

- Built with [n8n](https://n8n.io)
- Powered by [Google Gemini AI](https://ai.google.dev)
- Inspired by the need for Indonesian language AI assistants

## Demo

[Add demo video or screenshots here]

## Team

This project is created by:

- **Sayed Hanif**
- **Vicky Adi Firmansyah**

