# जन-शक्ति (Jan-Shakti)

<div align="center">

**People's Power — Citizen Services Platform for Every Indian**

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Status: Ideation](https://img.shields.io/badge/Status-Ideation-yellow.svg)](#roadmap)

*A unified pipeline from citizen need → government service resolution.*  
*Lightweight. Fast. Built for a billion.*

</div>

---

## The Problem

India has 1.4 billion citizens and thousands of government services — but the gap between a citizen and the service they need is massive:

| Friction Point | Reality |
|---|---|
| **Discovery** | Most Indians don't know which schemes, documents, or services they're eligible for |
| **Access** | Government portals are fragmented across states, languages, and departments |
| **Complexity** | A single registration (land, business, identity) often requires visits to 3-4 offices |
| **Language** | Services default to English; 90% of Indians don't speak it as a first language |
| **Intermediaries** | The knowledge gap creates a parasitic middleman economy (agents, touts, "consultants") |

**Jan-Shakti** closes this gap. It's a single, intelligent pipeline that takes a citizen from *"I need X"* to *"X is done"* — in their language, on their device, without an intermediary.

---

## What Jan-Shakti Does

Jan-Shakti is a **modular citizen services pipeline** — think of it as an OS for interacting with government:

```
┌─────────────────────────────────────────────────────────┐
│                    JAN-SHAKTI PIPELINE                     │
├───────────┬───────────┬──────────┬───────────┬───────────┤
│  DISCOVER │  PREPARE  │  SUBMIT  │   TRACK   │  RESOLVE  │
│           │           │          │           │           │
│ What am I │ Documents │ Digital  │ Real-time │ Certificate│
│ eligible  │ needed,   │ filing   │ status,   │ delivered, │
│ for?      │ forms,    │ to govt  │ SMS alerts│ verified   │
│           │ checklists│ portals  │           │            │
└───────────┴───────────┴──────────┴───────────┴───────────┘
```

### Initial Service Categories

| Category | Examples |
|---|---|
| 🆔 **Identity & Civil** | Aadhaar updates, voter ID, birth/death certificates, caste/income certificates |
| 🏠 **Land & Property** | Land record mutations, property registration, encumbrance certificates, tax payments |
| 🏢 **Business & Licenses** | Udyam MSME registration, GST registration, trade licenses, FSSAI, shop & establishment |
| 💰 **Government Schemes** | PMAY housing, PM-KISAN, scholarships, pension schemes, subsidy discovery |
| 📄 **Document Services** | Affidavit generation, notarization routing, document translation, attestation tracking |
| ⚖️ **Grievance & RTI** | Public grievance filing, RTI request drafting and submission, status tracking |

---

## Architecture Philosophy

### Design Principles

1. **Billion-Scale, Start Small** — Every component is designed for horizontal scaling, but the MVP runs on a single $5 VPS
2. **Offline-First Where Possible** — India has connectivity gaps. Forms and checklists work offline; sync when connected
3. **Language-Native** — Every interaction starts in the citizen's language (22 scheduled languages). English is optional, not default
4. **No Intermediary Required** — The platform itself is the guide. AI-powered assistance replaces the middleman
5. **Government API Agnostic** — Wraps existing portals via APIs where available, falls back to structured form-filling guidance where not

### High-Level Architecture

```
                    ┌──────────────┐
                    │   Nginx/CDN  │
                    └──────┬───────┘
                           │
              ┌────────────┼────────────┐
              │                         │
     ┌────────▼────────┐     ┌──────────▼──────────┐
     │   API Gateway    │     │   Static Frontend   │
     │   (FastAPI)      │     │   (Svelte/SolidJS)  │
     │   Async, WSGI    │     │   < 50KB gzip       │
     └────────┬────────┘     └─────────────────────┘
              │
    ┌─────────┼─────────┬──────────────┬────────────┐
    │         │         │              │            │
┌───▼───┐ ┌──▼──┐ ┌────▼────┐ ┌───────▼──────┐ ┌───▼───┐
│Citizen│ │Doc  │ │Scheme   │ │Submission   │ │Notif  │
│Engine │ │Engine│ │Engine   │ │Engine       │ │Engine │
│       │ │     │ │         │ │             │ │       │
│Profile│ │OCR  │ │Eligib.  │ │Govt Portal  │ │SMS    │
│+Prefs │ │Templ.│ │Matching │ │Integration  │ │Email  │
│       │ │     │ │         │ │             │ │WhatsApp│
└───┬───┘ └──┬──┘ └────┬────┘ └──────┬──────┘ └───┬───┘
    │        │          │             │            │
    └────────┴──────────┴─────────────┴────────────┘
                           │
                    ┌──────▼──────┐
                    │  PostgreSQL │
                    │  + Redis    │
                    │  + S3/MinIO │
                    └─────────────┘
```

### Tech Stack Recommendation

> **This is the proposed stack — subject to iteration.**

| Layer | Technology | Why |
|---|---|---|
| **Backend** | Python / FastAPI | Async-native, excellent concurrency, familiar ecosystem (VAAYU, FlipKit use it) |
| **Worker Queue** | ARQ (async-rq) or Celery | Handles long-running govt portal submissions, document processing |
| **Database** | PostgreSQL + Redis | Start with SQLite for single-node dev; scale to Postgres with read replicas |
| **Frontend** | Svelte or SolidJS | < 50KB gzip, no virtual DOM overhead, works on low-end devices (₹5K Android phones) |
| **Mobile** | PWA wrapper | No Play Store dependency; updates instantly; works offline |
| **AI/ML** | LangGraph + local models | Form-filling guidance, eligibility matching, document OCR, language translation |
| **File Storage** | MinIO (S3-compatible) | Self-hosted document storage; migrate to cloud S3 for scale |
| **Caching** | Redis | Session state, rate limiting, frequently accessed scheme data |
| **Monitoring** | Prometheus + Grafana | Open-source, battle-tested for India-scale deployments |

### Why This Stack?

- **Lightweight**: FastAPI + Svelte can serve 10K+ req/s on a single $20 VPS
- **Concurrent**: Python `asyncio` + worker queues handle parallel submissions to multiple government portals simultaneously
- **Scales**: Every component is horizontally scalable — add more API workers, read replicas, Redis clusters as needed
- **Low Ops Burden**: Minimal moving parts initially. No Kubernetes until 100K+ users
- **Shared DNA**: Same stack family as [VAAYU](https://github.com/sumit1612/vaayu-platform) (AI sales agent) and [FlipKit](https://github.com/sumit1612/flipkit) (music production) — shared libraries, patterns, and learnings

---

## Related Projects in the Ecosystem

Jan-Shakti is part of a larger vision to build AI-powered platforms for India:

| Project | Tagline | Status |
|---|---|---|
| **[VAAYU](https://github.com/sumit1612/vaayu-platform)** | AI Sales Agent for Real Estate — WhatsApp-first, LangGraph-powered | 🟢 Active |
| **[FlipKit](https://github.com/sumit1612/flipkit)** | URL → Stems + MIDI. AI-powered sample flipping | 🟢 Active |
| **Jan-Shakti** | Citizen Services Pipeline for Every Indian | 🟡 Ideation |

---

## Getting Started

> ⚠️ **Jan-Shakti is in the ideation phase.** This section will be populated as the MVP takes shape.

```bash
# Coming soon
git clone https://github.com/sumit1612/jan-shakti.git
cd jan-shakti
cp .env.example .env
# ...
```

---

## Roadmap

### Phase 1: Foundation (MVP)
- [ ] Project scaffolding (FastAPI + Svelte + PostgreSQL)
- [ ] Citizen profile & preference engine (language, location, service history)
- [ ] Single service pipeline end-to-end (e.g., Udyam MSME registration)
- [ ] Multi-language UI (Hindi + English initially)
- [ ] SMS notification integration

### Phase 2: Expansion
- [ ] Document OCR & template engine (Aadhaar, PAN, address proof parsing)
- [ ] Scheme discovery engine (eligibility matching across 100+ central schemes)
- [ ] 5 additional service pipelines (land records, caste certificate, etc.)
- [ ] PWA offline support
- [ ] WhatsApp chatbot interface

### Phase 3: Scale
- [ ] 10 Indian languages supported
- [ ] State-specific service catalog (starting with 3 states)
- [ ] Government department API integrations (DigiLocker, UMANG, state portals)
- [ ] Partner API for NGOs, Common Service Centers (CSCs), gram panchayats
- [ ] Analytics dashboard for service delivery metrics

### Phase 4: Platform
- [ ] Open service pipeline SDK — third parties can contribute new service integrations
- [ ] Federation model — state governments can host their own Jan-Shakti nodes
- [ ] AI-powered document drafting (GPT-assisted affidavit, application letter generation)
- [ ] Voice-first interface for low-literacy users

---

## Contributing

Jan-Shakti is in its earliest stages. If you're excited about building citizen infrastructure for India:

1. **Watch this repo** — star and watch for updates
2. **Ideas & Feedback** — open a [Discussion](https://github.com/sumit1612/jan-shakti/discussions)
3. **Code** — once the MVP scaffold is up, contribution guidelines will be posted

---

## License

MIT © [Sumit Patel](https://github.com/sumit1612)

---

<div align="center">

**जन-शक्ति — क्योंकि शक्ति जनता में है।**  
*Jan-Shakti — because the power is in the people.*

</div>
