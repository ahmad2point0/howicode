---
layout: default
title: Home
nav_order: 1
description: "How Ahmad codes — folder structures, design patterns, setup guides, and engineering notes for React, React Native, Next.js, and AI apps."
permalink: /
---

<div class="hc-hero" markdown="1">

# How I Code
{: .fs-9 .mb-0 }

<p class="hc-tagline">A living playbook of the structures, patterns, and setup I reach for when I ship products — React, React Native, Next.js, and AI agentic systems.</p>

<div class="hc-chips">
  <span class="hc-chip">TypeScript</span>
  <span class="hc-chip">React Native + Expo</span>
  <span class="hc-chip">Next.js</span>
  <span class="hc-chip purple">FastAPI · NestJS</span>
  <span class="hc-chip green">Zustand · React Query</span>
  <span class="hc-chip amber">OpenAI · Claude · RAG</span>
</div>

<p class="hc-meta">Maintained by <a href="https://ahmad2point0.com" target="_blank" rel="noopener">Muhammad Ahmad Arshad</a> — Software Engineer @ Obsidian Code · Lahore, PK (GMT+5)</p>

[Personal site](https://ahmad2point0.com){: .btn .btn-primary .fs-5 .mb-2 .mb-md-0 .mr-2 }
[GitHub](https://github.com/ahmad2point0){: .btn .fs-5 .mb-2 .mb-md-0 .mr-2 }
[Medium](https://ahmad2point0.medium.com){: .btn .fs-5 .mb-2 .mb-md-0 }

</div>

<div class="hc-narrow" markdown="1">

## About this site
{: .no_toc }

I’ve been shipping production apps long enough to have strong opinions about how a codebase should feel. This site is where I keep those opinions: the folder structures I default to, the patterns I trust, the setup steps I’d copy into every new repo. It’s written for the version of me starting a new project at 9am on a Monday.

If you read one thing first, read **[Code I Prefer](code-preferences/)** — it’s the short version of how I think about every line.

---

<p class="hc-section-label">Start here</p>

<div class="hc-grid">
<a class="hc-card" href="code-preferences/"><span class="hc-card-tag">Opinions</span><span class="hc-card-title">Code I Prefer →</span><p>Naming, structure, state, styling, error handling — the defaults I apply without thinking.</p></a>
<a class="hc-card" href="folder-structures/"><span class="hc-card-tag">Templates</span><span class="hc-card-title">Folder Structures →</span><p>Feature-based layouts for Expo and Next.js App Router, ready to copy into a new project.</p></a>
<a class="hc-card" href="blogs/"><span class="hc-card-tag">Writing</span><span class="hc-card-title">Engineering Notes →</span><p>Long-form pieces — starting with automatic JWT refresh tokens via Axios interceptors.</p></a>
</div>

---

## What I focus on

<div class="hc-grid">
<div class="hc-card"><span class="hc-card-tag">AI</span><span class="hc-card-title">Agentic systems</span><p>Multi-agent orchestration, chat UIs, RAG over private corpora, prompt engineering across OpenAI, Claude, and Groq.</p></div>
<div class="hc-card"><span class="hc-card-tag">Mobile</span><span class="hc-card-title">Cross-platform apps</span><p>Expo + Expo Router for app stores and web. File-based routing, native modules when needed, fast OTA updates.</p></div>
<div class="hc-card"><span class="hc-card-tag">SaaS</span><span class="hc-card-title">Full-stack products</span><p>Next.js App Router on the front, FastAPI / NestJS on the back. Stripe, multi-tenant, billing-aware from day one.</p></div>
<div class="hc-card"><span class="hc-card-tag">Integrations</span><span class="hc-card-title">Real-world plumbing</span><p>HubSpot, Salesforce, Shopify, WebRTC, NFC — the boring connectors that make products useful.</p></div>
</div>

---

## The pattern behind everything

{: .highlight }
I organize code by **business features**, not technical layers. Related code lives together. Screens are thin. Features don’t cross-import. State has clear ownership.

```text
src/
├── app/           # File-based routing (screens / pages)
├── features/      # Feature modules (auth, chat, orders, …)
│   └── auth/
│       ├── components/
│       ├── hooks/
│       ├── services/
│       ├── @types/
│       └── index.ts        # Public API (barrel export)
├── global/        # Shared components, hooks, utils, stores
```

The full breakdown lives in [Folder Structures](folder-structures/), with concrete templates for [React Native Expo](folder-structures/react-native-expo.html) and [Next.js App Router](folder-structures/nextjs.html).

---

## Default tech stack

| Layer | What I reach for first |
|:------|:------------------------|
| **Language** | TypeScript (strict mode, always) |
| **Mobile** | React Native · Expo · Expo Router |
| **Web** | Next.js (App Router) · React · Vite for SPAs |
| **Client state** | Zustand |
| **Server state** | TanStack Query (React Query) |
| **Styling** | Tailwind CSS on web · StyleSheet / NativeWind on RN |
| **HTTP** | Axios with interceptors (see the [JWT refresh notes](blogs/jwt-refresh-tokens-axios.html)) |
| **Backend** | FastAPI · NestJS · Node.js |
| **Data** | Supabase · Prisma · Postgres |
| **Payments** | Stripe |
| **AI** | OpenAI · Claude · Groq · RAG · vector retrieval |

---

## Track record

A few numbers from real client work — included as a sanity check that these patterns survive contact with production:

- **50%** engagement lift after shipping an AI chat layer into an existing app
- **40%** faster order processing in a WhatsApp-integrated commerce flow
- **35%** performance gain on an Expo SDK migration
- **100%** client satisfaction rating on Upwork across multi-quarter engagements

More context on the projects behind these numbers lives on [ahmad2point0.com](https://ahmad2point0.com).

---

## Contributing

Spotted something that could be sharper? Disagree with a pattern? Open an issue or a PR — see the [Contributing Guide](contributing.html).

</div>
