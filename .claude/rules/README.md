# Claude Code Rules - Migration from Cursor

## Overview

This directory contains Claude Code rules migrated from your original Cursor configuration. The migration consolidates and enhances your original 8 rule files into 7 comprehensive, Claude-optimized rule files.

## Rule Files

### 1. `project-structure.mdc`
**Scope**: Applied globally to all development work
**Content**:
- Technology stack overview (Astro 5, TypeScript 5, React 19, Tailwind 4, Shadcn/ui)
- Project directory structure and organization
- General coding practices and clean code principles
- Performance and accessibility standards

### 2. `astro-development.mdc`
**Scope**: Astro framework development
**Content**:
- Astro core principles and best practices
- Page routing and View Transitions API
- Content collections management
- API development with server endpoints
- Middleware implementation
- Asset management and image optimization
- Hybrid rendering strategies

### 3. `react-development.mdc`
**Scope**: React component development (.tsx files)
**Content**:
- React 19 features and best practices
- Component architecture with functional components
- Performance optimization (React.memo, useCallback, useMemo)
- Custom hooks development patterns
- State management strategies
- Event handling and form patterns
- Error boundaries and accessibility

### 4. `frontend-styling.mdc`
**Scope**: Frontend styling and UI development
**Content**:
- Tailwind CSS best practices and organization
- Component styling patterns
- Shadcn/ui integration and usage
- CSS custom properties and theming
- Responsive design strategies
- Animation and transitions
- Accessibility in styling

### 5. `backend-database.mdc`
**Scope**: Backend services and database operations
**Content**:
- Supabase integration patterns
- Client configuration and middleware setup
- Type-safe database operations
- Data validation with Zod schemas
- Authentication patterns
- Error handling strategies
- Security best practices

### 6. `database-migrations.mdc`
**Scope**: Database schema management
**Content**:
- Migration file naming conventions and structure
- Postgres and Supabase SQL standards
- Table creation and RLS (Row Level Security) policies
- Index and performance optimization
- Destructive operations with proper warnings
- Common migration patterns

### 7. `supabase-setup.mdc`
**Scope**: Complete Supabase integration setup
**Content**:
- Prerequisites and installation guide
- File structure setup with code examples
- Environment variable configuration
- Database type generation
- Usage examples for API routes, Astro pages, and React components
- Authentication implementation
- Security best practices and testing

## Migration Benefits

### Consolidated Structure
- **Before**: 8 separate rule files with some overlap
- **After**: 7 comprehensive rule files with clear separation of concerns

### Enhanced Content
- Added detailed code examples and patterns
- Improved error handling guidelines
- Enhanced security considerations
- More comprehensive testing recommendations

### Claude Optimization
- Better context organization for Claude's understanding
- Improved readability and navigation
- Structured for efficient rule application
- Enhanced with practical examples

## Usage

These rules will be automatically applied by Claude Code when working on your project. The rules provide:

1. **Consistent Code Quality**: Enforce your coding standards and best practices
2. **Project Structure Guidance**: Maintain proper file organization and architecture
3. **Technology-Specific Guidelines**: Tailored advice for Astro, React, Tailwind, and Supabase
4. **Security and Performance**: Built-in security and performance optimization patterns

## Backup Information

Your original Cursor rules have been backed up to:
`.cursor/rules-backup-[timestamp]/`

You can safely remove these once you're satisfied with the Claude rules migration.

## Support

If you need to modify or add rules:
- Edit the appropriate `.mdc` file in this directory
- Follow the established structure and formatting
- Test changes with Claude Code to ensure proper application

The migration preserves all your original guidelines while making them more accessible and effective for Claude Code assistance.