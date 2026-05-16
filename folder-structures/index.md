---
layout: default
title: Folder Structures
nav_order: 3
has_children: true
---

# Folder Structures
{: .fs-9 }

Production-ready, feature-based folder structures I use across different frameworks.
{: .fs-6 .fw-300 }

---

## Available Templates

| Template | Framework | Description |
|:---------|:----------|:------------|
| [React Native Expo](react-native-expo.html) | React Native | Mobile apps with Expo Router |
| [Next.js App Router](nextjs.html) | Next.js | Full-stack web apps with App Router |

---

## Core Pattern

All my folder structures follow this proven feature-based pattern:

```
src/
├── app/           # File-based routing (screens/pages)
├── features/      # Feature modules (auth, chat, etc.)
│   ├── auth/
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── services/
│   │   ├── @types/
│   │   └── index.ts
│   └── ...
├── global/        # Shared components & services
│   ├── components/
│   ├── hooks/
│   ├── utils/
│   ├── store/
│   ├── config/
│   └── @types/
```

Each feature is self-contained with its own components, hooks, services, and types. Shared infrastructure lives in `global/`.
