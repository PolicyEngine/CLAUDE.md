# Dashboard and application architecture

This guide covers the architecture patterns for building PolicyEngine dashboards and web applications.

## Project types

### Static dashboards

For dashboards with static content that doesn't require dynamic computation:

**Stack:**
- Next.js for static site generation
- Data stored in CSV files in the same repository
- Build output can be deployed to GitHub Pages or Vercel

**When to use:**
- Content is fixed or updated infrequently
- Data can be pre-computed and stored in CSV format
- No user-specific calculations needed

**Structure:**
```
my-dashboard/
├── data/
│   ├── results.csv
│   └── metadata.csv
├── app/
│   └── page.tsx
├── components/
├── package.json
└── next.config.js
```

### Dynamic dashboards

For dashboards requiring live computation or user interaction:

**Stack:**
- Next.js frontend
- FastAPI backend for computation
- PostgreSQL for data persistence (if needed)

**When to use:**
- User inputs drive calculations
- Integration with PolicyEngine simulation API
- Real-time data processing required

**Structure:**
```
my-dashboard/
├── frontend/
│   ├── app/
│   ├── components/
│   └── package.json
├── backend/
│   ├── main.py
│   ├── requirements.txt
│   └── api/
└── docker-compose.yml
```

## Technical stack

### Frontend
- **Framework**: Next.js 14+ with App Router
- **UI library**: Mantine 8.x for components
- **Charts**: Plotly.js for interactive visualizations, D3.js for custom visualizations
- **State**: TanStack Query for server state, Zustand for client state (if needed)
- **Styling**: Mantine's styling system with design tokens
- **Package manager**: npm (or pnpm for monorepos)

### Backend (dynamic apps only)
- **Framework**: FastAPI
- **Database**: PostgreSQL with SQLAlchemy ORM
- **API**: RESTful with OpenAPI documentation
- **Deployment**: Docker containers

## Development setup

### Prerequisites
```bash
# Install Node.js 20+
nvm install 20
nvm use 20

# Install Python 3.11+ (for dynamic apps)
pyenv install 3.11
pyenv local 3.11
```

### Frontend setup
```bash
# Create Next.js app
npx create-next-app@latest my-dashboard --typescript --tailwind

# Install Mantine
npm install @mantine/core @mantine/hooks @mantine/charts
npm install @tabler/icons-react

# Install charting libraries
npm install plotly.js-dist react-plotly.js
npm install d3

# Install data fetching
npm install @tanstack/react-query
```

### Backend setup (dynamic apps only)
```bash
# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install FastAPI
pip install fastapi uvicorn sqlalchemy psycopg2-binary pydantic
```

## Data loading patterns

### CSV data (static apps)
```typescript
// lib/data.ts
import fs from 'fs';
import path from 'path';
import Papa from 'papaparse';

export function loadCSV(filename: string) {
  const filepath = path.join(process.cwd(), 'data', filename);
  const content = fs.readFileSync(filepath, 'utf-8');
  const parsed = Papa.parse(content, { header: true, dynamicTyping: true });
  return parsed.data;
}
```

### API data (dynamic apps)
```typescript
// lib/api.ts
import { useQuery } from '@tanstack/react-query';

export function useSimulation(policyId: string) {
  return useQuery({
    queryKey: ['simulation', policyId],
    queryFn: async () => {
      const response = await fetch(`/api/simulations/${policyId}`);
      if (!response.ok) throw new Error('Failed to fetch');
      return response.json();
    },
  });
}
```

## Deployment

### Static dashboards
```bash
# Build for production
npm run build

# Deploy to Vercel
vercel deploy --prod

# Or to GitHub Pages
npm run build && npm run export
```

### Dynamic dashboards
```yaml
# docker-compose.yml
services:
  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
  backend:
    build: ./backend
    ports:
      - "8000:8000"
    environment:
      DATABASE_URL: postgresql://user:pass@db:5432/mydb
  db:
    image: postgres:15
    environment:
      POSTGRES_DB: mydb
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
```

## Performance considerations

- Pre-compute data where possible for static apps
- Use Next.js static generation for pages that don't change
- Implement proper caching headers for API responses
- Use React Query's stale-while-revalidate pattern
- Lazy load heavy visualizations (Plotly charts)
- Consider using Web Workers for heavy computations
