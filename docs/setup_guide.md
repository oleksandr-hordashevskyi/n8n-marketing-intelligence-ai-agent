# 🛠 Deployment & Setup Guide

This guide provides end-to-end instructions for deploying, configuring, and testing the **Automated Marketing Intelligence & AI Agent Pipeline** and its **Global Error Handler** in your n8n environment.

---

## 📋 Prerequisites

* **n8n Instance:** Self-hosted (Docker/npm) or n8n Cloud (v1.x+ recommended).
* **Google Gemini API Key:** Access token generated from Google AI Studio.
* **Telegram Bot:** A dedicated bot created via `@BotFather` and target channel/group `Chat ID`.
* **Google Cloud Console:** Service Account or OAuth2 credentials with Google Sheets API enabled.

---

## 📥 Step 1: Import Workflows into n8n

1. Download or copy the JSON workflow files from the `workflows/` directory:
   * `workflows/marketing_ai_intelligence_pipeline.json`
   * `workflows/global_error_handler.json`
2. Open your n8n web interface.
3. In the top-right corner, click **Add Workflow** (or **+**).
4. Click the three dots menu (**...**) in the top-right → select **Import from File** (or press `Ctrl + O` / `Cmd + O` and paste the JSON content).
5. Repeat the process for both workflows.

---

## 🔑 Step 2: Configure Credentials & Environment

Create or link the following credentials in **Settings → Credentials**:

### 1. Google Gemini (Chat Model)
* **Credential Type:** `Google Gemini Chat Model` (or `Google PaLM API`).
* **API Key:** Paste your private key from Google AI Studio.

### 2. Telegram Bot & Channel
* **Credential Type:** `Telegram API`.
* **Access Token:** Paste the HTTP API token provided by `@BotFather`.
* **Chat Configuration:** Ensure the bot is added as an **Administrator** in your target Telegram alerts channel, and configure the node parameter `Chat ID` (e.g., `@your_channel_name` or numeric ID like `-100xxxxxxxxx`).

### 3. Google Sheets (Data Logging)
* **Credential Type:** `Google Sheets OAuth2 API`.
* **Target Spreadsheet:** Create a Google Sheet with the following headers in row 1:
  `Timestamp | Metric_Name | Ingested_Value | AI_Summary | Status`
* In the **Append Record** node, specify your **Spreadsheet ID** and the target sheet tab name.

---

## 🛡 Step 3: Link the Global Error Handler

1. Open the primary workflow (`marketing_ai_intelligence_pipeline`).
2. Click the workflow settings icon (gear icon ⚙️ near the workflow name).
3. Under **Error Workflow**, select `Global_Error_Handler` from the dropdown list.
4. Save the workflow settings.

> **Note:** Any unhandled failure across nodes (API rate-limiting, network timeouts, invalid JSON responses) will immediately trigger `Global_Error_Handler`, routing formatted diagnostic metadata directly to your Telegram channel.

---

## 🧪 Step 4: Verification & Production Run

1. Open `marketing_ai_intelligence_pipeline`.
2. Click **Test Step** or **Execute workflow** via the manual trigger (`When clicking 'Execute workflow'`).
3. Verify sequential execution:
   * Node `HTTP Request` returns HTTP 200 with JSON payload.
   * Node `Normalize & Validate Data` formats data into normalized attributes.
   * Node `AI Agent` with `Google Gemini` generates concise marketing bullet points.
   * Node `Google Sheets` logs a new row.
   * Node `Telegram` delivers the formatted alert.
4. Switch the workflow toggle from **Inactive** to **Active** to enable the automated schedule.
