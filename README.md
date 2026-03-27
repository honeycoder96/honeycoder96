<div align="center">

# Honey Sharma

**Lead Engineer · Infoedge Ltd · Noida, India**

_Building systems where thoughtful architecture meets product engineering_

[![Portfolio](https://img.shields.io/badge/Portfolio-honeyhimself.com-0a0a0a?style=flat-square)](https://honeyhimself.com)
[![Blog](https://img.shields.io/badge/Blog-blog.honeyhimself.com-0a0a0a?style=flat-square)](https://blog.honeyhimself.com)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-honeycoder96-0077B5?style=flat-square&logo=linkedin)](https://www.linkedin.com/in/honeycoder96)
[![Email](https://img.shields.io/badge/Email-contact@honeyhimself.com-EA4335?style=flat-square&logo=gmail)](mailto:contact@honeyhimself.com)

</div>

---

## About

6+ years building production web systems — currently at **Infoedge Ltd**, where I work on large-scale platforms serving millions of users across India's leading job, real estate, and matrimony verticals.

Outside work I build things that scratch my own engineering itch: hiring infrastructure, in-browser AI tooling, session observability, and whatever else requires thinking carefully about the right abstraction level.

**What I care about:**

- Designing for _operational correctness_ first — rate limiting, idempotency, non-blocking side effects, and graceful degradation aren't afterthoughts, they're the design
- Keeping accidental complexity low. The right data model removes entire feature categories of complexity in the application layer
- Shipping full vertical slices — schema → controller → queue worker → email template → UI component — solo and fast

---

## Engineering Philosophy

**On backend design:** Controllers are thin. Business logic lives in services. Zod validates at the boundary. Mongoose lean() on lists, populate() on single-doc reads. Every side-effect that can fail independently (email, DB audit) is non-blocking with logged fallback.

**On queues over synchronous calls:** Anything that touches an external API goes through BullMQ. The HTTP response is decoupled from delivery. Retry, dead-letter, and backpressure come for free.

**On frontend architecture:** Services own all API contracts and types. Components consume services; they don't know about HTTP. Splitting data-fetching from rendering makes both testable and portable.

**On real-time:** Yjs CRDTs over raw socket events for shared state. Operational transforms are a solved problem — don't reinvent them. Use the right primitive (CRDT for documents, Socket.IO for presence/signals, WebRTC for peer media).

**On AI integration:** AI is a pipeline stage, not a product. Wrap it in a service, make the output schema explicit with Zod, and treat failures as soft-degraded states — not exceptions.

---

## Projects

### TalentSync — End-to-end Hiring Platform
> _The main thing. A full recruiting workflow from job posting to candidate decision._

A multi-tenant SaaS platform covering the complete hiring lifecycle: job openings with configurable stage pipelines, automated async screening (AI-evaluated), live 1:1 video interviews with collaborative tools, recruiter feedback gates, and dual-facing dashboards for both recruiter and candidate.

**Architecture highlights:**
- **13-collection MongoDB schema** with denormalized plan limits on Organization to avoid join-on-read for quota checks
- **Candidate pipeline** as single source of truth — dual status (internal recruiter view + candidate-facing) decoupled so each party sees appropriate information
- **StageTypeConfig collection** drives all frontend dropdowns — zero hardcoded stage types anywhere in the UI
- **BullMQ email queue** with Resend + React Email for all transactional emails — screening invitations, interview calendaring (ICS attachments), decline flows, AI report notifications
- **Screening evaluation service** using Claude (Anthropic SDK) — async, post-submit, result stored back to pipeline
- **Yjs + Socket.IO** for real-time collaborative code editor and whiteboard (tldraw) inside live interview rooms
- **Privacy projection layer** strips AI scores, tokens, and internal notes before sending any data to candidate-facing endpoints
- **Firebase Auth** with role-aware middleware — `attachOrgContext()` fast-paths org lookup from `user.organizationId`

**Stack:** Express · TypeScript · MongoDB/Mongoose · Zod · BullMQ · Redis · Socket.IO · Yjs · tldraw · Firebase Admin · Resend · @anthropic-ai/sdk · React · Vite · shadcn/ui · CodeMirror · Excalidraw

| | |
|---|---|
| Backend repo | talentsyncAPI - Currently private |
| Frontend repo | talentsync - - Currently private |
| Live | [app.talentsync.io](https://talentsync.honeyhimself.com) |

---

### WebGPU Local Code Reviewer
> _AI code review that runs entirely in the browser. No server. No data leaves the machine._

Paste a `git diff`, get structured review feedback from 4 specialized AI agents — Bug Detection, Security Analysis, Performance, and Summary — all running client-side via WebLLM on the GPU.

**Architecture highlights:**
- **Web Worker** parses and chunks the diff off the main thread so the UI stays responsive on large PRs
- **4-agent pipeline** with independent role prompts but cumulative shared memory — each agent builds on prior context without re-running inference from scratch
- **Risk scoring** (0–10), security findings panel, suggested commit message, test case generation
- **Zustand** for persistent UI state across sessions (agent toggles, focus context, severity filters)
- Zero backend — deployment is a static file

**Stack:** React · WebLLM (@mlc-ai/web-llm) · Web Workers · Zustand · parse-diff · Vite

| | |
|---|---|
| Repo | [webgpu-llm](https://github.com/honeycoder96/clientside-codereviewer) |
| Live | [review.honeyhimself.com](https://webllm.honeyhimself.com) |

---

### SessionsSmith — Session Recording & Replay Platform
> _rrweb-based observability. Embed a script, replay user sessions with full fidelity._

Website owners register their domain, receive a script key, and embed a tracking snippet. Sessions (mouse movement, clicks, scrolls, DOM mutations, console output) are streamed and stored server-side, then replayed frame-perfect in a dashboard — including console log playback synced to the timeline.

**Architecture highlights:**
- **rrweb** for DOM serialization and event capture — battle-tested, mutation-observer-based, minimal overhead
- **Script-key-based multi-tenancy** — one platform, many sites, isolated session data
- **Caddy reverse proxy** with automatic TLS for production deployment
- Console output captured as structured events and replayed in sync with the session timeline

**Stack:** Node.js · rrweb · Docker · Caddy · Nginx · Vanilla JS (embed)

| | |
|---|---|
| Repo | [SessionsSmith](https://github.com/honeycoder96/SessionsSmith) |
| Live | [record.honeyhimself.com](https://record.honeyhimself.com) |

---

### AI GitHub Code Review Bot
> _GitHub webhook integration for automated PR review using Genkit + Gemini._

A GitHub App that listens to pull request events, fetches the diff, runs it through a Genkit AI pipeline (Google Gemini), and posts structured review comments back to the PR thread.

**Stack:** Node.js · TypeScript · Genkit · Google Gemini · GitHub Webhooks

| | |
|---|---|
| Repo | [patchpilot](https://github.com/honeycoder96/PatchPilot) |
| Live | [patchpilot.honeyhimself.com](https://patchpilot.honeyhimself.com/) |
| Github app link | [patchpilothub](https://github.com/apps/patchpilothub) |
---

### VoiceFill — Speech-to-Text Chrome Extension
> _Browser extension that fills any input field with your voice._

Lightweight Chrome extension that attaches a microphone button to focused input fields and textareas. Uses the Web Speech API for transcription with a minimal UI — no backend, no storage.

**Stack:** Chrome Extensions API · Web Speech API · Vanilla JS

| | |
|---|---|
| Repo | [speechtotext](#) |

---

### Personal Site & Blog
> _The public surface area._

Portfolio built with React + Framer Motion + Spline 3D — scroll-triggered animations, kinetic typography, WebGL backgrounds. Blog built with Astro + MDX — static, fast, with live Sandpack code playgrounds embedded in posts.

| | |
|---|---|
| Portfolio | [honeyhimself.com](https://honeyhimself.com) |
| Blog | [blog.honeyhimself.com](https://blog.honeyhimself.com) |

---

## Tech Stack

```
Languages      TypeScript · JavaScript · SQL
Runtime        Node.js · Browser (WebGPU, Web Workers, Web Speech API)
Backend        Express · Zod · BullMQ · Socket.IO · Yjs · Firebase Admin
Database       MongoDB/Mongoose · Redis
AI/ML          Anthropic Claude · Google Gemini · Genkit · WebLLM (on-device)
Frontend       React · Vite · Tailwind CSS · shadcn/ui · Framer Motion
Real-time      Socket.IO · WebRTC · Y.js CRDTs
Email          Resend · React Email
Infra          Docker · Caddy · Nginx · GitHub Actions
Observability  Winston · BullMQ dashboards · rrweb session replay
```

---

## Currently

- Shipping Phase 3 of TalentSync (live interview scheduling + recruiter tooling)
- Exploring vector search and embedding pipelines for candidate matching
- Writing about system design and architecture decisions at [blog.honeyhimself.com](https://blog.honeyhimself.com)

---

<div align="center">

_Based in Noida, India · Available for consulting and collaboration_

</div>
