# Real-Time AI Inbound Lead Triage Engine (n8n + Supabase + Gemini + Gmail API)

Production-grade, **Human-in-the-Loop** B2B lead qualification pipeline. Processes incoming web leads in under 5 seconds—eliminating manual data entry while preventing unchecked AI emails from hitting high-value prospects.

## Architecture Flow

```text
[Webhook Ingestion]
        │
        ▼
[Custom JavaScript Node] ──► Regex HTML cleanup, domain check, deterministic hash
        │
        ▼
[Gemini AI + Structured Output Parser] ──► Strict JSON (intent, 0-100 score, urgency, pain point, reply)
        │
        ▼
[Supabase PostgreSQL] ──► Idempotent INSERT (UNIQUE constraint on message_hash)
        │
        ▼
[IF Gate: lead_score >= 70]
        │ (true)
        ▼
[Gmail API (Google Cloud OAuth2)] ──► Auto-creates personalized Draft ready for 1-click sales review
```

## Engineering Highlights

* **Zero Duplicate Records (SQL Idempotency):** Generates a deterministic hash (`FNV-1a`) from the sender and sanitized message, enforced at the database layer via a PostgreSQL `UNIQUE` constraint (`schema.sql`).
* **Custom JS Data Sanitization:** Strips raw HTML tags and normalizes whitespace via Regex before sending payloads to the LLM, saving tokens and preventing prompt clutter.
* **Strict Schema Enforcement:** Uses a Structured Output Parser to guarantee integer scoring (`0–100`) and strict enum values—zero broken downstream nodes.
* **Human-in-the-Loop Safety:** High-intent leads automatically generate personalized replies directly inside Gmail Drafts via custom Google Cloud OAuth2 credentials.

## Tech Stack

* **Orchestration:** n8n (self-hosted / local)
* **Database:** Supabase (PostgreSQL)
* **AI Model:** Google Gemini + Structured Output Parser
* **Integration:** Gmail API via Google Cloud OAuth2
* **Custom Logic:** JavaScript (ES6)

## Quick Setup

1. Execute `schema.sql` inside your Supabase SQL Editor to create the `inbound_leads` table.
2. Import `workflow.json` into your n8n instance.
3. Configure your own credentials for **Google Gemini**, **Supabase**, and **Gmail OAuth2** (Google Cloud Console).
4. Point your web form or CRM webhook to the Webhook trigger URL.

---

**Built by Mateusz Piekarski** — AI Automation Architect  
[Portfolio & Services (Contra)](https://contra.com/mateuszpiekarskiai) | [LinkedIn](https://www.linkedin.com/in/mateusz-piekarski-ai)
