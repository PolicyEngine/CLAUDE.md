# PolicyEngine visual style guide

This guide defines the visual design system for PolicyEngine applications and dashboards.

## Design principles

1. **Clean but not sensationalised**: Use animations and visual interest thoughtfully without overwhelming users
2. **Sentence case everywhere**: Titles, buttons, labels, and headings all use sentence case
3. **Data-driven clarity**: Visualizations should be immediately understandable and focused on insights
4. **Professional simplicity**: Clean interfaces that put data and functionality first

## Colour palette

### Primary colours (teal)
```
primary.50:  #E6FFFA  (lightest backgrounds)
primary.100: #B2F5EA
primary.200: #81E6D9
primary.300: #4FD1C5
primary.400: #38B2AC
primary.500: #319795  (main brand colour)
primary.600: #2C7A7B
primary.700: #285E61
primary.800: #234E52
primary.900: #1D4044  (darkest)
```

Use primary.500 for:
- Primary buttons
- Active states
- Key UI elements
- Brand accent

### Secondary colours (gray)
```
secondary.50:  #F0F9FF
secondary.100: #F2F4F7  (light backgrounds)
secondary.200: #E2E8F0  (borders)
secondary.300: #CBD5E1
secondary.400: #94A3B8
secondary.500: #64748B
secondary.600: #475569
secondary.700: #344054  (dark text)
secondary.800: #1E293B
secondary.900: #101828  (darkest text)
```

### Blue accent
```
blue.50:  #F0F9FF
blue.100: #E0F2FE
blue.200: #BAE6FD
blue.300: #7DD3FC
blue.400: #38BDF8
blue.500: #0EA5E9
blue.600: #0284C7
blue.700: #026AA2  (links, info)
blue.800: #075985
blue.900: #0C4A6E
```

### Semantic colours
```
Success: #22C55E  (green for positive changes)
Warning: #FEC601  (yellow for cautions)
Error:   #EF4444  (red for errors/negative changes)
Info:    #1890FF  (blue for information)
```

### Text colours
```
Primary:   #000000  (main text)
Secondary: #5A5A5A  (supporting text)
Tertiary:  #9CA3AF  (subtle text)
Inverse:   #FFFFFF  (text on dark backgrounds)
```

### Background colours
```
Primary:   #FFFFFF  (main background)
Secondary: #F5F9FF  (sidebar background)
Tertiary:  #F1F5F9  (alternative background)
AppShell:  #F9FAFB  (gray.50 - main content area)
```

### Border colours
```
Light:  #E2E8F0  (gray.200 - default borders)
Medium: #CBD5E1  (gray.300 - emphasized borders)
Dark:   #94A3B8  (gray.400 - strong borders)
```

## Typography

### Font families
```typescript
Primary:   'Inter, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif'
Secondary: 'Public Sans, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif'
Body:      'Roboto, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif'
Mono:      'JetBrains Mono, "Fira Code", Consolas, monospace'
```

Use Inter for most UI text, Public Sans for medium text, Roboto for body text.

### Font sizes
```
xs:   12px  (small labels, captions)
sm:   14px  (default body text)
base: 16px  (larger body text)
lg:   18px  (subheadings)
xl:   20px  (headings)
2xl:  24px  (page titles)
3xl:  28px  (large titles)
4xl:  32px  (hero titles)
```

### Font weights
```
Light:     300
Normal:    400  (body text)
Medium:    500  (emphasized text)
Semibold:  600  (headings)
Bold:      700  (strong emphasis)
```

### Line heights
```
Tight:   1.25
Normal:  1.5   (default)
Relaxed: 1.625
Loose:   2.0
```

## Spacing scale

Use consistent spacing for margins, padding, and gaps:

```
xs:  4px   (tight spacing)
sm:  8px   (small spacing)
md:  12px  (default spacing)
lg:  16px  (medium spacing)
xl:  20px  (large spacing)
2xl: 24px  (extra large spacing)
3xl: 32px  (section spacing)
4xl: 48px  (major section spacing)
5xl: 64px  (page-level spacing)
```

### Component spacing
```typescript
// Button
padding: '8px 14px'
height: '36px'

// Input
padding: '8px 12px'
height: '40px'

// Badge
padding: '4px 12px'

// Card
padding: spacing.md (12px) or spacing.lg (16px)
marginBottom: spacing.md (12px)

// Container
paddingLeft/Right: '80px' (2xl)
paddingTop/Bottom: '48px' (lg)
```

## Border radius

```
xs:  2px   (subtle rounding)
sm:  4px   (default for most elements)
md:  6px   (cards, buttons)
lg:  8px   (larger cards)
xl:  12px  (modals)
2xl: 16px  (large containers)
```

## Shadows

Use sparingly for elevation and focus:

```
Light:  rgba(16, 24, 40, 0.05)  (subtle elevation)
Medium: rgba(16, 24, 40, 0.1)   (cards, dropdowns)
Dark:   rgba(16, 24, 40, 0.2)   (modals, popovers)
```

## Component patterns

### Cards

Use Card components for grouping related content:

```typescript
import { Card } from '@mantine/core';

// Default card
<Card padding="md" radius="md" withBorder>
  Content
</Card>

// Selectable card (inactive)
<Card variant="cardList--inactive">
  Content
</Card>

// Selected card
<Card variant="cardList--active">
  Content
</Card>
```

**Card variants:**
- `cardList--active`: Selected state with teal border and light blue background
- `cardList--inactive`: Default state with gray border
- `setupCondition--fulfilled`: Completed setup step
- `setupCondition--unfulfilled`: Incomplete setup step
- `setupCondition--active`: Currently active setup step

### Buttons

```typescript
import { Button } from '@mantine/core';

// Primary action
<Button variant="filled" color="primary">Save changes</Button>

// Secondary action
<Button variant="light" color="primary">Cancel</Button>

// Subtle action
<Button variant="subtle" color="gray">Dismiss</Button>
```

Always use sentence case for button labels.

### Text hierarchy

```typescript
import { Title, Text } from '@mantine/core';

// Page title
<Title order={1} size="2xl" fw={600}>Page title</Title>

// Section heading
<Title order={2} size="xl" fw={600}>Section heading</Title>

// Subsection heading
<Title order={3} size="lg" fw={500}>Subsection heading</Title>

// Body text
<Text size="sm">Regular body text</Text>

// Secondary text
<Text size="sm" c="dimmed">Supporting information</Text>

// Small label
<Text size="xs" c="dimmed">Small label</Text>
```

## Animations and transitions

Use clean, subtle animations throughout:

### Hover effects

```typescript
// Card hover
{
  cursor: 'pointer',
  transition: 'all 0.2s ease',
  '&:hover': {
    backgroundColor: colors.gray[50],
    borderColor: colors.border.medium,
  },
}

// Button hover (built into Mantine)
// Icons fade in on hover
{
  opacity: 0,
  transition: 'opacity 150ms ease',
  '&:hover': {
    opacity: 1,
  },
}
```

### Standard transitions
```
Fast:   150ms ease  (opacity, visibility)
Normal: 200ms ease  (hover states, colors)
Slow:   300ms ease  (larger movements, layouts)
```

### Number animations

When displaying changing numbers, animate the transition:

```typescript
import { AnimatedNumber } from '@/components/common';

// Animate number changes
<AnimatedNumber value={totalCost} duration={300} />
```

For custom implementations, use CSS transitions or spring animations.

### Component entry

Use subtle fade-in or slide-in animations for new content:

```typescript
{
  animation: 'fadeIn 200ms ease',
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(4px); }
  to { opacity: 1; transform: translateY(0); }
}
```

### Loading states

Use Mantine's LoadingOverlay or skeleton components:

```typescript
import { LoadingOverlay, Skeleton } from '@mantine/core';

// Full overlay
<LoadingOverlay visible={isLoading} />

// Skeleton placeholder
<Skeleton height={50} radius="md" />
```

## Chart styling

See [chart-visualization-guide.md](./chart-visualization-guide.md) for complete charting guidelines.

### Quick reference

**Plotly theme:**
```typescript
const plotlyLayout = {
  paper_bgcolor: 'rgba(0,0,0,0)',  // transparent
  plot_bgcolor: 'rgba(0,0,0,0)',   // transparent
  font: { family: 'Inter, sans-serif', size: 14, color: '#344054' },
  xaxis: { gridcolor: '#E2E8F0', linecolor: '#CBD5E1' },
  yaxis: { gridcolor: '#E2E8F0', linecolor: '#CBD5E1' },
};
```

**Colour scheme for data series:**
```typescript
const chartColors = [
  '#319795',  // primary.500 (teal)
  '#026AA2',  // blue.700
  '#22C55E',  // success (green)
  '#FEC601',  // warning (yellow)
  '#38B2AC',  // primary.400 (lighter teal)
  '#0EA5E9',  // blue.500
];
```

## Responsive design

### Breakpoints
```
xs: 36em  (576px)
sm: 48em  (768px)
md: 62em  (992px)
lg: 75em  (1200px)
xl: 88em  (1408px)
```

### Responsive patterns

Use Mantine's responsive props:

```typescript
<Box
  p={{ base: 'md', sm: 'lg', md: 'xl' }}
  w={{ base: '100%', md: '80%', lg: '60%' }}
>
  Content
</Box>
```

### Layout patterns

```typescript
// Responsive grid
<SimpleGrid cols={{ base: 1, sm: 2, md: 3 }} spacing="lg">
  {items}
</SimpleGrid>

// Responsive flex
<Flex direction={{ base: 'column', sm: 'row' }} gap="md">
  {items}
</Flex>
```

## Accessibility

- Maintain 4.5:1 contrast ratio for normal text (AA standard)
- Maintain 3:1 contrast ratio for large text and UI components
- Use semantic HTML elements
- Provide alt text for images
- Ensure keyboard navigation works
- Use ARIA labels where appropriate
- Test with screen readers

## Writing style

- Use sentence case for all text
- Be concise and clear
- Avoid oversensationalizing ("amazing", "incredible")
- Use British English spelling
- Focus on data and insights, not hyperbole
- Write in active voice where possible
- Use "we" for PolicyEngine, "you" for the user

Examples:
- ✓ "Calculate household impact"
- ✗ "Calculate Household Impact"
- ✓ "This reform reduces poverty by 12%"
- ✗ "This AMAZING reform dramatically reduces poverty!"
