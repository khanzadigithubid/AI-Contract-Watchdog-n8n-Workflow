# 🐕 AI Contract Watchdog — n8n Workflow

> An intelligent n8n automation that monitors contract PDFs in Google Drive, extracts key renewal dates using Claude AI (Anthropic), and emails you before auto-renewals sneak up on you.

![Workflow Diagram](images/workflow-diagram.png)

---

## 📋 What It Does

This workflow runs in **three parts**:

### A — When a Contract Arrives
1. **Drive Trigger** watches a designated Google Drive folder for new files.
2. **Is it a PDF?** — If not, sends a Gmail alert asking for a proper PDF.
3. **Download & Prepare** — Downloads the file and converts it to base64.
4. **Claude Reads It** — Anthropic API (Claude) reads the contract with 3 retries.
5. **Parse Result** — Extracts JSON data including renewal dates and confidence level.
6. **Save to Sheet** — Appends or updates a row in Google Sheets (keyed by `file_id`).
7. **Needs Review?** — If confidence is low, sends a Gmail review alert.

### B — Every Morning at 9 AM (Asia/Karachi)
1. **Schedule Trigger** fires daily at 9 AM.
2. **Read Contracts** — Reads all rows from Google Sheets.
3. **Find Renewals** — Identifies contracts inside the cancellation window.
4. **Build & Send Email** — Constructs an urgency-based alert and sends via Gmail (only if urgency has risen).
5. **Mark Alerted** — Updates the sheet flags so no duplicate alerts are sent.

### C — If Anything Breaks
- **Error Trigger** catches any node failure.
- **Format Error** node captures the node name + error message.
- **Error Alert** sends a Gmail notification with a direct run link.

---

## 🛡️ Built-in Safeguards

| Safeguard | Details |
|---|---|
| **No duplicates** | Re-uploading a file updates its existing row, not a new one |
| **No spam** | Alerts only fire when urgency rises; weekly reminder at HIGH |
| **No silent failures** | Unsure reads become `needs_review` + trigger an email |

---

## 🗂️ Google Sheet Structure

One row per contract, keyed by `file_id`.

| Column | Description |
|---|---|
| `file_id` | Unique Google Drive file ID |
| `status` | `ok` or `needs_review` |
| `renewal_date` | Extracted renewal/expiry date |
| `urgency` | Alert level (LOW / MEDIUM / HIGH) |
| `alerted` | Flag to prevent duplicate emails |

---

## 🔧 Prerequisites

- [n8n](https://n8n.io/) (self-hosted or cloud)
- Google Drive + Gmail + Google Sheets credentials configured in n8n
- Anthropic API key (Claude)

---

## 🚀 Setup

1. **Import the workflow** — In n8n, go to **Workflows → Import from File** and upload `ai-contract-watchdog-n8n-workflow.json`.
2. **Connect credentials** — Link your Google OAuth2 and Anthropic API credentials to the relevant nodes.
3. **Create the Google Sheet** — Make a sheet named `Contracts` with the columns listed above.
4. **Set your Drive folder** — Update the Drive Trigger node with your contracts folder ID.
5. **Activate** — Toggle the workflow ON.

---

## 📁 Repo Structure

```
ai-contract-watchdog-n8n-workflow/
├── ai-contract-watchdog-n8n-workflow.json   # n8n workflow export
├── images/
│   └── workflow-diagram.png                 # Workflow architecture diagram
└── README.md
```

---

## 🏷️ Tech Stack

- **n8n** — Workflow automation
- **Claude (Anthropic)** — AI contract reading & date extraction
- **Google Drive** — Contract storage & trigger
- **Google Sheets** — Contract database
- **Gmail** — Alerts & notifications

---

## 📄 License

MIT License — free to use, modify, and share.

---

*Built with ❤️ using n8n + Claude AI*
