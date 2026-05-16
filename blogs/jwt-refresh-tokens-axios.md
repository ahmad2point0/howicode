---
layout: default
title: Auto JWT Refresh with Axios
parent: Engineering Notes
nav_order: 1
description: "How I handle JWT refresh tokens automatically with Axios across React Native, Expo, and Next.js — using one centralized file, Zustand, and a queue."
---

# How to Handle JWT Refresh Tokens Automatically with Axios
{: .no_toc }

*React Native · Expo · Next.js*
{: .fs-5 .fw-300 }

---

<details open markdown="1">
<summary>Table of contents</summary>

1. TOC
{:toc}

</details>

---

## The problem

Your user logs in. Everything works. The access token expires. Suddenly every API call starts failing with `401 Unauthorized` and the app feels broken.

This is one of those rites-of-passage problems in auth. The right answer isn't to lengthen the token lifetime — it's to **refresh transparently in the background** so the user never notices.

I want a single file in the codebase that:

- Attaches the access token to every outgoing request
- Detects 401s and tries to refresh the access token automatically
- Retries the original request with the new token
- Prevents N parallel refreshes when many requests fail at once
- Logs the user out if the refresh token itself is dead

That file is an Axios instance with two interceptors plus a small refresh queue. It looks the same whether you're in React Native, Expo, or Next.js — only the storage layer changes.

---

## The pieces

| Piece | What it does |
|:------|:-------------|
| **Axios instance** | Centralized HTTP client — base URL, timeout, default headers |
| **Zustand store** | Holds `access_token` and `refresh_token` in memory (RN) |
| **Cookies** | Holds the access token in Next.js (read by SSR + client) |
| **Request interceptor** | Attaches the current access token to every request |
| **Response interceptor** | Catches 401s, refreshes, retries — the brains of the system |
| **Refresh queue** | Ensures only one refresh happens at a time |

---

## Step 1 — The Axios instance

Create the instance once. Import it everywhere. Never `import axios from "axios"` inside a feature.

```ts
// global/api/axios-instance.ts
import axios from "axios";

export const axiosInstance = axios.create({
  baseURL: process.env.EXPO_PUBLIC_API_URL ?? process.env.NEXT_PUBLIC_API_URL,
  timeout: 10_000,
  headers: {
    "Content-Type": "application/json",
  },
});
```

Same file works on both platforms — the only difference is which env var has the URL.

---

## Step 2 — Request interceptor (attach the access token)

Read the token from wherever it lives (Zustand on RN, cookies on Next.js) and add it to the `Authorization` header.

```ts
import Cookies from "js-cookie"; // Next.js only
import { useAuthStore } from "@/features/auth/store";

axiosInstance.interceptors.request.use(
  async (config) => {
    // React Native / Expo — token lives in memory via Zustand
    const { access_token } = useAuthStore.getState();

    // Next.js — also check cookies (server + client both have access)
    const cookieToken = typeof window !== "undefined"
      ? Cookies.get("access_token")
      : undefined;

    const token = access_token ?? cookieToken;

    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  (error) => Promise.reject(error),
);
```

A few notes that matter:

- `useAuthStore.getState()` reads from Zustand **outside React** — interceptors aren't components, they don't have hooks
- Don't throw if there's no token. Some routes are public (`/login`, `/health`). Let the request go out without an `Authorization` header and let the server decide.

---

## Step 3 — Response interceptor (the auto-refresh magic)

This is the heart of the pattern. Catch a 401 once per original request, call `/refresh`, update state, retry.

```ts
import axios from "axios";
import { useAuthStore } from "@/features/auth/store";
import { logout } from "@/features/auth/services/logout";

axiosInstance.interceptors.response.use(
  (response) => response,
  async (error) => {
    const originalRequest = error.config;

    // Only handle 401s, and only retry once per original request
    if (error.response?.status === 401 && !originalRequest._retry) {
      originalRequest._retry = true;

      const { refresh_token, setAccess_token } = useAuthStore.getState();

      if (!refresh_token) {
        logout();
        return Promise.reject(error);
      }

      try {
        // IMPORTANT: use raw axios, not axiosInstance, to avoid
        // triggering this interceptor recursively if /refresh 401s.
        const response = await axios.post(
          `${process.env.EXPO_PUBLIC_API_URL}/refresh`,
          { refresh_token },
        );

        const newAccessToken = response.data.token?.access_token?.token;
        setAccess_token(newAccessToken);

        // Update the failed request with the new token and replay it
        originalRequest.headers.Authorization = `Bearer ${newAccessToken}`;
        return axiosInstance(originalRequest);
      } catch (refreshError) {
        logout();
        return Promise.reject(refreshError);
      }
    }

    return Promise.reject(error);
  },
);
```

### Why this works

- `_retry` is a flag set on the **original request**. If the retried request also 401s, we bail to the `return Promise.reject` at the bottom — no infinite loop.
- The refresh call uses **raw `axios`**, not the instance, so its own 401 doesn't trigger the same interceptor recursively.
- On success, the original request is replayed with the new token. The component that fired it gets the response it expected — it never sees the 401.

---

## Step 4 — The queue (prevents the thundering herd)

There's still a bug in the version above. Imagine your dashboard fires six parallel requests on mount, and the token has just expired. All six get a 401. All six try to refresh. You make six refresh calls — and on most backends, the second one invalidates the first.

The fix: a tiny queue that says **"a refresh is already in flight; wait for it."**

```ts
let isRefreshing = false;
let pendingRequests: Array<(token: string) => void> = [];

const enqueue = (cb: (token: string) => void) => {
  pendingRequests.push(cb);
};

const flushQueue = (token: string) => {
  pendingRequests.forEach((cb) => cb(token));
  pendingRequests = [];
};

axiosInstance.interceptors.response.use(
  (response) => response,
  async (error) => {
    const originalRequest = error.config;

    if (error.response?.status === 401 && !originalRequest._retry) {
      originalRequest._retry = true;

      if (isRefreshing) {
        // A refresh is already happening — wait for it
        return new Promise((resolve) => {
          enqueue((token) => {
            originalRequest.headers.Authorization = `Bearer ${token}`;
            resolve(axiosInstance(originalRequest));
          });
        });
      }

      const { refresh_token, setAccess_token } = useAuthStore.getState();
      if (!refresh_token) {
        logout();
        return Promise.reject(error);
      }

      isRefreshing = true;
      try {
        const { data } = await axios.post(
          `${process.env.EXPO_PUBLIC_API_URL}/refresh`,
          { refresh_token },
        );

        const newAccessToken = data.token?.access_token?.token;
        setAccess_token(newAccessToken);

        flushQueue(newAccessToken);

        originalRequest.headers.Authorization = `Bearer ${newAccessToken}`;
        return axiosInstance(originalRequest);
      } catch (refreshError) {
        pendingRequests = [];
        logout();
        return Promise.reject(refreshError);
      } finally {
        isRefreshing = false;
      }
    }

    return Promise.reject(error);
  },
);
```

Now six parallel 401s become **one** refresh call, and the other five wait for the new token and then replay themselves.

---

## Step 5 — The Zustand store

For React Native / Expo, the tokens live in a small Zustand slice. (On Next.js, they live in HTTP-only cookies set by the server — the client doesn't manage them directly.)

```ts
// features/auth/store/auth-store.ts
import { create } from "zustand";
import { persist, createJSONStorage } from "zustand/middleware";
import AsyncStorage from "@react-native-async-storage/async-storage";

interface AuthState {
  access_token: string | null;
  refresh_token: string | null;
  setAccess_token: (t: string) => void;
  setTokens: (a: string, r: string) => void;
  clear: () => void;
}

export const useAuthStore = create<AuthState>()(
  persist(
    (set) => ({
      access_token: null,
      refresh_token: null,
      setAccess_token: (access_token) => set({ access_token }),
      setTokens: (access_token, refresh_token) =>
        set({ access_token, refresh_token }),
      clear: () => set({ access_token: null, refresh_token: null }),
    }),
    {
      name: "auth",
      storage: createJSONStorage(() => AsyncStorage),
      // Only persist the refresh token across reloads — access tokens
      // are short-lived and live in memory only.
      partialize: (s) => ({ refresh_token: s.refresh_token }),
    },
  ),
);
```

For production, replace `AsyncStorage` with **`expo-secure-store`** for the refresh token. AsyncStorage is plaintext; SecureStore uses the OS keychain.

---

## Step 6 — Logout

`logout` clears the store, removes the cookie (Next.js), and navigates to the login screen.

```ts
export function logout() {
  useAuthStore.getState().clear();
  if (typeof window !== "undefined") {
    Cookies.remove("access_token");
  }
  router.replace("/login");
}
```

---

## Putting it all together

The interceptor file is **the** auth integration point. Every other file in the app just calls `axiosInstance.get('/orders')` and gets data — it never thinks about tokens, refreshes, or 401s.

```text
global/api/
└── axios-instance.ts   ← everything from this post lives here

features/auth/
├── store/auth-store.ts
└── services/logout.ts

features/orders/
└── services/orders-service.ts   ← just uses axiosInstance
```

---

## Why I like this pattern

- ✅ **Transparent to the user** — they never see a 401-driven crash
- ✅ **One source of truth** — auth concerns live in one file, not sprinkled across the app
- ✅ **Cross-platform** — works identically on RN, Expo, and Next.js
- ✅ **Race-condition safe** — the queue prevents the thundering-herd bug
- ✅ **Testable** — the interceptor is a pure function of `error`; MSW makes it trivial to test

By combining: Axios interceptors, a Zustand store, a `/refresh` endpoint, and a request queue — you get an enterprise-level authentication flow in just one centralized file.

---

## Originally published

I first wrote this up on Medium: [How to Handle JWT Refresh Tokens Automatically with Axios](https://ahmad2point0.medium.com/how-to-handle-jwt-refresh-tokens-automatically-with-axios-react-native-expo-next-js-fc69e85b4ff6). This version is expanded with the queue pattern and the Next.js notes.
