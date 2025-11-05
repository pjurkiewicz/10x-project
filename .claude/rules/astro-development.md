# Astro Framework Development Guidelines

## Core Astro Principles

### Page Routing and Navigation
- Leverage View Transitions API for smooth page transitions (use ClientRouter)
- Implement proper navigation structure with Astro's file-based routing
- Use `<a>` tags for navigation; View Transitions will automatically handle SPA-like behavior

### Content Management
- Use content collections with type safety for blog posts, documentation, and structured content
- Define content schemas in `src/content/config.ts` for type safety
- Leverage `getCollection()` and `getEntry()` for content retrieval

### API Development
- Use Server Endpoints for API routes in `src/pages/api/`
- Use HTTP methods in uppercase format: `GET`, `POST`, `PUT`, `DELETE`
- Set `export const prerender = false` for dynamic API routes
- Use Zod for input validation in all API endpoints
- Extract business logic into services in `src/lib/services/`

### Middleware Implementation
- Implement middleware for request/response modification in `src/middleware/`
- Use middleware for authentication, logging, and request transformation
- Access middleware data via `Astro.locals` in routes and components

### Asset Management
- Use image optimization with the Astro Image integration
- Implement proper image formats (WebP, AVIF) with fallbacks
- Use `import.meta.env` for environment variables
- Store static assets in `public/` directory

### Rendering Strategy
- Implement hybrid rendering with server-side rendering where needed
- Use `export const prerender = true` for static pages when appropriate
- Leverage Astro's island architecture for interactive components

### Cookie and Session Management
- Use `Astro.cookies` for server-side cookie management
- Implement secure cookie practices (httpOnly, secure, sameSite)
- Use `context.locals` for passing data between middleware and routes

### Performance Optimization
- Use partial rendering for large pages
- Implement proper caching strategies
- Leverage Astro's built-in performance features
- Use `<ViewTransitions>` for smooth navigation

### Integration with React
- Import React components only when interactivity is needed
- Use `client:load`, `client:idle`, or `client:visible` directives appropriately
- Never use "use client" or other Next.js directives
- Keep React components focused on interactivity, not content

### Error Handling
- Implement proper error boundaries in React components
- Use Astro's error pages for route-level error handling
- Provide meaningful error messages and fallbacks

### Development Best Practices
- Use TypeScript for all Astro files
- Follow consistent file naming conventions
- Implement proper SEO meta tags
- Use Astro's built-in accessibility features
- Test both server and client-side functionality

### Security Considerations
- Validate all user inputs with Zod schemas
- Use environment variables for sensitive data
- Implement proper CORS configuration if needed
- Follow secure cookie practices

## File Patterns
- **Layout files**: `src/layouts/*.astro`
- **Page files**: `src/pages/**/*.astro`
- **API routes**: `src/pages/api/**/*.ts`
- **Content collections**: `src/content/**/*`
- **Middleware**: `src/middleware/**/*.ts`

When working with Astro files, always prioritize the framework's strengths: content-focused development, performance optimization, and progressive enhancement.