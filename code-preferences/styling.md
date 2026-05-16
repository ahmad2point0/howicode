---
layout: default
title: Styling
parent: Code I Prefer
nav_order: 5
---

# Styling
{: .no_toc }

I want to write styles fast, keep them co-located, and never end up grepping a global CSS file at 2am wondering which rule wins. That's it. Everything below follows from that.

---

## On web: Tailwind CSS

Utility-first wins for me because it solves the three problems I actually have:

- **Co-location** — the styles are right there in the component, not three folders away
- **No naming** — I never have to invent `<div className="card__inner--highlighted-v2">`
- **Predictable specificity** — utilities are flat; there's no cascade puzzle

```tsx
export function StatCard({ label, value }: Props) {
  return (
    <div className="rounded-xl border border-slate-800 bg-slate-900/60 p-5 transition hover:border-sky-500/50">
      <p className="text-xs uppercase tracking-wider text-slate-400">{label}</p>
      <p className="mt-1 text-2xl font-semibold text-slate-100">{value}</p>
    </div>
  );
}
```

### What I add on top of Tailwind

- **`clsx` or `cn` helper** — for conditional classes. Never string concatenation.
- **Design tokens in `tailwind.config.ts`** — colors, spacing scale, radii. I don't sprinkle hex codes in components.
- **`@apply` is allowed, but rarely** — only for repeated patterns that genuinely deserve a name (e.g., `.btn-primary`). If I'm doing it three times, I extract a component first.

---

## On React Native: StyleSheet or NativeWind

The `style` prop in RN accepts an object, not classes. Two paths:

### StyleSheet — the safe default

```tsx
import { StyleSheet, View, Text } from "react-native";

export function StatCard({ label, value }: Props) {
  return (
    <View style={styles.card}>
      <Text style={styles.label}>{label}</Text>
      <Text style={styles.value}>{value}</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  card: {
    borderRadius: 12,
    backgroundColor: "rgba(15, 23, 42, 0.6)",
    padding: 20,
  },
  label: { fontSize: 12, color: "#94a3b8", letterSpacing: 0.4 },
  value: { fontSize: 22, fontWeight: "600", color: "#f1f5f9", marginTop: 4 },
});
```

### NativeWind — when the web and app share a design system

If a project has both web (Tailwind) and mobile (RN), I reach for **NativeWind v5** so the same utility classes work on both. It cuts down on the cognitive tax of switching between platforms in the same codebase.

For RN-only projects, I stay on `StyleSheet`. Less magic, fewer build-time surprises.

---

## Tokens, not raw values

Whether it's Tailwind config or a TypeScript `theme` object, every color/spacing/radius/font-size lives in one tokens file:

```ts
export const colors = {
  brand: { 500: "#0ea5e9", 600: "#0284c7" },
  surface: { base: "#0b1020", raised: "#0f1530" },
  text: { primary: "#f1f5f9", muted: "#94a3b8" },
} as const;
```

Components reference `colors.brand[500]`, never `"#0ea5e9"`. When the design changes, I update one file.

---

## What I avoid

- **CSS-in-JS at runtime** (styled-components, Emotion) — the perf cost on RN and the cascade footguns on web don't pay back what I get for them
- **Global CSS files** beyond a tiny `reset` / `tokens` / `typography` layer
- {% raw %}**Inline `style={{ ... }}` for anything reusable**{% endraw %} — fine for true one-offs, but if I'm doing it twice, it goes into a class or a `StyleSheet`
- **Theme prop drilling** — themes live in a context (web) or a Zustand slice (RN), never as a prop on every component

---

## Dark mode

I design dark-first. Light mode is a variant, not the default I retrofit later. On web, that means CSS variables driven by a `data-theme` attribute on `<html>`; on RN, it's a `useColorScheme()` + token mapping. Either way, components never branch on theme — they just consume tokens that already resolved.
