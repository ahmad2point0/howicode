# How I Code

> **[View Full Documentation](https://ahmad2point0.github.io/howicode)**

A collection of my coding practices, folder structure templates, setup guides, and design patterns I use to build scalable applications.

## What's Inside

- **Folder Structures** - Production-ready, feature-based folder structures for different frameworks
- **Design Patterns** - Architecture patterns and principles I follow
- **Setup Guides** - How I set up and configure my projects
- **Code Examples** - Complete, working TypeScript examples

## Folder Structure Templates

| Framework | Status | Description |
|-----------|--------|-------------|
| **React Native Expo** | Available | Mobile apps with Expo Router and file-based routing |
| **Next.js App Router** | Available | Full-stack web applications with App Router |
| **Vite + React** | Coming Soon | Fast development with Vite bundler |
| **Create React App** | Coming Soon | Traditional React single-page applications |
| **React Native CLI** | Coming Soon | Native development without Expo |
| **Remix** | Coming Soon | Full-stack with server-side rendering |

## Core Architecture

All structures follow feature-based architecture:

```
src/
├── app/           # File-based routing (screens/pages)
├── features/      # Feature modules (auth, chat, orders, etc.)
│   ├── auth/
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── services/
│   │   ├── @types/
│   │   └── index.ts
├── global/        # Shared components, hooks, utils, stores
```

## Local Development

```bash
git clone https://github.com/ahmad2point0/howicode.git
cd howicode
bundle install
bundle exec jekyll serve
# Visit http://localhost:4000
```

**Windows users:** See [SETUP.md](SETUP.md) for detailed instructions.

## Contributing

See the [Contributing Guide](https://ahmad2point0.github.io/howicode/contributing.html) for details.

## Links

- [Full Documentation](https://ahmad2point0.github.io/howicode)
- [Issues](https://github.com/ahmad2point0/howicode/issues)
- [Discussions](https://github.com/ahmad2point0/howicode/discussions)
