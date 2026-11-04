# Kinsenas — AI Collections Agent (n8n) — project notes

Name (2026-11-04): **Kinsenas** names the most unique feature, payday timing (reminders go out right after the 15th / end of month). Every Filipino gets it right away.

## 1. Link
GitHub: https://github.com/RIll-cmd/Kinsenas-Collection-Agent (local folder: `D:\n8n-collections-agent\`)
Workflow exports (personal data removed): `daily-chase.json`, `handle-replies.json`, `error-alert.json`, `weekly-summary.json`, `add-invoice.json`.
The Telegram assistant is an n8n Agent (no JSON export); see screenshots.

## 2. What it automates
Chases unpaid invoices for a Philippine small business, from first reminder to "call this customer".

- **Add invoice** (n8n Form, login required): owner enters invoice → duplicate check → saved to an n8n Data Table → Telegram confirmation.
- **Daily chase** (every day 9am, Asia/Manila):
  reads invoices → skips Sundays and PH holidays → normal reminders only 1-3 days after payday (15th / end of month); broken promises chased any day →
  Gemini writes the email (tone set by stage, promise score and the owner's notes) → firmer emails wait for Approve/Decline in Telegram (20h timeout) →
  Gmail sends → table updated. Also posts a daily "call these" list for invoices with 3 reminders sent, and scores kept promises.
- **Handle replies** (Gmail trigger, every minute):
  cleans the reply → Gemini Text Classifier (paid / promise / dispute / payment plan / other, English/Tagalog/Taglish, with confidence) →
  promise: Information Extractor turns "sa Friday" into a date → table → thank-you reply in the same thread → Telegram;
  paid: thank-you reply + Gemini reads the GCash/bank screenshot → flags reused reference numbers, wrong amount, odd dates → Telegram report (never marks paid by itself);
  everything else → Telegram "needs you".
- **Weekly summary** (Monday 8am): owed / promised this week / collected / calls needed → Telegram.
- **Error alert**: any failure in the workflows above → Telegram message with workflow, node and error.
- **Collections Assistant** (n8n Agent on a private Telegram bot): owner asks in Taglish ("Sino pinakamalaki utang?", "pause mo si ACME", "dahan-dahan kay A, suki natin yan"); reads the table, saves tone notes, and changes status only after the owner approves.

## 3. Role
Built alone, step by step with Claude (AI assistant) as a guide.

## 4. Who it's for
Personal portfolio / demo project, aimed at Philippine SMEs (schools, clinics, distributors, online sellers).

## 5. Screenshots
See `screenshots/` (canvases, Telegram approval, alerts, emails, data table, agent chat).

## 6. Numbers
Test data only (3 test invoices); no production numbers yet.
- 5 workflows + 1 n8n Agent, about 48 nodes.
- About 40 test runs; every path tested end to end or in sandbox (auto-send, approve, decline, promise, paid, broken promise, payday, holiday, receipt flags, duplicate invoice).
- Found and fixed during testing: Data Table update wiping columns, hidden TAB in a field name, workflow running on UTC instead of Manila time, Gemini free-tier daily limit (spread across models).

## 7. Tools
n8n 3.0 (Cloud): Schedule Trigger, Gmail Trigger, Form Trigger (n8n user auth), Error Trigger, Data Tables, Code (JavaScript/Luxon), Basic LLM Chain + Structured Output Parser, Text Classifier (confidence scores), Information Extractor, Google Gemini (image analysis), If, Edit Fields, Telegram Send and Wait (in-chat approval), Gmail send/reply/get, n8n Agents (Telegram channel, tool approval, memory), n8n MCP server (built and edited with Claude Code).
Services: Google Gemini (3.1 Flash-Lite, 3.5 Flash-Lite, Flash-Lite Latest), Gmail API (OAuth2), Telegram Bot API.

## 8. Hardest part (pick one, say it your way)
- Getting the AI to understand Taglish replies like "babayaran ko sa Friday" and turn them into a real date.
- Making sure a customer who promised but didn't pay still gets chased, and counting it against their promise score.
- Checking GCash screenshots without trusting them: a screenshot is never proof, so the bot only flags problems.

## 9. Demo video
Yes. Planned in `VIDEO_PROMPT.md`: same style as the "Schedule Manager" AVP (white SaaS, brand blue #155DFC, reference-copied motion). Built with Remotion + three.js + Blender. Voice: ElevenLabs Matilda (eleven_v3). Music: ACE-Step (local). Sound effects: ElevenLabs.
