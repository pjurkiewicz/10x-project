# Database Migration Guidelines

## Overview

You are a Postgres Expert creating secure database schemas for Supabase. This project uses Supabase CLI migrations for database schema management.

## Migration File Creation

### File Naming Convention
Migration files must follow this exact naming format:
```
YYYYMMDDHHmmss_short_description.sql
```

**Format breakdown:**
1. `YYYY` - Four digits for the year (e.g., `2024`)
2. `MM` - Two digits for the month (01-12)
3. `DD` - Two digits for the day (01-31)
4. `HH` - Two digits for the hour in 24-hour format (00-23)
5. `mm` - Two digits for the minute (00-59)
6. `ss` - Two digits for the second (00-59)
7. `short_description` - Snake_case description of the migration

**Examples:**
```
20240906123045_create_profiles.sql
20240906124530_add_user_avatars.sql
20240906130000_enable_rls_on_posts.sql
```

### File Location
All migration files must be created in: `supabase/migrations/`

## SQL Writing Standards

### General Guidelines
- Write all SQL in lowercase for consistency
- Include thorough comments explaining each migration step
- Add header comments with migration metadata
- Use clear, descriptive table and column names
- Follow Postgres best practices and Supabase conventions

### Migration File Template
```sql
-- Migration: create_users_table
-- Description: Creates the users table with authentication integration
-- Author: Generated for [project name]
-- Created: 2024-09-06 12:30:45 UTC

-- Create users table
create table public.users (
  id uuid primary key default gen_random_uuid(),
  email text unique not null,
  username text unique,
  full_name text,
  avatar_url text,
  created_at timestamp with time zone default now(),
  updated_at timestamp with time zone default now()
);

-- Create indexes for performance
create index idx_users_email on public.users(email);
create index idx_users_username on public.users(username);

-- Enable Row Level Security
alter table public.users enable row level security;

-- Create RLS policies
create policy "Users can view their own profile" on public.users
  for select using (auth.uid() = id);

create policy "Users can update their own profile" on public.users
  for update using (auth.uid() = id);

create policy "Users can insert their own profile" on public.users
  for insert with check (auth.uid() = id);
```

## Table Creation Standards

### Required Columns
Every table should include:
```sql
create table public.table_name (
  id uuid primary key default gen_random_uuid(),
  created_at timestamp with time zone default now(),
  updated_at timestamp with time zone default now()
  -- other columns...
);
```

### Foreign Key Relationships
```sql
-- Reference to users table
user_id uuid references public.users(id) on delete cascade,
```

### Common Data Types
```sql
-- Text types
email text unique not null,
username text,
description text,

-- Boolean types
is_active boolean default true,
is_published boolean default false,

-- Timestamp types
created_at timestamp with time zone default now(),
updated_at timestamp with time zone default now(),
deleted_at timestamp with time zone, -- for soft deletes

-- JSON types
metadata jsonb default '{}',
settings jsonb default '{}',
```

## Row Level Security (RLS)

### Mandatory RLS
**CRITICAL**: Always enable Row Level Security on every table, even if the table is intended for public access.

```sql
-- Enable RLS on the table
alter table public.posts enable row level security;
```

### Policy Creation Patterns

#### Public Access Tables
```sql
-- For tables that should be publicly readable
create policy "Public read access" on public.posts
  for select using (true);

-- For tables that allow anonymous inserts
create policy "Public insert access" on public.posts
  for insert with check (true);
```

#### User-Restricted Tables
```sql
-- Users can only access their own data
create policy "Users can view own records" on public.user_settings
  for select using (auth.uid() = user_id);

create policy "Users can update own records" on public.user_settings
  for update using (auth.uid() = user_id);

create policy "Users can insert own records" on public.user_settings
  for insert with check (auth.uid() = user_id);
```

#### Role-Based Access
```sql
-- Admin access
create policy "Admin full access" on public.posts
  for all using (
    auth.jwt() ->> 'role' = 'admin'
  );

-- Authenticated user access
create policy "Authenticated user read access" on public.posts
  for select using (
    auth.role() = 'authenticated'
  );
```

### Policy Granularity
**IMPORTANT**: Create separate policies for each operation type and user role. Do not combine multiple operations in a single policy.

```sql
-- GOOD: Separate policies
create policy "Select for anonymous users" on public.posts
  for select using (auth.role() = 'anon');

create policy "Select for authenticated users" on public.posts
  for select using (auth.role() = 'authenticated');

create policy "Insert for authenticated users" on public.posts
  for insert with check (auth.role() = 'authenticated');

-- BAD: Combined policy
create policy "Full access for all users" on public.posts
  for all using (true); -- Too permissive and unclear
```

## Destructive Operations

### Required Warnings
For any destructive operations, include clear warnings and comments:

```sql
-- WARNING: This migration will delete data and cannot be undone
-- Destructive operation: Dropping the old_user_data column
-- Impact: All data in old_user_data will be permanently lost

-- Backup recommendation: Export data before running this migration
alter table public.users drop column old_user_data;
```

### Common Destructive Operations
```sql
-- WARNING: Dropping table - all data will be permanently lost
drop table public.temp_data;

-- WARNING: Dropping column - all data in this column will be lost
alter table public.users drop column deprecated_field;

-- WARNING: Changing column type - may cause data loss
alter table public.posts alter column content type text using content::text;

-- WARNING: Truncating table - all data will be deleted
truncate table public.logs;
```

## Indexes and Performance

### Creating Indexes
```sql
-- Single column indexes
create index idx_users_email on public.users(email);
create index idx_posts_published on public.posts(published);

-- Composite indexes
create index idx_posts_user_created on public.posts(user_id, created_at);
create index idx_posts_status_priority on public.posts(status, priority);

-- Unique indexes
create unique index idx_users_email_unique on public.users(email);
```

### When to Create Indexes
- Foreign key columns
- Columns frequently used in WHERE clauses
- Columns used in ORDER BY clauses
- Columns used in JOIN conditions
- Columns with unique constraints

## Functions and Triggers

### Updated Timestamp Trigger
```sql
-- Create updated_at trigger function
create or replace function public.handle_updated_at()
returns trigger as $$
begin
  new.updated_at = now();
  return new;
end;
$$ language plpgsql;

-- Create trigger for tables with updated_at column
create trigger handle_updated_at
  before update on public.posts
  for each row
  execute procedure public.handle_updated_at();
```

## Data Validation

### Check Constraints
```sql
-- Email format validation
alter table public.users add constraint valid_email
  check (email ~* '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$');

-- Age validation
alter table public.profiles add constraint valid_age
  check (age >= 0 and age <= 150);

-- Username format
alter table public.users add constraint valid_username
  check (username ~* '^[a-z0-9_]{3,30}$');
```

## Migration Best Practices

### Before Creating Migration
1. **Plan the changes**: Understand the impact on existing data
2. **Test locally**: Use local Supabase instance for testing
3. **Consider backwards compatibility**: Will this break existing code?
4. **Plan rollback strategy**: How to undo if something goes wrong

### After Creating Migration
1. **Test thoroughly**: Verify the migration works as expected
2. **Check RLS policies**: Ensure security policies work correctly
3. **Validate data**: Confirm data integrity is maintained
4. **Update application code**: Modify code to work with new schema

### Migration Comments
Always include comprehensive comments:
```sql
-- Migration: add_user_profiles
-- Description: Extends the users table with profile information
-- Dependencies: users table must exist
-- Impact: Non-breaking change, adds optional columns
-- Testing: Verify RLS policies still work correctly
```

## Common Migration Patterns

### Adding New Table
```sql
-- Standard table creation with RLS
create table public.categories (
  id uuid primary key default gen_random_uuid(),
  name text unique not null,
  description text,
  color text default '#6B7280',
  created_at timestamp with time zone default now(),
  updated_at timestamp with time zone default now()
);

-- Enable RLS
alter table public.categories enable row level security;

-- Public read access
create policy "Public read access" on public.categories
  for select using (true);

-- Admin insert/update access
create policy "Admin full access" on public.categories
  for all using (auth.jwt() ->> 'role' = 'admin');
```

### Adding Column to Existing Table
```sql
-- Add new column with default value
alter table public.posts add column featured boolean default false;

-- Add new nullable column
alter table public.users add column bio text;

-- Add new column with constraint
alter table public.posts add column priority integer default 0
  check (priority >= 0 and priority <= 10);
```

Remember: All generated SQL code should be production-ready, well-documented, and aligned with Supabase's best practices.