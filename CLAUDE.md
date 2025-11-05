# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a 10x Astro Starter template - a modern, opinionated starter for building fast, accessible, and AI-friendly web applications using Astro v5.5.5, React 19, TypeScript 5, and Tailwind CSS 4.0.17.

## Tech Stack

- **Astro** v5.5.5 - Modern web framework for building fast, content-focused websites
- **React** v19.1.1 - UI library for building interactive components
- **TypeScript** v5 - Type-safe JavaScript
- **Tailwind CSS** v4.1.13 - Utility-first CSS framework
- **shadcn/ui** - Component library with Radix UI primitives
- **Node.js** v22.14.0 (specified in `.nvmrc`)

## Development Commands

```bash
# Development
npm run dev          # Start development server on port 3000
npm run build        # Build for production
npm run preview      # Preview production build
npm run astro        # Direct Astro CLI access

# Code Quality
npm run lint         # Run ESLint
npm run lint:fix     # Fix ESLint issues automatically
npm run format       # Format code with Prettier
```

## Project Architecture

### Directory Structure

```
src/
├── layouts/         # Astro layouts (e.g., Layout.astro)
├── pages/           # Astro pages and routes
│   └── api/         # API endpoints (server-side)
├── components/      # UI components (Astro & React)
│   └── ui/          # shadcn/ui components
├── lib/             # Services and utilities (e.g., utils.ts)
├── assets/          # Static internal assets
└── styles/          # Global styles (e.g., global.css)
```

### Key Configuration

- **Astro Config**: Server-side rendering with Node.js adapter (standalone mode)
- **Path Aliases**: `@/*` maps to `./src/*` for cleaner imports
- **Component System**:
  - Astro components (`.astro`) for static content and layouts
  - React components (`.tsx`) for interactive UI
  - shadcn/ui components in `src/components/ui/`

### Development Patterns

- **Component Organization**: Use shadcn/ui components with `cn()` utility for class merging
- **Styling**: Tailwind CSS with CSS variables and dark mode support
- **Type Safety**: Strict TypeScript configuration with Astro types
- **Code Quality**: ESLint + Prettier with pre-commit hooks via Husky

## AI Development Tools Integration

This project includes AI development configurations for:
- **Claude Code**: Detailed rules in `.claude/rules/` directory covering:
  - `astro-development.md` - Astro-specific development guidelines
  - `backend-database.md` - Backend and database patterns
  - `database-migrations.md` - Database migration strategies
  - `frontend-styling.md` - Frontend styling approaches
  - `project-structure.md` - Project structure conventions
  - `react-development.md` - React development best practices
  - `supabase-setup.md` - Supabase integration guidelines
- **Cursor IDE**: Rules in `.cursor/rules/` directory
- **GitHub Copilot**: Instructions in `.github/copilot-instructions.md`
- **Windsurf**: Configuration in `.windsurfrules`

## Important Notes

- Server runs on port 3000 by default
- Uses standalone Node.js adapter for production deployment
- Includes comprehensive linting setup (ESLint + TypeScript + React + Astro + Accessibility)
- Prettier formatting with Astro plugin support
- Path aliases configured for cleaner imports (`@/` for `src/`)