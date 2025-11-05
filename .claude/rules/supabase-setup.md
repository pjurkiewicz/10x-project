# Supabase Integration Setup Guide

## Overview

This document provides a comprehensive guide for integrating Supabase with your Astro project. Follow these steps to ensure proper setup and configuration.

## Prerequisites

Before proceeding with the integration, verify the following requirements:

### Project Requirements
- **Astro 5** with TypeScript support
- **TypeScript 5** configured and working
- **React 19** integrated with Astro
- **Tailwind 4** for styling
- Node.js installed (version 18 or higher)

### Required Files and Configurations
- `/supabase/config.toml` exists and is properly configured
- `/src/db/database.types.ts` exists with correct type definitions
- `@supabase/supabase-js` package installed

**IMPORTANT**: If any prerequisites are not met, stop and ask the user to resolve them before proceeding.

## Installation

### Install Supabase Client
```bash
npm install @supabase/supabase-js
# or
yarn add @supabase/supabase-js
# or
pnpm add @supabase/supabase-js
```

## File Structure Setup

### 1. Supabase Client Initialization

**File: `/src/db/supabase.client.ts`**

```typescript
import { createClient } from '@supabase/supabase-js';
import type { Database } from './database.types.ts';

const supabaseUrl = import.meta.env.SUPABASE_URL;
const supabaseAnonKey = import.meta.env.SUPABASE_KEY;

if (!supabaseUrl || !supabaseAnonKey) {
  throw new Error('Missing Supabase environment variables');
}

export const supabaseClient = createClient<Database>(supabaseUrl, supabaseAnonKey);
```

**Key Points:**
- Use TypeScript with proper typing
- Import `Database` type for type safety
- Validate environment variables exist
- Export the client instance

### 2. Middleware Configuration

**File: `/src/middleware/index.ts`**

```typescript
import { defineMiddleware } from 'astro:middleware';
import { supabaseClient } from '../db/supabase.client.ts';

export const onRequest = defineMiddleware((context, next) => {
  // Add Supabase client to context locals
  context.locals.supabase = supabaseClient;

  // Optionally handle authentication
  const accessToken = context.cookies.get('sb-access-token')?.value;

  if (accessToken) {
    const { data: { user } } = await supabaseClient.auth.getUser(accessToken);
    context.locals.user = user;
  }

  return next();
});
```

**Middleware Benefits:**
- Makes Supabase available throughout the application
- Handles authentication state automatically
- Provides type-safe access to database

### 3. TypeScript Environment Definitions

**File: `src/env.d.ts`**

```typescript
/// <reference types="astro/client" />

import type { SupabaseClient } from '@supabase/supabase-js';
import type { Database } from './db/database.types.ts';

declare global {
  namespace App {
    interface Locals {
      supabase: SupabaseClient<Database>;
      user?: import('@supabase/supabase-js').User | null;
    }
  }
}

interface ImportMetaEnv {
  readonly SUPABASE_URL: string;
  readonly SUPABASE_KEY: string;
  readonly SUPABASE_SERVICE_ROLE_KEY?: string;
}

interface ImportMeta {
  readonly env: ImportMetaEnv;
}

// Ensure database types are available
export type Database = Database;
```

### 4. Environment Variables Configuration

**Create `.env` file:**
```env
# Supabase Configuration
SUPABASE_URL=https://your-project-ref.supabase.co
SUPABASE_KEY=your-anon-key-here
SUPABASE_SERVICE_ROLE_KEY=your-service-role-key-here
```

**Create `.env.example` file:**
```env
# Supabase Configuration
SUPABASE_URL=https://your-project-ref.supabase.co
SUPABASE_KEY=your-anon-key-here
SUPABASE_SERVICE_ROLE_KEY=your-service-role-key-here
```

## Database Type Generation

### Generate TypeScript Types
```bash
# Navigate to your project root
npx supabase gen types typescript --project-id your-project-ref > src/db/database.types.ts
```

**Example `database.types.ts` structure:**
```typescript
export type Json =
  | string
  | number
  | boolean
  | null
  | { [key: string]: Json | undefined }
  | Json[]

export interface Database {
  public: {
    Tables: {
      users: {
        Row: {
          id: string
          email: string
          username: string | null
          created_at: string
          updated_at: string
        }
        Insert: {
          id?: string
          email: string
          username?: string | null
          created_at?: string
          updated_at?: string
        }
        Update: {
          id?: string
          email?: string
          username?: string | null
          created_at?: string
          updated_at?: string
        }
      }
      // ... other tables
    }
    Views: {
      [_ in never]: never
    }
    Functions: {
      [_ in never]: never
    }
    Enums: {
      [_ in never]: never
    }
  }
}
```

## Usage Examples

### API Routes with Supabase

**File: `src/pages/api/users/[id].ts`**
```typescript
import type { APIRoute } from 'astro';

export const GET: APIRoute = async ({ params, locals }) => {
  const { data, error } = await locals.supabase
    .from('users')
    .select('*')
    .eq('id', params.id)
    .single();

  if (error) {
    return new Response(
      JSON.stringify({ error: error.message }),
      { status: 404, headers: { 'Content-Type': 'application/json' } }
    );
  }

  return new Response(
    JSON.stringify({ user: data }),
    { headers: { 'Content-Type': 'application/json' } }
  );
};

export const PUT: APIRoute = async ({ params, request, locals }) => {
  const body = await request.json();

  const { data, error } = await locals.supabase
    .from('users')
    .update(body)
    .eq('id', params.id)
    .select()
    .single();

  if (error) {
    return new Response(
      JSON.stringify({ error: error.message }),
      { status: 400, headers: { 'Content-Type': 'application/json' } }
    );
  }

  return new Response(
    JSON.stringify({ user: data }),
    { headers: { 'Content-Type': 'application/json' } }
  );
};
```

### Astro Pages with Supabase

**File: `src/pages/profile.astro`**
```astro
---
import Layout from '../layouts/Layout.astro';

// Get user from middleware
const user = Astro.locals.user;

if (!user) {
  return Astro.redirect('/login');
}

// Fetch user profile data
const { data: profile } = await Astro.locals.supabase
  .from('profiles')
  .select('*')
  .eq('user_id', user.id)
  .single();
---

<Layout title="Profile">
  <div class="container mx-auto p-4">
    <h1>Welcome, {user.email}</h1>
    {profile && (
      <div>
        <h2>Profile Information</h2>
        <p>Username: {profile.username}</p>
        <p>Bio: {profile.bio}</p>
      </div>
    )}
  </div>
</Layout>
```

### React Components with Supabase

**File: `src/components/UserProfile.tsx`**
```tsx
import { useState, useEffect } from 'react';
import { supabaseClient } from '../db/supabase.client';
import type { User } from '@supabase/supabase-js';

interface UserProfileProps {
  userId: string;
}

export function UserProfile({ userId }: UserProfileProps) {
  const [user, setUser] = useState<User | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    const fetchUser = async () => {
      try {
        const { data, error } = await supabaseClient
          .from('users')
          .select('*')
          .eq('id', userId)
          .single();

        if (error) throw error;
        setUser(data);
      } catch (err) {
        setError(err instanceof Error ? err.message : 'Unknown error');
      } finally {
        setLoading(false);
      }
    };

    fetchUser();
  }, [userId]);

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  if (!user) return <div>User not found</div>;

  return (
    <div className="user-profile">
      <h2>{user.email}</h2>
      <p>Username: {user.username}</p>
    </div>
  );
}
```

## Authentication Implementation

### Sign Up Component
```tsx
export function SignUpForm() {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);

  const handleSignUp = async (e: React.FormEvent) => {
    e.preventDefault();
    setLoading(true);
    setError(null);

    try {
      const { error } = await supabaseClient.auth.signUp({
        email,
        password,
      });

      if (error) throw error;

      // Handle successful signup
      alert('Check your email for confirmation!');
    } catch (err) {
      setError(err instanceof Error ? err.message : 'Signup failed');
    } finally {
      setLoading(false);
    }
  };

  return (
    <form onSubmit={handleSignUp}>
      {/* Form fields */}
    </form>
  );
}
```

### Authentication State Management
```tsx
// Custom hook for authentication
export function useAuth() {
  const [user, setUser] = useState<User | null>(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    // Get initial session
    supabaseClient.auth.getSession().then(({ data: { session } }) => {
      setUser(session?.user ?? null);
      setLoading(false);
    });

    // Listen for auth changes
    const { data: { subscription } } = supabaseClient.auth.onAuthStateChange(
      (_event, session) => {
        setUser(session?.user ?? null);
        setLoading(false);
      }
    );

    return () => subscription.unsubscribe();
  }, []);

  const signOut = async () => {
    await supabaseClient.auth.signOut();
  };

  return { user, loading, signOut };
}
```

## Security Best Practices

### Row Level Security
- Always enable RLS on all tables
- Create granular policies for different operations
- Test policies thoroughly

### Environment Variables
- Never expose service role key to client-side code
- Use different keys for different environments
- Regularly rotate API keys

### Error Handling
- Never expose detailed error messages to users
- Log errors appropriately for debugging
- Implement proper error boundaries

## Testing the Integration

### Verify Setup
1. Check environment variables are loaded correctly
2. Test database connection
3. Verify authentication flow
4. Test API endpoints

### Common Issues and Solutions

**CORS Errors:**
```typescript
// In supabase/project_settings/api
allowed_origins = ['http://localhost:3000', 'https://yourdomain.com']
```

**Type Errors:**
```bash
# Regenerate types after schema changes
npx supabase gen types typescript --project-id your-project-ref > src/db/database.types.ts
```

**Authentication Issues:**
- Verify email confirmation settings
- Check redirect URLs in Supabase dashboard
- Ensure proper cookie configuration

This setup provides a solid foundation for building applications with Supabase and Astro, ensuring type safety, security, and best practices.