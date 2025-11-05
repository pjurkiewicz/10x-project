# Project Structure and Development Guidelines

You are working with a modern web application using the following tech stack:

## Technology Stack
- **Frontend Framework**: Astro 5 with React 19 integration
- **Language**: TypeScript 5
- **Styling**: Tailwind CSS 4
- **UI Components**: Shadcn/ui with "new-york" style
- **Backend**: Supabase (database, authentication)
- **Build Tool**: Vite
- **Icons**: Lucide React

## Project Structure

Follow this directory structure for all code organization:

```
src/
├── layouts/           # Astro layout components
├── pages/            # Astro pages and routes
│   └── api/          # API endpoints
├── components/       # React components
│   ├── ui/          # Shadcn/ui components
│   └── hooks/       # Custom React hooks
├── lib/             # Services, utilities, and helpers
├── db/              # Supabase clients and types
├── middleware/      # Astro middleware
├── styles/          # Global styles
├── types.ts         # Shared types for backend and frontend
└── env.d.ts         # TypeScript environment definitions
```

**Key Principles:**
- Use Astro components (.astro) for static content and layout
- Use React components (.tsx) only when interactivity is needed
- Extract reusable logic into services in `src/lib/`
- Store shared types in `src/types.ts`
- Use `src/db/` for Supabase client configurations

## Coding Practices

### Clean Code Guidelines
- Use feedback from linters to improve code quality
- Prioritize error handling and edge cases
- Handle errors and edge cases at the beginning of functions
- Use early returns for error conditions to avoid deeply nested if statements
- Place the happy path last in the function for improved readability
- Avoid unnecessary else statements; use if-return pattern instead
- Use guard clauses to handle preconditions and invalid states early
- Implement proper error logging and user-friendly error messages
- Consider using custom error types or error factories for consistent error handling

### Performance Guidelines
- Implement React.memo() for expensive components that render often with the same props
- Utilize React.lazy() and Suspense for code-splitting and performance optimization
- Use the useCallback hook for event handlers passed to child components
- Prefer useMemo for expensive calculations to avoid recomputation on every render
- Use useTransition for non-urgent state updates to keep the UI responsive

## Accessibility Standards

### ARIA Best Practices
- Use ARIA landmarks to identify regions of the page (main, navigation, search, etc.)
- Apply appropriate ARIA roles to custom interface elements that lack semantic HTML equivalents
- Set aria-expanded and aria-controls for expandable content like accordions and dropdowns
- Use aria-live regions with appropriate politeness settings for dynamic content updates
- Implement aria-hidden to hide decorative or duplicative content from screen readers
- Apply aria-label or aria-labelledby for elements without visible text labels
- Use aria-describedby to associate descriptive text with form inputs or complex elements
- Implement aria-current for indicating the current item in a set, navigation, or process
- Avoid redundant ARIA that duplicates the semantics of native HTML elements

When modifying the directory structure, update this documentation accordingly.