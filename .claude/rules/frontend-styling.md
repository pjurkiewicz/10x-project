# Frontend Styling and UI Development Guidelines

## Tailwind CSS Best Practices

### Layer Organization
Use the `@layer` directive to organize styles into base, components, and utilities layers:

```css
@layer base {
  html {
    font-family: 'Inter', sans-serif;
  }
}

@layer components {
  .btn-primary {
    @apply bg-blue-600 text-white px-4 py-2 rounded-md hover:bg-blue-700;
  }
}

@layer utilities {
  .text-shadow {
    text-shadow: 0 2px 4px rgba(0,0,0,0.1);
  }
}
```

### Arbitrary Values
Use square brackets for precise one-off designs:
```tsx
<div className="w-[123px] h-[45px] bg-[#1a1a1a]">
```

### Responsive Design
Use responsive variants for adaptive designs:
```tsx
<div className="w-full md:w-1/2 lg:w-1/3 xl:w-1/4">
```

### State Variants
Leverage state variants for interactive elements:
```tsx
<button className="bg-blue-500 hover:bg-blue-600 focus-visible:ring-2 focus-visible:ring-blue-500 active:bg-blue-700">
  Click me
</button>
```

### Dark Mode Implementation
Use the `dark:` variant for dark mode support:
```tsx
<div className="bg-white dark:bg-gray-900 text-gray-900 dark:text-gray-100">
```

### Theme Configuration
Access Tailwind theme values using the `theme()` function in CSS:
```css
.custom-component {
  background-color: theme('colors.blue.500');
  padding: theme('spacing.4');
}
```

## Component Architecture

### Static vs Dynamic Components
- **Use Astro components (.astro)** for static content and layout
- **Use React components (.tsx)** only when interactivity is needed
- Keep React components focused on user interactions

### Component Styling Patterns

#### Consistent Spacing
```tsx
// Use consistent spacing scale
<div className="space-y-4"> {/* Vertical spacing */}
<div className="space-x-2"> {/* Horizontal spacing */}
<div className="gap-4 grid grid-cols-2"> {/* Grid gaps */}
```

#### Typography
```tsx
<h1 className="text-4xl font-bold tracking-tight">Title</h1>
<p className="text-lg text-gray-600 dark:text-gray-400">Description</p>
<span className="text-sm font-medium">Label</span>
```

#### Layout Patterns
```tsx
// Flexbox layouts
<div className="flex items-center justify-between">
<div className="flex flex-col space-y-2">

// Grid layouts
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">

// Container patterns
<div className="container mx-auto px-4 sm:px-6 lg:px-8">
<div className="max-w-7xl mx-auto">
```

## Shadcn/ui Integration

### Component Usage
Import components using the configured `@/` alias:

```tsx
import { Button } from "@/components/ui/button";
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs";
import { Card, CardContent, CardDescription, CardFooter, CardHeader, CardTitle } from "@/components/ui/card";
```

### Component Styling
This project uses the "new-york" style variant with "neutral" base color and CSS variables for theming.

### Available Components
Commonly used components include:
- Button, Input, Textarea
- Card, Dialog, Sheet
- Tabs, Accordion, Collapsible
- Form, Select, Checkbox
- Table, Pagination
- Navigation Menu, Breadcrumb
- Toast (Sonner), Alert
- Progress, Skeleton
- Avatar, Badge

### Installing New Components
Use the shadcn CLI to install additional components:
```bash
npx shadcn@latest add [component-name]
```

Example:
```bash
npx shadcn@latest add accordion
npx shadcn@latest add data-table
```

## CSS Custom Properties

### Theme Variables
Use CSS custom properties for consistent theming:
```css
:root {
  --background: 0 0% 100%;
  --foreground: 222.2 84% 4.9%;
  --card: 0 0% 100%;
  --card-foreground: 222.2 84% 4.9%;
  --popover: 0 0% 100%;
  --popover-foreground: 222.2 84% 4.9%;
  --primary: 221.2 83.2% 53.3%;
  --primary-foreground: 210 40% 98%;
  --secondary: 210 40% 96%;
  --secondary-foreground: 222.2 84% 4.9%;
  --muted: 210 40% 96%;
  --muted-foreground: 215.4 16.3% 46.9%;
  --accent: 210 40% 96%;
  --accent-foreground: 222.2 84% 4.9%;
  --destructive: 0 84.2% 60.2%;
  --destructive-foreground: 210 40% 98%;
  --border: 214.3 31.8% 91.4%;
  --input: 214.3 31.8% 91.4%;
  --ring: 221.2 83.2% 53.3%;
  --radius: 0.5rem;
}

.dark {
  --background: 222.2 84% 4.9%;
  --foreground: 210 40% 98%;
  /* ... other dark mode variables */
}
```

## Animation and Transitions

### Smooth Transitions
```tsx
<button className="transition-all duration-200 ease-in-out hover:scale-105">
  Animated Button
</button>
```

### Page Transitions
Use Astro's View Transitions for smooth page navigation:
```astro
<template transition:animate="fade" />
```

### Component Animations
```tsx
<div className="transition-opacity duration-300 ease-in-out">
  {isVisible && <Content />}
</div>
```

## Responsive Design Strategy

### Mobile-First Approach
```tsx
<div className="w-full p-4 md:p-6 lg:p-8">
  <h1 className="text-xl md:text-2xl lg:text-3xl">
    Responsive heading
  </h1>
</div>
```

### Breakpoint Usage
- `sm`: 640px and up
- `md`: 768px and up
- `lg`: 1024px and up
- `xl`: 1280px and up
- `2xl`: 1536px and up

## Performance Considerations

### CSS Optimization
- Use Tailwind's purging capabilities to remove unused CSS
- Minimize custom CSS in favor of utility classes
- Use CSS-in-JS sparingly for dynamic styles

### Image Optimization
- Use Astro's Image integration for automatic optimization
- Implement proper lazy loading for images
- Use modern image formats (WebP, AVIF) with fallbacks

## Accessibility in Styling

### Focus Styles
```tsx
<button className="focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-blue-500">
  Accessible button
</button>
```

### Color Contrast
- Ensure text meets WCAG AA contrast ratios (4.5:1 for normal text)
- Use tools to verify color combinations
- Provide sufficient color contrast for interactive elements

### Reduced Motion
```css
@media (prefers-reduced-motion: reduce) {
  .animated-element {
    animation: none;
    transition: none;
  }
}
```

## Development Workflow

### Component Development
1. Start with mobile layout
2. Enhance for larger screens
3. Test with accessibility tools
4. Verify dark mode compatibility
5. Check performance impact

### Code Organization
- Keep component-specific styles with the component
- Use utility classes for one-off styles
- Extract repeated patterns into component classes
- Maintain consistent naming conventions

When working with frontend styling, always prioritize maintainability, performance, and accessibility.