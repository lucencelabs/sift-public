<div align="center">
  <img src="https://github.com/user-attachments/assets/f54b3225-4e82-4bad-9edc-425f07fad56e" alt="Sift" width="200" />
  <h1>Sift</h1>
  <p><strong>Your life, on autopilot.</strong></p>
  <p>
    <a href="https://app.usesift.app">Web App</a> &middot;
    <a href="https://usesift.app">Landing Page</a> &middot;
    <a href="https://usesift.app/pricing">Pricing</a> &middot;
    <a href="https://docs.usesift.app">User Docs</a>
  </p>
</div>

---

> Text Sift: (303) 994 9654
> 
> Email Sift: sift@usesift.app

Sift connects to Canvas, your calendar, and your email — then keeps you on top of all of it. It texts you about the deadline you forgot, tells you what you need on the final to keep your grade, and builds your schedule around what's actually on your plate. Morning briefings, deadline reminders, study plans, grade tracking — all running in the background without you lifting a finger.

Text it from iMessage. Ask it anything from the web app. It plans, schedules, and follows up — so you don't have to.

## How It Works

- **Syncs everything** — Canvas assignments, Google Calendar, Gmail, Slack, and Notion stay current automatically. You never manually enter anything.
- **Acts on its own** — describe what you want in plain English ("text me every morning with my schedule," "warn me 3 days before any exam") and Sift turns it into a real automation that runs on a schedule.
- **Learns your context** — Sift builds a persistent understanding of your classes, commitments, habits, and goals. Every interaction gets smarter because it remembers what matters to you.
- **Lives where you are** — iMessage, SMS, or web. Same AI, same memory, every channel.

## Under the Hood

### Native iMessage Relay

Sift lives in iMessage through a custom Mac Mini relay — not a third-party wrapper or API bridge. Messages arrive as native iMessages with natural chunked delivery pacing, tapback reactions, Gemini Vision for photo attachments, and per-user privacy isolation in group chats. If the relay is unreachable, it degrades gracefully to SMS via SignalWire. This is the core product bet: the best assistant is the one you already have open.

### Self-Improving Classifier

Every inbound signal — emails, calendar events, Canvas updates — passes through a 3-tier classification pipeline: keyword hash lookup (~1ms), embedding similarity via pgvector (~10ms), then Gemini Flash LLM (~500ms) only if the first two tiers miss. High-confidence results feed back into the cache, so repeated patterns get faster over time. The system learns what matters to each user without any manual configuration.

### Browser Sidecar

A Playwright-based microservice deployed on its own Fly.io instance lets the AI agent browse the web on behalf of users — logging into Canvas portals, extracting course content, filling forms, and scraping authenticated pages. Each user gets a persistent Chromium profile so login state survives across sessions. Credentials are injected directly into the browser and never exposed to the AI agent.

### Multi-Agent Orchestration

A central orchestrator delegates to 15 specialized sub-agents (scheduling, email, memory, tasks, notes, browser, and more), each with isolated tool access and domain-specific prompts. 139 tools are auto-registered at class definition time. The agent runs an agentic loop with mid-processing steer signals — if you send a follow-up while it's still thinking, your message gets injected into the current iteration via Redis rather than waiting for the next turn.

### Architecture

```
Web App (React 19)  ·  iMessage  ·  SMS  ·  Automations
                          │
                    FastAPI Backend
                          │
              ┌───────────┼───────────┐
              │           │           │
        Orchestrator   Workers    Middleware
        Agent (LLM)   (19 jobs)  (11 layers)
              │
        15 Sub-Agents
        139 Tools
              │
    ┌─────────┼─────────┐─────────┐
    │         │         │         │
 Supabase   Redis    External   Browser
 (Postgres, (Cache,  (Canvas,   Sidecar
  pgvector, Locks,   Google,    (Playwright,
  Auth)     Queues)  Slack,     Fly.io)
                     Notion,
                     Stripe)
```

278K lines of Python backend. 100K lines of React frontend. 19 background workers handling everything from 30-second calendar syncs to weekly behavioral snapshots. The whole thing runs on Fly.io, a Mac Mini, and Supabase.

## Integrations

| Category | Services |
|----------|----------|
| **LMS** | Canvas (courses, assignments, grades, announcements, syllabus parsing) |
| **Calendar** | Google Calendar (OAuth + push notifications), Outlook, ICS feeds |
| **Email** | Gmail (OAuth + Pub/Sub push), Outlook |
| **Messaging** | iMessage (custom relay), SMS (SignalWire) |
| **Productivity** | Notion, Google Drive, Slack, GitHub |
| **Payments** | Stripe, RevenueCat, Apple IAP |
| **AI** | Claude (Anthropic), GPT-4o (OpenAI), Gemini 2.5 Flash (Google) |
| **Browser** | Playwright sidecar (Chromium, persistent profiles, Fly.io) |

## Tech Stack

| Layer | Technology |
|-------|-----------|
| **Backend** | Python 3.12, FastAPI, Uvicorn |
| **Database** | Supabase (PostgreSQL + pgvector + RLS + Auth) |
| **Cache** | Redis (Upstash) — caching, distributed locks, message queues |
| **Web** | React 19, Vite, TypeScript, TailwindCSS, Radix UI |
| **Real-time** | Socket.IO (WebSocket + polling fallback) |
| **Browser** | Playwright sidecar (Chromium, Fly.io) |
| **Messaging** | Custom Mac Mini relay (iMessage), SignalWire (SMS) |
| **Security** | AES-256-GCM encryption, HMAC signing, 11 middleware layers |
| **Deployment** | Fly.io, Vercel, Supabase Cloud, Upstash |

## About

Sift is built by **[Lucence Labs](https://lucence.so)** — a Boulder-based company focused on AI systems that do real work, not just answer questions. 

<div align="center">
  <sub>Sift is closed-source. Source code available upon request for recruiting purposes — conner@connergroth.com</sub>
</div>
