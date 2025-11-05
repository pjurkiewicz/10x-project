# React 19 Development Guidelines

## Component Architecture

### Functional Components Only
- Use functional components with hooks exclusively - no class components
- Never use "use client" or other Next.js directives (we use React with Astro)
- Keep components focused and single-purpose
- Use composition over inheritance

### Custom Hooks
- Extract reusable logic into custom hooks in `src/components/hooks/`
- Name custom hooks starting with "use" (e.g., `useUserData`, `useApiCall`)
- Keep hooks pure and side-effect free when possible
- Return consistent data structures from hooks

## Performance Optimization

### React.memo for Component Memoization
```tsx
const ExpensiveComponent = React.memo(({ data, onUpdate }) => {
  // Component implementation
}, (prevProps, nextProps) => {
  // Custom comparison function
  return prevProps.data.id === nextProps.data.id;
});
```

### Code Splitting with React.lazy
```tsx
const LazyComponent = React.lazy(() => import('./LazyComponent'));

// Use with Suspense
<Suspense fallback={<div>Loading...</div>}>
  <LazyComponent />
</Suspense>
```

### useCallback for Event Handlers
```tsx
const handleClick = useCallback((itemId: string) => {
  onItemClick(itemId);
}, [onItemClick]);
```

### useMemo for Expensive Calculations
```tsx
const expensiveValue = useMemo(() => {
  return computeExpensiveValue(data);
}, [data]);
```

## React 19 Features

### useOptimistic Hook
```tsx
const [optimisticMessages, addOptimisticMessage] = useOptimistic(
  messages,
  (state, newMessage) => [...state, newMessage]
);
```

### useTransition for Non-urgent Updates
```tsx
const [isPending, startTransition] = useTransition();

const handleUpdate = () => {
  startTransition(() => {
    // Non-urgent state update
    setFilters(newFilters);
  });
};
```

### useId for Unique IDs
```tsx
const id = useId();
return (
  <>
    <label htmlFor={id}>Label</label>
    <input id={id} type="text" />
  </>
);
```

## State Management

### Local State
- Use `useState` for simple component state
- Use `useReducer` for complex state logic
- Consider external state management for global state

### State Patterns
```tsx
// Loading states
const [data, setData] = useState(null);
const [isLoading, setIsLoading] = useState(false);
const [error, setError] = useState(null);

// Form states
const [formData, setFormData] = useState(initialState);
const [isSubmitting, setIsSubmitting] = useState(false);
```

## Side Effects

### useEffect Guidelines
- Include all dependencies in the dependency array
- Use cleanup functions for subscriptions and timers
- Separate effects by their concerns

### Custom Effect Hooks
```tsx
const useApiData = (url: string) => {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    let isMounted = true;

    const fetchData = async () => {
      try {
        const response = await fetch(url);
        const result = await response.json();
        if (isMounted) {
          setData(result);
        }
      } catch (err) {
        if (isMounted) {
          setError(err);
        }
      } finally {
        if (isMounted) {
          setLoading(false);
        }
      }
    };

    fetchData();

    return () => {
      isMounted = false;
    };
  }, [url]);

  return { data, loading, error };
};
```

## Event Handling

### Event Handler Best Practices
- Use `useCallback` for event handlers passed to child components
- Handle events asynchronously when needed
- Provide meaningful error handling for user interactions

### Form Handling
```tsx
const useForm = (initialValues: FormData) => {
  const [values, setValues] = useState(initialValues);
  const [errors, setErrors] = useState({});

  const handleChange = useCallback((e: React.ChangeEvent<HTMLInputElement>) => {
    const { name, value } = e.target;
    setValues(prev => ({ ...prev, [name]: value }));
  }, []);

  const handleSubmit = useCallback(async (e: React.FormEvent) => {
    e.preventDefault();
    // Form submission logic
  }, [values]);

  return { values, errors, handleChange, handleSubmit };
};
```

## Accessibility

### ARIA Implementation
- Use semantic HTML elements when possible
- Implement proper ARIA attributes for custom components
- Ensure keyboard navigation works
- Use `aria-live` regions for dynamic content announcements

### Focus Management
```tsx
const useFocusManagement = () => {
  const [focusedElement, setFocusedElement] = useState<HTMLElement | null>(null);

  const setFocus = useCallback((element: HTMLElement | null) => {
    if (element) {
      element.focus();
      setFocusedElement(element);
    }
  }, []);

  return { focusedElement, setFocus };
};
```

## Error Handling

### Error Boundaries
```tsx
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    console.error('Error caught by boundary:', error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return <h1>Something went wrong.</h1>;
    }

    return this.props.children;
  }
}
```

## Testing Considerations
- Write testable components with clear inputs and outputs
- Mock external dependencies in tests
- Test user interactions and state changes
- Use React Testing Library for component testing

## File Organization
- Component files: `src/components/**/*.tsx`
- Custom hooks: `src/components/hooks/**/*.ts`
- Component tests: `src/components/**/*.test.tsx`
- Storybook stories: `src/components/**/*.stories.tsx`

When developing React components, always prioritize performance, accessibility, and maintainability.