---
layout: default
title: Next.js App Router
parent: Folder Structures
nav_order: 2
---

# Next.js App Router Folder Structure
{: .no_toc }

A feature-based folder structure for Next.js applications using the App Router, Server Components, and TypeScript.
{: .fs-6 .fw-300 }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Project Structure Overview

```
project-root/
│
├── public/                                # Static files served at /
│   ├── images/
│   │   └── logo.png
│   ├── favicon.ico
│   └── robots.txt
│
├── src/
│   ├── app/                               # App Router (file-based routing)
│   │   ├── (auth)/
│   │   │   ├── login/
│   │   │   │   └── page.tsx
│   │   │   ├── register/
│   │   │   │   └── page.tsx
│   │   │   └── layout.tsx
│   │   ├── (main)/
│   │   │   ├── page.tsx                   # Home page
│   │   │   ├── profile/
│   │   │   │   └── page.tsx
│   │   │   └── layout.tsx
│   │   ├── orders/
│   │   │   ├── page.tsx                   # Orders list
│   │   │   └── [id]/
│   │   │       └── page.tsx               # Order details
│   │   ├── chats/
│   │   │   └── [id]/
│   │   │       └── page.tsx
│   │   ├── api/                           # API Route Handlers
│   │   │   ├── auth/
│   │   │   │   └── route.ts
│   │   │   └── orders/
│   │   │       └── route.ts
│   │   ├── layout.tsx                     # Root layout
│   │   ├── loading.tsx                    # Global loading UI
│   │   ├── error.tsx                      # Global error UI
│   │   └── not-found.tsx                  # 404 page
│   │
│   ├── features/                          # Business logic modules
│   │   ├── auth/
│   │   │   ├── components/
│   │   │   │   ├── LoginForm.tsx
│   │   │   │   └── RegisterForm.tsx
│   │   │   ├── hooks/
│   │   │   │   ├── useLogin.ts
│   │   │   │   └── useRegister.ts
│   │   │   ├── services/
│   │   │   │   └── auth.service.ts
│   │   │   ├── actions/
│   │   │   │   └── auth.action.ts         # Server Actions
│   │   │   ├── utils/
│   │   │   │   └── authValidator.ts
│   │   │   ├── @types/
│   │   │   │   ├── auth.types.ts
│   │   │   │   ├── auth.dto.ts
│   │   │   │   ├── auth.response.ts
│   │   │   │   └── index.ts
│   │   │   └── index.ts
│   │   ├── chat/
│   │   │   ├── components/
│   │   │   │   ├── ChatBubble.tsx
│   │   │   │   └── ChatInput.tsx
│   │   │   ├── hooks/
│   │   │   │   └── useChat.ts
│   │   │   ├── services/
│   │   │   │   └── chat.service.ts
│   │   │   ├── @types/
│   │   │   │   ├── chat.types.ts
│   │   │   │   ├── message.types.ts
│   │   │   │   └── index.ts
│   │   │   └── index.ts
│   │   ├── orders/
│   │   │   ├── components/
│   │   │   │   ├── OrderCard.tsx
│   │   │   │   └── OrderStatusBadge.tsx
│   │   │   ├── hooks/
│   │   │   │   └── useOrders.ts
│   │   │   ├── services/
│   │   │   │   └── order.service.ts
│   │   │   ├── actions/
│   │   │   │   └── order.action.ts
│   │   │   ├── @types/
│   │   │   │   ├── order.types.ts
│   │   │   │   ├── order.status.ts
│   │   │   │   └── index.ts
│   │   │   └── index.ts
│   │   └── home/
│   │       ├── components/
│   │       │   └── HomeHeader.tsx
│   │       ├── hooks/
│   │       │   └── useHome.ts
│   │       ├── services/
│   │       │   └── home.service.ts
│   │       ├── @types/
│   │       │   ├── home.types.ts
│   │       │   └── index.ts
│   │       └── index.ts
│   │
│   ├── global/                            # Shared app infrastructure
│   │   ├── components/
│   │   │   ├── ui/
│   │   │   │   ├── Button.tsx
│   │   │   │   ├── Input.tsx
│   │   │   │   └── Card.tsx
│   │   │   └── shared/
│   │   │       ├── AppHeader.tsx
│   │   │       ├── Footer.tsx
│   │   │       └── Loader.tsx
│   │   ├── hooks/
│   │   │   ├── useTheme.ts
│   │   │   └── useDebounce.ts
│   │   ├── utils/
│   │   │   ├── formatDate.ts
│   │   │   ├── validateEmail.ts
│   │   │   └── cn.ts                      # className utility
│   │   ├── constants/
│   │   │   ├── colors.ts
│   │   │   ├── strings.ts
│   │   │   └── endpoints.ts
│   │   ├── store/
│   │   │   ├── auth.store.ts
│   │   │   └── theme.store.ts
│   │   ├── lib/
│   │   │   ├── prisma.ts                  # Database client
│   │   │   ├── supabase.ts
│   │   │   └── analytics.ts
│   │   ├── config/
│   │   │   ├── site.ts
│   │   │   ├── env.ts
│   │   │   └── apiClient.ts
│   │   ├── @types/
│   │   │   ├── api.types.ts
│   │   │   ├── common.types.ts
│   │   │   └── index.ts
│   │   └── index.ts
│   │
│   └── styles/
│       └── globals.css
│
├── tsconfig.json
├── package.json
├── next.config.ts
├── tailwind.config.ts
└── .env.local
```

---

## Architecture Layers

{: .highlight }
The structure separates concerns into three clear layers: **Routing**, **Business Logic**, and **Shared Infrastructure**.

| Layer | Directory | Purpose |
|:------|:----------|:--------|
| **Routing** | `app/` | Pages, layouts, API routes — thin wrappers that compose feature components |
| **Business** | `features/` | Self-contained modules with components, hooks, services, actions, and types |
| **Shared** | `global/` | Reusable UI, utilities, stores, and configuration |

---

## Routing Layer

### Pages (Server Components by Default)

```typescript
// app/orders/page.tsx — server component, fetches data directly
import { OrderCard } from '@/features/orders';
import { orderService } from '@/features/orders/services/order.service';

export default async function OrdersPage() {
  const orders = await orderService.getAll();

  return (
    <main className="container mx-auto p-6">
      <h1 className="text-2xl font-bold mb-4">Orders</h1>
      {orders.map((order) => (
        <OrderCard key={order.id} order={order} />
      ))}
    </main>
  );
}
```

### Route Groups

Parentheses `()` create logical groups without affecting the URL:

```typescript
// app/(auth)/login/page.tsx — screen is just a thin wrapper
import { LoginForm } from '@/features/auth';

export default function LoginPage() {
  return (
    <main className="flex min-h-screen items-center justify-center">
      <LoginForm />
    </main>
  );
}
```

### Layouts

```typescript
// app/(auth)/layout.tsx
export default function AuthLayout({ children }: { children: React.ReactNode }) {
  return (
    <div className="flex min-h-screen items-center justify-center bg-gray-50">
      <div className="w-full max-w-md">{children}</div>
    </div>
  );
}
```

### Dynamic Routes

```typescript
// app/orders/[id]/page.tsx
import { orderService } from '@/features/orders/services/order.service';
import { notFound } from 'next/navigation';

export default async function OrderDetailPage({ params }: { params: { id: string } }) {
  const order = await orderService.getById(params.id);
  if (!order) notFound();

  return (
    <main className="container mx-auto p-6">
      <h1 className="text-xl font-bold">Order #{order.id}</h1>
      <p>Status: {order.status}</p>
    </main>
  );
}
```

### Root Layout

```typescript
// app/layout.tsx
import '@/styles/globals.css';
import { Providers } from '@/global/components/shared/Providers';

export const metadata = {
  title: 'Your App',
  description: 'Built with Next.js',
};

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en">
      <body>
        <Providers>{children}</Providers>
      </body>
    </html>
  );
}
```

### API Route Handlers

```typescript
// app/api/orders/route.ts
import { NextResponse } from 'next/server';
import { orderService } from '@/features/orders/services/order.service';

export async function GET() {
  const orders = await orderService.getAll();
  return NextResponse.json(orders);
}

export async function POST(request: Request) {
  const body = await request.json();
  const order = await orderService.create(body);
  return NextResponse.json(order, { status: 201 });
}
```

---

## Feature Modules

Each feature is fully self-contained with its own components, hooks, services, actions, and types:

```
features/auth/
├── components/        # UI specific to this feature
├── hooks/             # Client-side data fetching & logic
├── services/          # Data access (API calls or direct DB)
├── actions/           # Server Actions (Next.js specific)
├── utils/             # Feature-specific helpers
├── @types/            # TypeScript types, DTOs, responses
│   ├── auth.types.ts
│   ├── auth.dto.ts
│   ├── auth.response.ts
│   └── index.ts
└── index.ts           # Public API (barrel export)
```

### Feature Component (Client)

```typescript
// features/auth/components/LoginForm.tsx
'use client';

import { useState } from 'react';
import { Button, Input } from '@/global/components/ui';
import { useLogin } from '../hooks/useLogin';

export const LoginForm = () => {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const { mutate: login, isPending } = useLogin();

  return (
    <form onSubmit={(e) => { e.preventDefault(); login({ email, password }); }} className="space-y-4">
      <Input placeholder="Email" value={email} onChange={(e) => setEmail(e.target.value)} />
      <Input type="password" placeholder="Password" value={password} onChange={(e) => setPassword(e.target.value)} />
      <Button type="submit" loading={isPending}>Login</Button>
    </form>
  );
};
```

### Feature Hook

```typescript
// features/auth/hooks/useLogin.ts
'use client';

import { useMutation } from '@tanstack/react-query';
import { useRouter } from 'next/navigation';
import { authService } from '../services/auth.service';
import { useAuthStore } from '@/global/store/auth.store';
import type { LoginDto } from '../@types';

export const useLogin = () => {
  const router = useRouter();
  const setUser = useAuthStore((s) => s.setUser);

  return useMutation({
    mutationFn: (dto: LoginDto) => authService.login(dto),
    onSuccess: (data) => {
      setUser(data.user);
      router.push('/');
    },
  });
};
```

### Feature Service

```typescript
// features/auth/services/auth.service.ts
import { apiClient } from '@/global/config/apiClient';
import type { LoginDto, RegisterDto, AuthResponse } from '../@types';

export const authService = {
  login: (dto: LoginDto) =>
    apiClient.post<AuthResponse>('/auth/login', dto).then((r) => r.data),

  register: (dto: RegisterDto) =>
    apiClient.post<AuthResponse>('/auth/register', dto).then((r) => r.data),
};
```

### Server Actions

```typescript
// features/auth/actions/auth.action.ts
'use server';

import { cookies } from 'next/headers';
import { redirect } from 'next/navigation';
import { authService } from '../services/auth.service';
import type { LoginDto } from '../@types';

export async function loginAction(dto: LoginDto) {
  const { token } = await authService.login(dto);
  cookies().set('token', token, { httpOnly: true, secure: true });
  redirect('/');
}
```

### Feature Types

```typescript
// features/auth/@types/auth.dto.ts
export interface LoginDto {
  email: string;
  password: string;
}

export interface RegisterDto {
  name: string;
  email: string;
  password: string;
}
```

```typescript
// features/auth/@types/auth.types.ts
export interface User {
  id: string;
  email: string;
  name: string;
  avatar?: string;
}
```

### Barrel Export

```typescript
// features/auth/index.ts
export { LoginForm } from './components/LoginForm';
export { RegisterForm } from './components/RegisterForm';
export { useLogin } from './hooks/useLogin';
export { useRegister } from './hooks/useRegister';
export type { User, LoginDto, RegisterDto } from './@types';
```

---

## Global Layer

The `global/` directory holds everything shared across features:

### UI Components

```typescript
// global/components/ui/Button.tsx
'use client';

import { cn } from '@/global/utils/cn';

interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  variant?: 'primary' | 'secondary';
  loading?: boolean;
}

export const Button = ({ children, variant = 'primary', loading, className, ...props }: ButtonProps) => (
  <button
    className={cn(
      'px-5 py-2.5 rounded-lg font-semibold transition-colors disabled:opacity-50',
      variant === 'primary' && 'bg-blue-600 text-white hover:bg-blue-700',
      variant === 'secondary' && 'border border-blue-600 text-blue-600 hover:bg-blue-50',
      className,
    )}
    disabled={loading || props.disabled}
    {...props}
  >
    {loading ? 'Loading...' : children}
  </button>
);
```

### Utility: cn

```typescript
// global/utils/cn.ts
import { clsx, type ClassValue } from 'clsx';
import { twMerge } from 'tailwind-merge';

export const cn = (...inputs: ClassValue[]) => twMerge(clsx(inputs));
```

### Global Store

```typescript
// global/store/auth.store.ts
import { create } from 'zustand';
import { persist } from 'zustand/middleware';
import type { User } from '@/features/auth/@types';

interface AuthState {
  user: User | null;
  isAuthenticated: boolean;
  setUser: (user: User) => void;
  logout: () => void;
}

export const useAuthStore = create<AuthState>()(
  persist(
    (set) => ({
      user: null,
      isAuthenticated: false,
      setUser: (user) => set({ user, isAuthenticated: true }),
      logout: () => set({ user: null, isAuthenticated: false }),
    }),
    { name: 'auth-storage' },
  ),
);
```

### API Client

```typescript
// global/config/apiClient.ts
import axios from 'axios';
import { env } from './env';

export const apiClient = axios.create({
  baseURL: env.NEXT_PUBLIC_API_URL,
  timeout: 10_000,
  headers: { 'Content-Type': 'application/json' },
});
```

### Providers Wrapper

```typescript
// global/components/shared/Providers.tsx
'use client';

import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import { useState } from 'react';

export const Providers = ({ children }: { children: React.ReactNode }) => {
  const [queryClient] = useState(() => new QueryClient());

  return <QueryClientProvider client={queryClient}>{children}</QueryClientProvider>;
};
```

---

## Configuration

### Path Aliases

```json
// tsconfig.json
{
  "compilerOptions": {
    "target": "ES2017",
    "lib": ["dom", "dom.iterable", "esnext"],
    "strict": true,
    "module": "esnext",
    "moduleResolution": "bundler",
    "jsx": "preserve",
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"],
      "@/features/*": ["src/features/*"],
      "@/global/*": ["src/global/*"]
    },
    "plugins": [{ "name": "next" }]
  },
  "include": ["next-env.d.ts", "**/*.ts", "**/*.tsx", ".next/types/**/*.ts"]
}
```

### Next.js Config

```typescript
// next.config.ts
import type { NextConfig } from 'next';

const nextConfig: NextConfig = {
  reactStrictMode: true,
};

export default nextConfig;
```

---

## Getting Started

```bash
# Create project
npx create-next-app@latest --typescript --tailwind --app --src-dir

# Install state management & data fetching
npm install zustand @tanstack/react-query axios

# Install utility libraries
npm install clsx tailwind-merge

# Start dev server
npm run dev
```

---

## Server vs Client Components

{: .highlight }
Next.js App Router defaults to **Server Components**. Add `'use client'` only when needed.

| Use Server Components for | Use Client Components for |
|:--------------------------|:--------------------------|
| Data fetching | Interactive forms & inputs |
| Database access | Event handlers (onClick, onChange) |
| Reading cookies/headers | Browser APIs (localStorage, etc.) |
| Static content | State management (useState, Zustand) |
| SEO metadata | Real-time updates |

### Pattern: Server Fetching + Client Interactivity

```typescript
// app/orders/page.tsx (Server Component — fetches data)
import { OrderList } from '@/features/orders/components/OrderList';
import { orderService } from '@/features/orders/services/order.service';

export default async function OrdersPage() {
  const orders = await orderService.getAll();
  return <OrderList initialOrders={orders} />;
}
```

```typescript
// features/orders/components/OrderList.tsx (Client Component — handles interactivity)
'use client';

import { useState } from 'react';
import { OrderCard } from './OrderCard';
import type { Order } from '../@types';

export const OrderList = ({ initialOrders }: { initialOrders: Order[] }) => {
  const [filter, setFilter] = useState('all');
  const filtered = initialOrders.filter((o) => filter === 'all' || o.status === filter);

  return (
    <div>
      <select value={filter} onChange={(e) => setFilter(e.target.value)}>
        <option value="all">All</option>
        <option value="pending">Pending</option>
        <option value="completed">Completed</option>
      </select>
      {filtered.map((order) => (
        <OrderCard key={order.id} order={order} />
      ))}
    </div>
  );
};
```

---

## Best Practices

### Import Order

```typescript
// 1. React / Next.js
import { Suspense } from 'react';
import { notFound } from 'next/navigation';

// 2. Third-party
import { useQuery } from '@tanstack/react-query';

// 3. Global (shared)
import { Button } from '@/global/components/ui';
import { cn } from '@/global/utils/cn';

// 4. Feature (relative)
import { useLogin } from '../hooks/useLogin';
import type { LoginDto } from '../@types';
```

### Rules

- **Pages are thin** — `app/` files only compose feature components, no business logic.
- **Features don't import from other features** — shared code goes in `global/`.
- **Barrel exports** — each feature exposes a public API via `index.ts`.
- **Types stay close** — feature types live in `@types/` inside the feature, global types in `global/@types/`.
- **Server first** — default to Server Components; add `'use client'` only when interactivity is needed.
- **Server Actions for mutations** — use the `actions/` folder for form submissions and data mutations.
- **State separation** — Zustand for client state, React Query for server-state caching, Server Components for initial data.

{: .note }
This structure scales from side projects to large production apps. Start simple and add feature folders as your app grows.
