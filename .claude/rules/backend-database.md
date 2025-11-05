# Backend and Database Development Guidelines

## Supabase Integration

### Core Principles
- Use Supabase for all backend services, including authentication and database interactions
- Follow Supabase security and performance best practices
- Implement proper error handling and data validation
- Use type-safe database operations

### Client Configuration
**File: `src/db/supabase.client.ts`**
```typescript
import { createClient } from '@supabase/supabase-js';
import type { Database } from '../db/database.types.ts';

const supabaseUrl = import.meta.env.SUPABASE_URL;
const supabaseAnonKey = import.meta.env.SUPABASE_KEY;

export const supabaseClient = createClient<Database>(supabaseUrl, supabaseAnonKey);
```

### Middleware Setup
**File: `src/middleware/index.ts`**
```typescript
import { defineMiddleware } from 'astro:middleware';
import { supabaseClient } from '../db/supabase.client.ts';

export const onRequest = defineMiddleware((context, next) => {
  context.locals.supabase = supabaseClient;
  return next();
});
```

### Environment Variables
```typescript
// src/env.d.ts
interface ImportMetaEnv {
  readonly SUPABASE_URL: string;
  readonly SUPABASE_KEY: string;
}
```

## Database Operations

### Type Safety
- Use `SupabaseClient` type from `src/db/supabase.client.ts`, not from `@supabase/supabase-js`
- Always import `Database` type for type-safe operations
- Define custom types for complex data structures

### Data Access Patterns
```typescript
// Use context.locals in Astro routes
export async function GET({ locals }) {
  const { data, error } = await locals.supabase
    .from('profiles')
    .select('*');

  if (error) {
    return new Response(JSON.stringify({ error: error.message }), {
      status: 400,
      headers: { 'Content-Type': 'application/json' }
    });
  }

  return new Response(JSON.stringify({ data }), {
    headers: { 'Content-Type': 'application/json' }
  });
}
```

### Authentication Patterns
```typescript
// User authentication
export async function POST({ locals, request }) {
  const { email, password } = await request.json();

  const { data, error } = await locals.supabase.auth.signInWithPassword({
    email,
    password
  });

  if (error) {
    return new Response(JSON.stringify({ error: error.message }), {
      status: 401
    });
  }

  // Set session cookie or return token
  return new Response(JSON.stringify({ user: data.user }));
}
```

## Data Validation

### Zod Schema Validation
Use Zod schemas to validate all data exchanged with the backend:

```typescript
import { z } from 'zod';

// Define validation schemas
const profileSchema = z.object({
  username: z.string().min(3).max(50),
  email: z.string().email(),
  bio: z.string().max(500).optional(),
  avatar_url: z.string().url().optional()
});

// Validate incoming data
export async function POST({ locals, request }) {
  try {
    const body = await request.json();
    const validatedData = profileSchema.parse(body);

    // Process validated data
    const { data, error } = await locals.supabase
      .from('profiles')
      .insert(validatedData)
      .select()
      .single();

    if (error) throw error;

    return new Response(JSON.stringify({ data }), {
      status: 201,
      headers: { 'Content-Type': 'application/json' }
    });

  } catch (error) {
    if (error instanceof z.ZodError) {
      return new Response(JSON.stringify({
        error: 'Validation failed',
        details: error.errors
      }), {
        status: 400,
        headers: { 'Content-Type': 'application/json' }
      });
    }

    return new Response(JSON.stringify({
      error: 'Internal server error'
    }), {
      status: 500
    });
  }
}
```

## Database Schema Design

### Naming Conventions
- Use snake_case for table and column names
- Use plural table names (e.g., `users`, `posts`, `comments`)
- Include created_at and updated_at timestamps
- Use foreign key relationships with proper naming

### Common Table Patterns
```sql
-- Users table
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email TEXT UNIQUE NOT NULL,
  username TEXT UNIQUE,
  avatar_url TEXT,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Posts table with foreign key
CREATE TABLE posts (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES users(id) ON DELETE CASCADE,
  title TEXT NOT NULL,
  content TEXT,
  published BOOLEAN DEFAULT false,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);
```

## Error Handling

### Database Error Handling
```typescript
const handleDatabaseError = (error: any) => {
  console.error('Database error:', error);

  switch (error.code) {
    case 'PGRST116': // Not found
      return { status: 404, message: 'Resource not found' };
    case '23505': // Unique constraint violation
      return { status: 409, message: 'Resource already exists' };
    case '23503': // Foreign key constraint violation
      return { status: 400, message: 'Invalid reference' };
    default:
      return { status: 500, message: 'Internal server error' };
  }
};
```

### API Response Patterns
```typescript
// Success response
const successResponse = (data: any, status = 200) => new Response(
  JSON.stringify({ success: true, data }),
  {
    status,
    headers: { 'Content-Type': 'application/json' }
  }
);

// Error response
const errorResponse = (message: string, status = 400, details?: any) => new Response(
  JSON.stringify({ success: false, error: message, details }),
  {
    status,
    headers: { 'Content-Type': 'application/json' }
  }
);
```

## Security Best Practices

### Row Level Security (RLS)
- Enable RLS on all tables, even public ones
- Create granular policies for different operations
- Use separate policies for different user roles

### API Security
```typescript
// Rate limiting middleware
export const onRequest = defineMiddleware(async (context, next) => {
  const clientIP = context.clientAddress;
  // Implement rate limiting logic here
  return next();
});

// Input sanitization
const sanitizeInput = (input: string): string => {
  return input.trim().replace(/[<>]/g, '');
};
```

### Environment Security
- Never expose sensitive environment variables to the client
- Use Supabase Row Level Security for data access control
- Implement proper CORS configuration

## Performance Optimization

### Database Queries
```typescript
// Efficient querying with select
const { data } = await supabase
  .from('posts')
  .select('id, title, created_at, users(username)')
  .eq('published', true)
  .order('created_at', { ascending: false })
  .limit(10);

// Use indexes for frequently queried columns
// Implement pagination for large datasets
```

### Caching Strategies
```typescript
// Implement caching for frequently accessed data
const getCachedData = async (key: string, fetcher: () => Promise<any>) => {
  // Check cache first
  // If not found, fetch and cache
  return fetcher();
};
```

## Testing Backend Code

### Unit Testing
```typescript
// Test database operations
import { describe, it, expect, beforeEach } from 'vitest';

describe('Database Operations', () => {
  beforeEach(async () => {
    // Setup test database state
  });

  it('should create a new user', async () => {
    const userData = { email: 'test@example.com', username: 'testuser' };
    // Test user creation
  });
});
```

### Integration Testing
- Test API endpoints with real database
- Test authentication flows
- Test error scenarios

## File Organization

```
src/
├── db/
│   ├── supabase.client.ts     # Supabase client configuration
│   ├── database.types.ts      # Generated database types
│   └── schemas/              # Zod validation schemas
├── middleware/
│   └── index.ts              # Astro middleware
├── lib/
│   ├── services/             # Business logic services
│   ├── utils/                # Utility functions
│   └── types.ts              # Shared types
└── pages/api/                # API endpoints
```

When working with backend code, always prioritize security, type safety, and proper error handling.