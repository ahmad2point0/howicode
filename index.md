---
layout: default
title: Home
nav_order: 1
description: "How I code - my structure templates, setup guides, and design patterns"
permalink: /
---

# How I Code
{: .fs-9 }

A collection of my coding practices, folder structure templates, setup guides, and design patterns I use to build scalable applications.
{: .fs-6 .fw-300 }

[View on GitHub](https://github.com/ahmad2point0/howicode){: .btn .btn-primary .fs-5 .mb-4 .mb-md-0 }

---

## What's Inside

{: .highlight }
This site documents how I structure, set up, and architect my projects. It's a living reference that grows as I learn and build.

### Folder Structures

Production-ready folder structures I use across different frameworks, all following feature-based architecture.

| Template | Framework | Description |
|:---------|:----------|:------------|
| [React Native Expo](howicode/react-native-expo.html) | React Native | Mobile apps with Expo Router |
| [Next.js App Router](howicode/nextjs.html) | Next.js | Full-stack web apps with App Router |

### Coming Soon

| Topic | Category | Status |
|:------|:---------|:-------|
| Vite + React Folder Structure | Folder Structures | Coming Soon |
| Create React App Folder Structure | Folder Structures | Coming Soon |
| React Native CLI Folder Structure | Folder Structures | Coming Soon |
| Remix Folder Structure | Folder Structures | Coming Soon |

---

## Design Patterns I Follow

### Feature-Based Architecture

I organize code by business features rather than technical layers. This keeps related code together and makes projects easier to scale and maintain.

```
src/
├── app/           # File-based routing (screens/pages)
├── features/      # Feature modules (auth, chat, etc.)
├── global/        # Shared components & services
```

**Why this works:**

- Related code stays together - components, hooks, services, and types for a feature live in the same folder
- Features are self-contained and don't depend on each other
- Pages/screens are thin wrappers that compose feature components
- Shared code lives in a dedicated `global/` layer

### Key Principles

- **Screens are thin** - routing files only compose feature components, no business logic
- **Features don't cross-import** - shared code goes in `global/`
- **Barrel exports** - each feature exposes a public API via `index.ts`
- **Types stay close** - feature types live inside the feature folder
- **State separation** - Zustand for client state, React Query for server state

---

## Tech Stack

Tools and libraries I commonly use across projects:

| Category | Tools |
|:---------|:------|
| **Languages** | TypeScript, JavaScript |
| **Mobile** | React Native, Expo, Expo Router |
| **Web** | Next.js (App Router), React, Vite |
| **State Management** | Zustand, React Query |
| **Styling** | Tailwind CSS, StyleSheet (RN) |
| **API** | Axios, Server Actions |
| **Backend** | Supabase, Prisma |

---

## Contributing

Want to suggest improvements or add new templates? See the [Contributing Guide](contributing.html).
