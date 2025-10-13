# Chart and visualization guide

This guide covers charting and data visualization patterns for PolicyEngine applications.

## Visualization philosophy

- **Clarity over complexity**: Start simple, add detail only when necessary
- **Immediate insight**: Charts should communicate their message at a glance
- **Consistent styling**: Use the same colour palette and styling across all charts
- **Animation**: Use smooth transitions when data changes to help users track changes
- **Interactive**: Enable hover tooltips, zooming, and filtering where helpful

## Chart libraries

### Plotly.js (primary)

Use Plotly for most standard charts:

```typescript
import Plot from 'react-plotly.js';

<Plot
  data={[
    {
      type: 'bar',
      x: ['Q1', 'Q2', 'Q3', 'Q4'],
      y: [100, 150, 130, 180],
      marker: { color: '#319795' },
    },
  ]}
  layout={{
    paper_bgcolor: 'rgba(0,0,0,0)',
    plot_bgcolor: 'rgba(0,0,0,0)',
    font: { family: 'Inter, sans-serif', size: 14, color: '#344054' },
    margin: { l: 50, r: 20, t: 30, b: 50 },
    xaxis: {
      gridcolor: '#E2E8F0',
      linecolor: '#CBD5E1',
      title: { text: 'Quarter', standoff: 10 },
    },
    yaxis: {
      gridcolor: '#E2E8F0',
      linecolor: '#CBD5E1',
      title: { text: 'Revenue (£)', standoff: 10 },
    },
  }}
  config={{
    displayModeBar: false,
    responsive: true,
  }}
  style={{ width: '100%', height: '100%' }}
/>
```

**When to use Plotly:**
- Bar charts, line charts, scatter plots
- Standard statistical visualizations
- Charts requiring zoom/pan interactions
- 3D visualizations

### D3.js (advanced)

Use D3 for custom or complex visualizations:

```typescript
import * as d3 from 'd3';
import { useEffect, useRef } from 'react';

function CustomChart({ data }) {
  const svgRef = useRef<SVGSVGElement>(null);

  useEffect(() => {
    if (!svgRef.current) return;

    const svg = d3.select(svgRef.current);
    svg.selectAll('*').remove();

    // D3 visualization code
    const g = svg.append('g')
      .attr('transform', 'translate(40, 20)');

    // ... rest of D3 code
  }, [data]);

  return <svg ref={svgRef} width={600} height={400} />;
}
```

**When to use D3:**
- Network diagrams
- Custom interactive visualizations
- Geographic maps with custom styling
- Animated transitions between data states
- Hierarchical data (trees, sunbursts)

### Recharts (simple alternative)

Available as alternative for simple charts:

```typescript
import { BarChart, Bar, XAxis, YAxis, Tooltip, ResponsiveContainer } from 'recharts';

<ResponsiveContainer width="100%" height={300}>
  <BarChart data={data}>
    <XAxis dataKey="name" />
    <YAxis />
    <Tooltip />
    <Bar dataKey="value" fill="#319795" />
  </BarChart>
</ResponsiveContainer>
```

Use Recharts for quick prototypes, but prefer Plotly for production.

## Chart types and when to use them

### Bar charts
Best for comparing discrete categories or time periods.

```typescript
const data = [{
  type: 'bar',
  x: ['2020', '2021', '2022', '2023'],
  y: [120, 150, 180, 210],
  marker: { color: '#319795' },
  name: 'Revenue',
}];
```

**Use for:**
- Comparing values across categories
- Showing changes over discrete time periods
- Displaying distribution across groups

### Line charts
Best for continuous time series or trends.

```typescript
const data = [{
  type: 'scatter',
  mode: 'lines+markers',
  x: ['Jan', 'Feb', 'Mar', 'Apr'],
  y: [120, 135, 125, 145],
  line: { color: '#319795', width: 2 },
  marker: { size: 6 },
}];
```

**Use for:**
- Time series data
- Trends over continuous periods
- Multiple series comparisons

### Stacked bar charts
Best for showing composition over categories.

```typescript
const data = [
  {
    type: 'bar',
    name: 'Income tax',
    x: ['Q1', 'Q2', 'Q3', 'Q4'],
    y: [100, 120, 110, 130],
    marker: { color: '#319795' },
  },
  {
    type: 'bar',
    name: 'National insurance',
    x: ['Q1', 'Q2', 'Q3', 'Q4'],
    y: [50, 60, 55, 65],
    marker: { color: '#026AA2' },
  },
];

const layout = { barmode: 'stack' };
```

**Use for:**
- Part-to-whole relationships
- Comparing composition across categories
- Budget breakdowns

### Area charts
Best for showing cumulative totals over time.

```typescript
const data = [{
  type: 'scatter',
  mode: 'lines',
  fill: 'tozeroy',
  x: ['Jan', 'Feb', 'Mar', 'Apr'],
  y: [120, 135, 125, 145],
  line: { color: '#319795' },
  fillcolor: 'rgba(49, 151, 149, 0.2)',
}];
```

**Use for:**
- Cumulative values over time
- Showing volume or magnitude trends
- Part-to-whole over time (stacked areas)

### Scatter plots
Best for showing relationships between variables.

```typescript
const data = [{
  type: 'scatter',
  mode: 'markers',
  x: [1, 2, 3, 4, 5],
  y: [10, 25, 20, 35, 30],
  marker: {
    size: 10,
    color: '#319795',
    opacity: 0.6,
  },
}];
```

**Use for:**
- Correlation analysis
- Distribution visualization
- Outlier detection

### Heatmaps
Best for showing density or intensity across two dimensions.

```typescript
const data = [{
  type: 'heatmap',
  z: [[1, 20, 30], [20, 1, 60], [30, 60, 1]],
  x: ['Category A', 'Category B', 'Category C'],
  y: ['Group 1', 'Group 2', 'Group 3'],
  colorscale: 'Blues',
}];
```

**Use for:**
- Correlation matrices
- Time-of-day patterns
- Geographic density (with geo coordinates)

## Styling guidelines

### Colour usage

**Single series:**
Use primary.500 (teal):
```typescript
marker: { color: '#319795' }
```

**Multiple series (ordered):**
```typescript
const seriesColors = [
  '#319795',  // primary.500 (teal)
  '#026AA2',  // blue.700
  '#22C55E',  // success (green)
  '#FEC601',  // warning (yellow)
  '#38B2AC',  // primary.400 (lighter teal)
  '#0EA5E9',  // blue.500
];
```

**Diverging scales (negative/positive):**
```typescript
// For showing gains/losses
const divergingColors = {
  negative: '#EF4444',  // error (red)
  neutral: '#9CA3AF',   // gray
  positive: '#22C55E',  // success (green)
};

// Use in Plotly colorscale
colorscale: [
  [0, '#EF4444'],
  [0.5, '#FFFFFF'],
  [1, '#22C55E'],
]
```

### Standard layout

Apply this layout to all Plotly charts:

```typescript
const standardLayout = {
  paper_bgcolor: 'rgba(0,0,0,0)',
  plot_bgcolor: 'rgba(0,0,0,0)',
  font: {
    family: 'Inter, -apple-system, sans-serif',
    size: 14,
    color: '#344054',  // secondary.700
  },
  margin: { l: 60, r: 30, t: 40, b: 60 },
  xaxis: {
    gridcolor: '#E2E8F0',    // secondary.200
    linecolor: '#CBD5E1',    // secondary.300
    zerolinecolor: '#CBD5E1',
    title: {
      font: { size: 14, color: '#344054' },
      standoff: 15,
    },
    tickfont: { size: 12, color: '#64748B' },  // secondary.500
  },
  yaxis: {
    gridcolor: '#E2E8F0',
    linecolor: '#CBD5E1',
    zerolinecolor: '#CBD5E1',
    title: {
      font: { size: 14, color: '#344054' },
      standoff: 15,
    },
    tickfont: { size: 12, color: '#64748B' },
  },
  hoverlabel: {
    bgcolor: '#FFFFFF',
    bordercolor: '#CBD5E1',
    font: { size: 13, color: '#344054' },
  },
  legend: {
    orientation: 'h',
    yanchor: 'bottom',
    y: 1.02,
    xanchor: 'left',
    x: 0,
    font: { size: 13 },
  },
};
```

### Responsive sizing

Always make charts responsive:

```typescript
<Box style={{ width: '100%', height: 400 }}>
  <Plot
    data={data}
    layout={{
      ...standardLayout,
      autosize: true,
    }}
    config={{
      displayModeBar: false,
      responsive: true,
    }}
    style={{ width: '100%', height: '100%' }}
  />
</Box>
```

For mobile, reduce height:

```typescript
<Box
  style={{
    width: '100%',
    height: '400px',
    '@media (max-width: 768px)': {
      height: '300px',
    },
  }}
>
  <Plot {...props} />
</Box>
```

## Animation patterns

### Transition on data change

Enable smooth transitions:

```typescript
<Plot
  data={data}
  layout={layout}
  config={{
    displayModeBar: false,
    responsive: true,
  }}
  useResizeHandler={true}
  // Plotly automatically animates data changes
/>
```

### Custom D3 transitions

```typescript
// Smooth transition for bar heights
svg.selectAll('rect')
  .data(data)
  .transition()
  .duration(300)
  .ease(d3.easeQuadOut)
  .attr('height', d => yScale(d.value))
  .attr('y', d => height - yScale(d.value));
```

### Number animations

Animate changing numeric values:

```typescript
import { useSpring, animated } from '@react-spring/web';

function AnimatedNumber({ value }: { value: number }) {
  const { number } = useSpring({
    number: value,
    from: { number: 0 },
    config: { duration: 300 },
  });

  return (
    <animated.span>
      {number.to(n => n.toFixed(0))}
    </animated.span>
  );
}
```

## Smart defaults

Use the smart chart defaults system for automatic axis selection:

```typescript
import { getSmartChartDefaults } from '@/utils/chartDefaults';

const defaults = getSmartChartDefaults(
  rows,           // data rows
  columns,        // column names
  isAggregateChange  // boolean
);

// defaults.xAxisColumns: recommended X axis
// defaults.yAxisColumns: recommended Y axis
// defaults.xAxisTitle: suggested X axis label
// defaults.yAxisTitle: suggested Y axis label
// defaults.columnOrder: recommended column ordering for tables
```

This automatically selects appropriate axes based on:
- Data types (categorical vs numeric)
- Column names (Year, Variable, Value, Change)
- Data patterns (varying vs constant values)

## Accessibility

### Colour considerations

- Don't rely solely on colour to convey information
- Use patterns or labels in addition to colours
- Ensure sufficient contrast between colours
- Provide colour-blind friendly palettes when showing many categories

### Alternative text

Always provide descriptive alt text:

```typescript
<Plot
  data={data}
  layout={layout}
  config={{ displayModeBar: false }}
  style={{ width: '100%', height: '100%' }}
  // Add ARIA label
  aria-label="Bar chart showing revenue growth from 2020 to 2023"
/>
```

### Data tables

Provide data table alternatives:

```typescript
function ChartWithTable({ data }) {
  const [showTable, setShowTable] = useState(false);

  return (
    <>
      <SegmentedControl
        value={showTable ? 'table' : 'chart'}
        onChange={(v) => setShowTable(v === 'table')}
        data={[
          { label: 'Chart', value: 'chart' },
          { label: 'Table', value: 'table' },
        ]}
      />
      {showTable ? <DataTable data={data} /> : <Chart data={data} />}
    </>
  );
}
```

## Performance optimization

### Large datasets

For large datasets (>1000 points):

```typescript
// Use WebGL for scatter plots
const data = [{
  type: 'scattergl',  // GL-accelerated
  mode: 'markers',
  x: largeXArray,
  y: largeYArray,
}];

// Or aggregate data before charting
const aggregatedData = aggregateByBins(rawData, 50);
```

### Lazy loading

Load charts only when visible:

```typescript
import { useInView } from 'react-intersection-observer';

function LazyChart({ data }) {
  const { ref, inView } = useInView({
    triggerOnce: true,
    rootMargin: '100px',
  });

  return (
    <div ref={ref}>
      {inView && <Plot data={data} />}
    </div>
  );
}
```

### Memoization

Memoize chart data calculations:

```typescript
import { useMemo } from 'react';

function ChartComponent({ rawData }) {
  const chartData = useMemo(() => {
    return processDataForChart(rawData);
  }, [rawData]);

  return <Plot data={chartData} />;
}
```

## Example implementations

### Decile chart (distributional analysis)

```typescript
function DecileChart({ data }) {
  const chartData = [{
    type: 'bar',
    x: data.map(d => `D${d.decile}`),
    y: data.map(d => d.averageChange),
    marker: {
      color: data.map(d => d.averageChange >= 0 ? '#22C55E' : '#EF4444'),
    },
    text: data.map(d => `£${d.averageChange.toFixed(0)}`),
    textposition: 'outside',
  }];

  const layout = {
    ...standardLayout,
    xaxis: { title: { text: 'Income decile' } },
    yaxis: { title: { text: 'Average change (£/year)' } },
    showlegend: false,
  };

  return <Plot data={chartData} layout={layout} />;
}
```

### Time series comparison

```typescript
function TimeSeriesChart({ baseline, reform }) {
  const data = [
    {
      type: 'scatter',
      mode: 'lines+markers',
      name: 'Baseline',
      x: baseline.map(d => d.year),
      y: baseline.map(d => d.value),
      line: { color: '#64748B', width: 2, dash: 'dot' },
      marker: { size: 6 },
    },
    {
      type: 'scatter',
      mode: 'lines+markers',
      name: 'Reform',
      x: reform.map(d => d.year),
      y: reform.map(d => d.value),
      line: { color: '#319795', width: 2 },
      marker: { size: 6 },
    },
  ];

  return <Plot data={data} layout={standardLayout} />;
}
```
