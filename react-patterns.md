# React and component patterns

This guide covers React patterns and component structure for PolicyEngine applications.

## Tech stack

### Core
- **React 19+** with hooks and functional components
- **TypeScript** for type safety
- **Next.js 14+** with App Router

### State management
- **TanStack React Query** for server state
- **Redux Toolkit** for complex client state (when needed)
- **Zustand** for simple client state (alternative)

### UI components
- **Mantine 8.x** as primary component library
- **@tabler/icons-react** for icons

### Styling
- **Mantine's styling system** with design tokens
- **No CSS modules** - use Mantine props and inline styles

## Component structure

### File organization

```
components/
├── common/           # Reusable components
│   ├── Button.tsx
│   ├── Card.tsx
│   └── DataTable.tsx
├── layout/           # Layout components
│   ├── Header.tsx
│   ├── Sidebar.tsx
│   └── Footer.tsx
├── features/         # Feature-specific components
│   ├── simulation/
│   ├── policy/
│   └── report/
└── shared/           # Shared utilities
    ├── BaseModal.tsx
    └── MarkdownRenderer.tsx
```

### Component anatomy

```typescript
import { useState } from 'react';
import { Box, Button, Text } from '@mantine/core';
import { IconCheck } from '@tabler/icons-react';

interface MyComponentProps {
  title: string;
  onSave?: () => void;
  children?: React.ReactNode;
}

export default function MyComponent({
  title,
  onSave,
  children,
}: MyComponentProps) {
  const [isActive, setIsActive] = useState(false);

  const handleClick = () => {
    setIsActive(!isActive);
    onSave?.();
  };

  return (
    <Box p="md">
      <Text size="lg" fw={600} mb="sm">
        {title}
      </Text>
      {children}
      <Button
        leftSection={<IconCheck size={16} />}
        onClick={handleClick}
        variant={isActive ? 'filled' : 'light'}
      >
        Save
      </Button>
    </Box>
  );
}
```

### Component guidelines

1. **Use functional components** with hooks
2. **Export default** for page/feature components
3. **Export named** for utility components
4. **Define Props interface** above component
5. **Destructure props** in function signature
6. **Use sentence case** for all user-facing text
7. **Keep components focused** - single responsibility

## Styling patterns

### Use Mantine props

```typescript
// Good: Use Mantine props
<Box
  p="md"
  bg="gray.50"
  c="gray.700"
  style={{ borderRadius: '8px' }}
>
  Content
</Box>

// Avoid: CSS modules
import styles from './Component.module.css';
<div className={styles.container}>Content</div>
```

### Design tokens

Import from `@/designTokens`:

```typescript
import { colors, spacing, typography } from '@/designTokens';

<Box
  style={{
    backgroundColor: colors.background.secondary,
    padding: spacing.lg,
    fontFamily: typography.fontFamily.primary,
  }}
>
  Content
</Box>
```

### Responsive styling

```typescript
<Box
  p={{ base: 'sm', sm: 'md', md: 'lg' }}
  w={{ base: '100%', md: '80%', lg: '60%' }}
>
  Content
</Box>
```

### Conditional styling

```typescript
<Card
  variant={isActive ? 'cardList--active' : 'cardList--inactive'}
  style={{
    cursor: 'pointer',
    transition: 'all 0.2s ease',
  }}
>
  Content
</Card>
```

## State management

### Server state (React Query)

```typescript
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { simulationsAPI } from '@/api/v2/simulations';

function SimulationView({ id }: { id: string }) {
  const queryClient = useQueryClient();

  // Fetch data
  const { data, isLoading, error } = useQuery({
    queryKey: ['simulation', id],
    queryFn: () => simulationsAPI.get(id),
  });

  // Update data
  const updateMutation = useMutation({
    mutationFn: (updates: any) => simulationsAPI.update(id, updates),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['simulation', id] });
    },
  });

  if (isLoading) return <LoadingOverlay visible />;
  if (error) return <Text c="red">Error loading simulation</Text>;

  return (
    <Box>
      <Text>{data.name}</Text>
      <Button onClick={() => updateMutation.mutate({ name: 'New name' })}>
        Update
      </Button>
    </Box>
  );
}
```

### Client state (hooks)

For simple local state, use useState:

```typescript
function Counter() {
  const [count, setCount] = useState(0);

  return (
    <Box>
      <Text>{count}</Text>
      <Button onClick={() => setCount(count + 1)}>Increment</Button>
    </Box>
  );
}
```

For shared state, use context:

```typescript
// contexts/UserContext.tsx
import { createContext, useContext, useState } from 'react';

interface UserContextType {
  userId: string;
  setUserId: (id: string) => void;
}

const UserContext = createContext<UserContextType | undefined>(undefined);

export function UserProvider({ children }: { children: React.ReactNode }) {
  const [userId, setUserId] = useState('');

  return (
    <UserContext.Provider value={{ userId, setUserId }}>
      {children}
    </UserContext.Provider>
  );
}

export function useUser() {
  const context = useContext(UserContext);
  if (!context) throw new Error('useUser must be used within UserProvider');
  return context;
}
```

### Global state (Redux - when necessary)

```typescript
// store/slices/uiSlice.ts
import { createSlice } from '@reduxjs/toolkit';

interface UIState {
  sidebarOpen: boolean;
}

const initialState: UIState = {
  sidebarOpen: true,
};

const uiSlice = createSlice({
  name: 'ui',
  initialState,
  reducers: {
    toggleSidebar: (state) => {
      state.sidebarOpen = !state.sidebarOpen;
    },
  },
});

export const { toggleSidebar } = uiSlice.actions;
export default uiSlice.reducer;

// In component
import { useDispatch, useSelector } from 'react-redux';
import { toggleSidebar } from '@/store/slices/uiSlice';

function Header() {
  const dispatch = useDispatch();
  const sidebarOpen = useSelector((state: RootState) => state.ui.sidebarOpen);

  return (
    <Button onClick={() => dispatch(toggleSidebar())}>
      {sidebarOpen ? 'Hide' : 'Show'} sidebar
    </Button>
  );
}
```

## Custom hooks

### Data fetching hook

```typescript
// hooks/useSimulation.ts
import { useQuery } from '@tanstack/react-query';
import { simulationsAPI } from '@/api/v2/simulations';

export function useSimulation(id: string) {
  return useQuery({
    queryKey: ['simulation', id],
    queryFn: () => simulationsAPI.get(id),
    staleTime: 5 * 60 * 1000, // 5 minutes
  });
}

// Usage
function Component() {
  const { data, isLoading } = useSimulation('abc123');
  // ...
}
```

### Form hook

```typescript
// hooks/useForm.ts
import { useState } from 'react';

export function useForm<T>(initialValues: T) {
  const [values, setValues] = useState<T>(initialValues);
  const [errors, setErrors] = useState<Partial<Record<keyof T, string>>>({});

  const handleChange = (field: keyof T, value: any) => {
    setValues(prev => ({ ...prev, [field]: value }));
    setErrors(prev => ({ ...prev, [field]: undefined }));
  };

  const reset = () => {
    setValues(initialValues);
    setErrors({});
  };

  return { values, errors, setErrors, handleChange, reset };
}

// Usage
function SignupForm() {
  const { values, handleChange } = useForm({
    email: '',
    password: '',
  });

  return (
    <Box>
      <TextInput
        label="Email"
        value={values.email}
        onChange={(e) => handleChange('email', e.currentTarget.value)}
      />
      <TextInput
        label="Password"
        type="password"
        value={values.password}
        onChange={(e) => handleChange('password', e.currentTarget.value)}
      />
    </Box>
  );
}
```

## Common patterns

### Loading states

```typescript
function DataComponent({ id }: { id: string }) {
  const { data, isLoading, error } = useQuery({
    queryKey: ['data', id],
    queryFn: () => fetchData(id),
  });

  if (isLoading) {
    return <LoadingOverlay visible />;
  }

  if (error) {
    return (
      <Text c="red" ta="center" p="xl">
        Error loading data: {error.message}
      </Text>
    );
  }

  return <DataView data={data} />;
}
```

### Empty states

```typescript
function ListComponent({ items }: { items: any[] }) {
  if (items.length === 0) {
    return (
      <Stack align="center" justify="center" p="xl">
        <Text size="lg" c="dimmed">No items found</Text>
        <Button onClick={handleCreate}>Create item</Button>
      </Stack>
    );
  }

  return (
    <Stack>
      {items.map(item => <ItemCard key={item.id} item={item} />)}
    </Stack>
  );
}
```

### Modal pattern

```typescript
import { Modal, Button } from '@mantine/core';
import { useState } from 'react';

function ModalExample() {
  const [opened, setOpened] = useState(false);

  return (
    <>
      <Button onClick={() => setOpened(true)}>Open modal</Button>
      <Modal
        opened={opened}
        onClose={() => setOpened(false)}
        title="Confirm action"
      >
        <Text mb="md">Are you sure?</Text>
        <Group justify="flex-end">
          <Button variant="subtle" onClick={() => setOpened(false)}>
            Cancel
          </Button>
          <Button onClick={handleConfirm}>Confirm</Button>
        </Group>
      </Modal>
    </>
  );
}
```

### Tabs pattern

```typescript
import { Tabs } from '@mantine/core';

function TabbedView() {
  return (
    <Tabs defaultValue="overview">
      <Tabs.List>
        <Tabs.Tab value="overview">Overview</Tabs.Tab>
        <Tabs.Tab value="details">Details</Tabs.Tab>
        <Tabs.Tab value="settings">Settings</Tabs.Tab>
      </Tabs.List>

      <Tabs.Panel value="overview" pt="md">
        <OverviewContent />
      </Tabs.Panel>
      <Tabs.Panel value="details" pt="md">
        <DetailsContent />
      </Tabs.Panel>
      <Tabs.Panel value="settings" pt="md">
        <SettingsContent />
      </Tabs.Panel>
    </Tabs>
  );
}
```

### List with actions

```typescript
function ItemList({ items }: { items: Item[] }) {
  const deleteMutation = useMutation({
    mutationFn: (id: string) => itemsAPI.delete(id),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['items'] });
    },
  });

  return (
    <Stack>
      {items.map(item => (
        <Card key={item.id} variant="cardList--inactive">
          <Group justify="space-between">
            <div>
              <Text fw={500}>{item.name}</Text>
              <Text size="sm" c="dimmed">{item.description}</Text>
            </div>
            <Menu>
              <Menu.Target>
                <ActionIcon variant="subtle">
                  <IconDotsVertical size={16} />
                </ActionIcon>
              </Menu.Target>
              <Menu.Dropdown>
                <Menu.Item leftSection={<IconEdit size={14} />}>
                  Edit
                </Menu.Item>
                <Menu.Item
                  color="red"
                  leftSection={<IconTrash size={14} />}
                  onClick={() => deleteMutation.mutate(item.id)}
                >
                  Delete
                </Menu.Item>
              </Menu.Dropdown>
            </Menu>
          </Group>
        </Card>
      ))}
    </Stack>
  );
}
```

## Performance optimization

### Memoization

```typescript
import { useMemo } from 'react';

function ExpensiveComponent({ data }: { data: number[] }) {
  const processedData = useMemo(() => {
    return data.map(x => expensiveOperation(x));
  }, [data]);

  return <Chart data={processedData} />;
}
```

### Callback memoization

```typescript
import { useCallback } from 'react';

function ParentComponent() {
  const [count, setCount] = useState(0);

  const handleIncrement = useCallback(() => {
    setCount(c => c + 1);
  }, []);

  return <ChildComponent onIncrement={handleIncrement} />;
}
```

### Component lazy loading

```typescript
import { lazy, Suspense } from 'react';

const HeavyComponent = lazy(() => import('./HeavyComponent'));

function Page() {
  return (
    <Suspense fallback={<LoadingOverlay visible />}>
      <HeavyComponent />
    </Suspense>
  );
}
```

## Testing patterns

See [../policyengine-app-v2/app/src/tests/CLAUDE.md](../policyengine-app-v2/app/src/tests/CLAUDE.md) for comprehensive testing guidelines.

### Component test

```typescript
import { render, screen } from '@testing-library/react';
import { userEvent } from '@testing-library/user-event';
import MyComponent from './MyComponent';

describe('MyComponent', () => {
  it('renders title', () => {
    render(<MyComponent title="Test" />);
    expect(screen.getByText('Test')).toBeInTheDocument();
  });

  it('handles click', async () => {
    const onSave = vi.fn();
    render(<MyComponent title="Test" onSave={onSave} />);

    await userEvent.click(screen.getByRole('button', { name: /save/i }));
    expect(onSave).toHaveBeenCalled();
  });
});
```

## Accessibility

### Semantic HTML

```typescript
// Good
<nav>
  <ul>
    <li><a href="/home">Home</a></li>
  </ul>
</nav>

// Avoid
<div className="nav">
  <div><span onClick={goHome}>Home</span></div>
</div>
```

### ARIA labels

```typescript
<Button aria-label="Close modal" onClick={onClose}>
  <IconX size={16} />
</Button>

<TextInput
  label="Email"
  aria-required="true"
  aria-invalid={!!error}
  aria-describedby={error ? 'email-error' : undefined}
/>
{error && <Text id="email-error" c="red">{error}</Text>}
```

### Keyboard navigation

```typescript
function CustomButton({ onClick }: { onClick: () => void }) {
  return (
    <button
      onClick={onClick}
      onKeyDown={(e) => {
        if (e.key === 'Enter' || e.key === ' ') {
          e.preventDefault();
          onClick();
        }
      }}
    >
      Click me
    </button>
  );
}
```

## TypeScript patterns

### Component props with children

```typescript
interface CardProps {
  title: string;
  children: React.ReactNode;
}

function Card({ title, children }: CardProps) {
  return (
    <Box>
      <Text>{title}</Text>
      {children}
    </Box>
  );
}
```

### Optional props

```typescript
interface ButtonProps {
  label: string;
  onClick?: () => void;
  disabled?: boolean;
}

function Button({ label, onClick, disabled = false }: ButtonProps) {
  return (
    <button onClick={onClick} disabled={disabled}>
      {label}
    </button>
  );
}
```

### Generic components

```typescript
interface ListProps<T> {
  items: T[];
  renderItem: (item: T) => React.ReactNode;
}

function List<T extends { id: string }>({ items, renderItem }: ListProps<T>) {
  return (
    <Stack>
      {items.map(item => (
        <div key={item.id}>{renderItem(item)}</div>
      ))}
    </Stack>
  );
}
```
