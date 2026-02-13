---
layout: default
title: React Native Expo
parent: Folder Structures
nav_order: 1
---

# React Native Expo Folder Structure
{: .no_toc }

Complete folder structure guide for React Native Expo applications using feature-based architecture with Expo Router.
{: .fs-6 .fw-300 }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Project Structure Overview

```
src/
├── app/                          # Expo Router pages (screens) using file-based routing
│   ├── (auth)/                   # Auth stack
│   │   ├── login.tsx
│   │   ├── register.tsx
│   │   └── _layout.tsx
│   ├── (tabs)/                   # Bottom tab layout
│   │   ├── index.tsx
│   │   └── _layout.tsx
│   ├── (chats)/                  # Nested route for chat
│   │   ├── [id].tsx              # Dynamic chat screen
│   │   └── _layout.tsx
│   ├── (orders)/                 # Order screens
│   │   └── index.tsx
│   └── _layout.tsx              # Root layout
│
├── assets/                       # Static assets
│   ├── fonts/
│   ├── icons/
│   ├── images/
│   ├── lottie/
│   └── svg/
│
├── features/                     # Feature-based modules
│   ├── auth/
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── services/
│   │   └── types.ts
│   ├── chat/
│   ├── orders/
│   └── home/
│
├── components/                   # Global reusable components
│   ├── ui/                       # Basic React Native components
│   └── shared/                   # Complex shared components
│
├── constants/                    # App-wide constants
├── lib/                          # Third-party integrations
├── store/                        # Global state management
├── hooks/                        # Global custom hooks
├── navigation/                   # Navigation helpers
├── utils/                        # Helper functions
├── types/                        # TypeScript definitions
├── service/                      # Background services
└── config/                       # App configuration
```

## Key Features

{: .highlight }
This structure is optimized for React Native Expo development with modern patterns and best practices.

- **🏗️ Expo Router** - File-based routing with full TypeScript support
- **📱 Mobile-first** - Optimized for iOS and Android development
- **⚡ Performance** - Efficient state management and code splitting
- **🔧 Developer Experience** - Clear structure and path aliases

---

## Expo Router Implementation

### Route Groups

Parentheses `()` create route groups without affecting URL structure:

```typescript
// app/(auth)/login.tsx
import { AuthLoginForm } from '@/features/auth/components';

export default function LoginScreen() {
  return <AuthLoginForm />;
}
```

### Layout Files

```typescript
// app/(auth)/_layout.tsx
import { Stack } from 'expo-router';
import { AuthProvider } from '@/features/auth/store';

export default function AuthLayout() {
  return (
    <AuthProvider>
      <Stack>
        <Stack.Screen name="login" options={{"{{"}} title: "Login" {{"}}"}} />
        <Stack.Screen name="register" options={{"{{"}} title: "Register" {{"}}"}} />
      </Stack>
    </AuthProvider>
  );
}
```

### Dynamic Routes

```typescript
// app/(chats)/[id].tsx
import { useLocalSearchParams } from 'expo-router';
import { ChatScreen } from '@/features/chat/components';

export default function ChatPage() {
  const { id } = useLocalSearchParams<{ id: string }>();
  return <ChatScreen chatId={id} />;
}
```

---

## Feature Structure

Each feature is self-contained:

```
features/auth/
├── components/        # Auth-specific React Native components
│   ├── LoginForm.tsx
│   ├── RegisterForm.tsx
│   └── AuthButton.tsx
├── hooks/            # Auth-related hooks
│   ├── useAuth.ts
│   └── useAuthForm.ts
├── services/         # API calls and business logic
│   ├── authApi.ts
│   └── authValidation.ts
├── store/           # Feature-specific state
│   └── authStore.ts
└── types.ts         # Auth-related TypeScript types
```

### Example Component

```typescript
// features/auth/components/LoginForm.tsx
import { View, Text, StyleSheet } from 'react-native';
import { useState } from 'react';
import { router } from 'expo-router';
import { Button, TextInput } from '@/components/ui';
import { useAuth } from '../hooks/useAuth';
import { validateEmail } from '@/utils/validateEmail';

export const LoginForm = () => {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const { login, isLoading } = useAuth();

  const handleLogin = async () => {
    if (!validateEmail(email)) return;
    
    const success = await login(email, password);
    if (success) {
      router.replace('/(tabs)/');
    }
  };

  return (
    <View style={styles.container}>
      <Text style={styles.title}>Login</Text>
      
      <TextInput
        placeholder="Email"
        value={email}
        onChangeText={setEmail}
        autoCapitalize="none"
        keyboardType="email-address"
      />
      
      <TextInput
        placeholder="Password"
        value={password}
        onChangeText={setPassword}
        secureTextEntry
      />
      
      <Button 
        onPress={handleLogin}
        loading={isLoading}
        variant="primary"
      >
        Login
      </Button>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    padding: 20,
    justifyContent: 'center',
  },
  title: {
    fontSize: 24,
    fontWeight: 'bold',
    marginBottom: 20,
    textAlign: 'center',
  },
});
```

---

## Global Components

### UI Components

```typescript
// components/ui/Button.tsx
import { TouchableOpacity, Text, StyleSheet, ActivityIndicator } from 'react-native';
import { theme } from '@/config/theme';

interface ButtonProps {
  children: React.ReactNode;
  onPress?: () => void;
  variant?: 'primary' | 'secondary';
  loading?: boolean;
  disabled?: boolean;
}

export const Button: React.FC<ButtonProps> = ({ 
  children, 
  onPress, 
  variant = 'primary',
  loading = false,
  disabled = false
}) => {
  return (
    <TouchableOpacity 
      style={[
        styles.button, 
        styles[variant],
        (disabled || loading) && styles.disabled
      ]} 
      onPress={onPress}
      disabled={disabled || loading}
    >
      {loading ? (
        <ActivityIndicator color="white" />
      ) : (
        <Text style={[styles.text, styles[`${variant}Text`]]}>{children}</Text>
      )}
    </TouchableOpacity>
  );
};

const styles = StyleSheet.create({
  button: {
    paddingHorizontal: 20,
    paddingVertical: 12,
    borderRadius: 8,
    alignItems: 'center',
    marginVertical: 8,
  },
  primary: {
    backgroundColor: theme.colors.primary,
  },
  secondary: {
    backgroundColor: 'transparent',
    borderWidth: 1,
    borderColor: theme.colors.primary,
  },
  disabled: {
    opacity: 0.5,
  },
  text: {
    fontSize: 16,
    fontWeight: '600',
  },
  primaryText: {
    color: 'white',
  },
  secondaryText: {
    color: theme.colors.primary,
  },
});
```

---

## State Management

### Global Store with Zustand

```typescript
// store/auth.store.ts
import { create } from "zustand";
import { persist, createJSONStorage } from 'zustand/middleware';
import AsyncStorage from '@react-native-async-storage/async-storage';

interface User {
  id: string;
  email: string;
  name: string;
}

interface AuthState {
  user: User | null;
  isLoading: boolean;
  isAuthenticated: boolean;
  login: (email: string, password: string) => Promise<boolean>;
  logout: () => void;
  setUser: (user: User) => void;
}

export const useAuthStore = create<AuthState>()(
  persist(
    (set, get) => ({
      user: null,
      isLoading: false,
      isAuthenticated: false,

      login: async (email: string, password: string) => {
        set({ isLoading: true });
        try {
          // API call here
          const response = await authApi.login(email, password);
          const user = response.data.user;
          
          set({ 
            user, 
            isAuthenticated: true, 
            isLoading: false 
          });
          return true;
        } catch (error) {
          set({ isLoading: false });
          return false;
        }
      },

      logout: () => {
        set({ 
          user: null, 
          isAuthenticated: false 
        });
      },

      setUser: (user: User) => {
        set({ user, isAuthenticated: true });
      },
    }),
    {
      name: 'auth-storage',
      storage: createJSONStorage(() => AsyncStorage),
      partialize: (state) => ({ 
        user: state.user,
        isAuthenticated: state.isAuthenticated 
      }),
    }
  )
);
```

---

## Configuration

### Theme Configuration

```typescript
// config/theme.ts
import { StyleSheet } from 'react-native';

export const theme = {
  colors: {
    primary: "#007AFF",
    secondary: "#FF3B30",
    background: "#FFFFFF",
    surface: "#F2F2F7",
    text: "#000000",
    textSecondary: "#8E8E93",
    border: "#C7C7CC",
    success: "#34C759",
    warning: "#FF9500",
    error: "#FF3B30",
  },
  spacing: {
    xs: 4,
    sm: 8,
    md: 16,
    lg: 24,
    xl: 32,
  },
  typography: {
    fontSize: {
      small: 12,
      medium: 16,
      large: 20,
      xlarge: 24,
    },
    fontWeight: {
      normal: '400' as const,
      medium: '500' as const,
      semiBold: '600' as const,
      bold: '700' as const,
    }
  },
  borderRadius: {
    small: 4,
    medium: 8,
    large: 12,
  },
};

export const globalStyles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: theme.colors.background,
  },
  centered: {
    justifyContent: 'center',
    alignItems: 'center',
  },
  row: {
    flexDirection: 'row',
  },
  shadow: {
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 2 },
    shadowOpacity: 0.25,
    shadowRadius: 3.84,
    elevation: 5,
  },
});
```

### Path Aliases

```json
// tsconfig.json
{
  "extends": "expo/tsconfig.base",
  "compilerOptions": {
    "strict": true,
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"],
      "@/components/*": ["src/components/*"],
      "@/features/*": ["src/features/*"],
      "@/utils/*": ["src/utils/*"],
      "@/constants/*": ["src/constants/*"],
      "@/assets/*": ["src/assets/*"],
      "@/config/*": ["src/config/*"],
      "@/hooks/*": ["src/hooks/*"],
      "@/store/*": ["src/store/*"]
    }
  },
  "include": [
    "**/*.ts",
    "**/*.tsx",
    ".expo/types/**/*.ts",
    "expo-env.d.ts"
  ]
}
```

---

## Getting Started

### 1. Install Dependencies

```json
{
  "dependencies": {
    "expo": "~49.0.0",
    "expo-router": "^2.0.0",
    "react": "18.2.0",
    "react-native": "0.72.0",
    "zustand": "^4.4.0",
    "@react-native-async-storage/async-storage": "1.18.2",
    "expo-secure-store": "~12.3.1",
    "expo-notifications": "~0.20.1",
    "expo-background-fetch": "~11.3.0"
  },
  "devDependencies": {
    "@types/react": "~18.2.14",
    "typescript": "^5.1.3"
  }
}
```

### 2. Expo Configuration

```json
// app.json
{
  "expo": {
    "name": "Your App Name",
    "slug": "your-app-slug",
    "version": "1.0.0",
    "orientation": "portrait",
    "icon": "./src/assets/icon.png",
    "userInterfaceStyle": "light",
    "scheme": "yourapp",
    "splash": {
      "image": "./src/assets/splash.png",
      "resizeMode": "contain",
      "backgroundColor": "#ffffff"
    },
    "assetBundlePatterns": ["**/*"],
    "ios": {
      "supportsTablet": true
    },
    "android": {
      "adaptiveIcon": {
        "foregroundImage": "./src/assets/adaptive-icon.png",
        "backgroundColor": "#FFFFFF"
      }
    },
    "web": {
      "favicon": "./src/assets/favicon.png"
    },
    "plugins": ["expo-router"]
  }
}
```

### 3. Setup Commands

```bash
# Create new Expo project
npx create-expo-app --template blank-typescript

# Install additional dependencies
npx expo install expo-router zustand @react-native-async-storage/async-storage

# Start development server
npx expo start
```

---

## Best Practices

### Import Organization

```typescript
// React Native core imports
import React from "react";
import { View, Text, StyleSheet } from "react-native";

// Expo imports
import { router } from "expo-router";
import * as SecureStore from 'expo-secure-store';

// Third-party libraries
import { create } from 'zustand';

// Internal imports
import { Button } from "@/components/ui";
import { useAuth } from "@/features/auth/hooks";
import { COLORS } from "@/constants";
import { theme } from "@/config/theme";
```

### Component Structure

- Keep components focused and single-purpose
- Use TypeScript interfaces for props
- Implement proper error handling
- Follow React Native performance best practices

### State Management

- Use Zustand for global state
- Keep feature-specific state in feature stores
- Implement proper persistence for auth state
- Use React Query for server state management

{: .note }
This structure has been tested in production React Native Expo applications and scales well from small projects to large enterprise applications.
