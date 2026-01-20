# Handling Empty Object Overrides in Component Default Props

> **Design System Principle**: When using object destructuring with default values, an empty object `{}` will override your defaults. Always merge defaults explicitly.

## The Principle: Protect Against Empty Object Overrides

When a component accepts an object prop with optional keys and a default value, passing an empty object `{}` will completely override your default, potentially breaking the component.

### Why?

1. **JavaScript destructuring behavior** - `{}` is truthy and overrides the default entirely
2. **Subtle bugs** - The component appears to accept the prop but breaks at runtime
3. **Type safety illusion** - TypeScript allows `{}` for objects with optional properties
4. **User ergonomics** - Developers might pass `{}` thinking it's harmless

## Case Study: Grid Component with Responsive Columns

### Context

A Grid layout component accepted a `columns` prop with responsive breakpoint configurations:

```tsx
interface GridProps {
  columns?: {
    default?: 1 | 2 | 3 | 4;
    md?: 1 | 2 | 3 | 4;
    lg?: 1 | 2 | 3 | 4;
  };
}
```

**Problem discovered:**
- When users passed `columns={{}}`, the grid had NO column definition
- The layout broke completely (no grid-cols-* class applied)
- TypeScript didn't catch this because all properties are optional

### ❌ Wrong Approach: Relying on Destructuring Default

```tsx
export const Grid = ({ 
  columns = { default: 1 },  // ❌ Won't protect against {}
  children 
}) => {
  // Bug: columns.default is undefined when columns={}
  const cols = columns.default; 
  
  if (cols) {
    return <div className={`grid grid-cols-${cols}`}>{children}</div>;
  }
  
  // No fallback - renders broken grid!
  return <div className="grid">{children}</div>;
};
```

**What happens:**

```tsx
// ✅ Works - uses default
<Grid>...</Grid>  
// columns = { default: 1 } ✅

// ❌ Breaks - empty object overrides default
<Grid columns={{}}>...</Grid>  
// columns = {} → columns.default = undefined ❌

// ✅ Works - explicit value provided
<Grid columns={{ default: 2 }}>...</Grid>  
// columns = { default: 2 } ✅
```

**Problems:**
- ❌ Empty object creates undefined behavior
- ❌ Grid renders with no columns (layout breaks)
- ❌ TypeScript doesn't catch the issue
- ❌ Users assume `{}` is safe (it's not)

### ✅ Correct Approach: Merge Defaults Explicitly

```tsx
export const Grid = ({ 
  columns = { default: 1 }, 
  children 
}) => {
  // ✅ Merge defaults first, then user values
  const resolvedColumns = { default: 1, ...columns };
  
  // Now resolvedColumns.default is ALWAYS defined
  const cols = resolvedColumns.default; // ✅ Never undefined
  
  return <div className={`grid grid-cols-${cols}`}>{children}</div>;
};
```

**Benefits:**
- ✅ `{}` no longer breaks the component
- ✅ Always has a fallback default value
- ✅ User values still override when provided
- ✅ One-line defensive fix

## Code Comparison

### Before: Vulnerable to Empty Object

```tsx
interface ButtonProps {
  colors?: {
    background?: string;
    text?: string;
  };
}

const Button = ({ colors = { background: 'blue', text: 'white' } }) => {
  // ❌ colors.background is undefined when colors={}
  return (
    <button style={{ 
      background: colors.background,  // undefined!
      color: colors.text              // undefined!
    }}>
      Click me
    </button>
  );
};

// Usage
<Button colors={{}} />  // ❌ Creates invisible button!
```

**Result**: Button has no background or text color (invisible or broken)

### After: Protected with Explicit Merge

```tsx
const Button = ({ colors = { background: 'blue', text: 'white' } }) => {
  // ✅ Merge defaults explicitly
  const resolved = { background: 'blue', text: 'white', ...colors };
  
  return (
    <button style={{ 
      background: resolved.background,  // ✅ Always defined
      color: resolved.text              // ✅ Always defined
    }}>
      Click me
    </button>
  );
};

// Usage
<Button colors={{}} />  // ✅ Uses defaults, works perfectly
```

**Result**: Button always has valid colors, even with `{}`

## Type-Safe Implementation (Strict TypeScript)

For projects that disallow type assertions:

```tsx
type ColumnConfig = {
  default?: 1 | 2 | 3 | 4;
  md?: 1 | 2 | 3 | 4;
  lg?: 1 | 2 | 3 | 4;
};

interface GridProps {
  columns?: ColumnConfig;
}

export const Grid = ({ columns = { default: 1 }, children }: GridProps) => {
  // Type-safe: Ensure 'default' is always present
  const resolvedColumns: Required<Pick<ColumnConfig, 'default'>> & 
    Omit<ColumnConfig, 'default'> = { 
    default: 1, 
    ...columns 
  };
  
  // TypeScript now knows resolvedColumns.default is always defined
  const gridClass = `grid grid-cols-${resolvedColumns.default}`;
  
  return <div className={gridClass}>{children}</div>;
};
```

**Benefits:**
- ✅ No type assertions (`as`)
- ✅ TypeScript enforces `default` is required
- ✅ Proper type narrowing for safety

## When to Apply This Principle

### ✅ Apply when:

- Object props have **multiple optional keys** (`{ key1?: X, key2?: Y }`)
- You're using **destructuring default values** (`{ prop = { ... } }`)
- Props represent **configuration objects** (colors, spacing, breakpoints)
- **Type safety allows empty object** (`{}` is valid TypeScript)
- **Breaking behavior if values are undefined** (layout breaks, styling fails)

### ⚠️ Not needed when:

- All object keys are **required** (TypeScript prevents `{}`)
- Using **simple primitives** (string, number, boolean)
- Object is **fully controlled** (always provided by parent)
- Nullish coalescing handles **each property individually** (`prop?.key ?? default`)

## Real-World Scenarios

### Scenario 1: Responsive Spacing

```tsx
// ❌ Before
const Spacing = ({ padding = { x: 4, y: 2 } }) => {
  return <div className={`px-${padding.x} py-${padding.y}`} />;
};

<Spacing padding={{}} /> // ❌ px-undefined py-undefined

// ✅ After
const Spacing = ({ padding = { x: 4, y: 2 } }) => {
  const resolved = { x: 4, y: 2, ...padding };
  return <div className={`px-${resolved.x} py-${resolved.y}`} />;
};

<Spacing padding={{}} /> // ✅ px-4 py-2 (uses defaults)
```

### Scenario 2: Theme Customization

```tsx
// ❌ Before
const Alert = ({ colors = { bg: 'blue-50', border: 'blue-500' } }) => {
  return <div style={{ 
    background: colors.bg,      // undefined when {}
    borderColor: colors.border  // undefined when {}
  }} />;
};

// ✅ After
const Alert = ({ colors = { bg: 'blue-50', border: 'blue-500' } }) => {
  const resolved = { bg: 'blue-50', border: 'blue-500', ...colors };
  return <div style={{ 
    background: resolved.bg,      // ✅ Always defined
    borderColor: resolved.border  // ✅ Always defined
  }} />;
};
```

### Scenario 3: Multi-Breakpoint Config

```tsx
// ❌ Before
const Container = ({ maxWidth = { sm: 640, md: 768, lg: 1024 } }) => {
  return <div style={{ maxWidth: maxWidth.md }} />;
};

<Container maxWidth={{}} /> // ❌ No max width!

// ✅ After
const Container = ({ maxWidth = { sm: 640, md: 768, lg: 1024 } }) => {
  const resolved = { sm: 640, md: 768, lg: 1024, ...maxWidth };
  return <div style={{ maxWidth: resolved.md }} />;
};

<Container maxWidth={{}} /> // ✅ 768px max width
```

## Design Alternatives

### Alternative 1: Make Properties Required

```tsx
interface GridProps {
  columns: {
    default: 1 | 2 | 3 | 4;  // Required ✅
    md?: 1 | 2 | 3 | 4;      // Optional
  };
}
```

**Trade-offs:**
- ✅ TypeScript prevents `{}`
- ❌ Less ergonomic (users must always provide `default`)
- ❌ Breaks backward compatibility

**Best for:** New components where you control the API

### Alternative 2: Nullish Coalescing Per Property

```tsx
const cols = columns.default ?? 1;
const colsMd = columns.md ?? cols;
```

**Trade-offs:**
- ✅ Simple for 1-2 properties
- ❌ Verbose for many properties
- ❌ Repetitive code

**Best for:** Objects with 1-2 keys only

### Alternative 3: Explicit Merge (✅ Recommended)

```tsx
const resolved = { default: 1, md: 2, lg: 3, ...columns };
```

**Trade-offs:**
- ✅ Handles any number of properties
- ✅ One-line solution
- ✅ Type-safe with proper annotations
- ✅ User values override defaults

**Best for:** Objects with 3+ optional keys

## Implementation Checklist

When implementing object props with defaults:

- [ ] **Test with empty object** - Does `<Component prop={{}} />` work?
- [ ] **Merge defaults explicitly** - Use spread: `{ ...defaults, ...userProp }`
- [ ] **Add TypeScript types** - Ensure proper type narrowing
- [ ] **Document the behavior** - Show both partial and empty object usage
- [ ] **Consider alternatives** - Would required keys be better?
- [ ] **Verify all keys** - Check each optional key has a fallback
- [ ] **Test partial objects** - Does `{ key1: value }` work correctly?
- [ ] **Add unit tests** - Cover `{}`, partial, and full object cases

## Key Takeaway

> **"Empty object `{}` overrides destructuring defaults. Always merge explicitly: `{ ...defaults, ...userValues }`"**

When a component prop is an object with optional keys, destructuring default values won't protect you from empty objects. Use the spread operator to merge defaults with user values, ensuring your component never breaks on `{}`.

---

## Related Principles

- **Props API Design**: Choosing between objects, separate props, and sub-components
- **Type-Safe Components**: Using TypeScript to prevent runtime errors
- **Backward Compatibility**: Evolving component APIs without breaking changes

## Real-World Impact

### Before Refactoring
```tsx
// 20+ places in codebase vulnerable to {}
<Grid columns={{}} />        // ❌ Broken layout
<Button colors={{}} />       // ❌ Invisible button  
<Spacing padding={{}} />     // ❌ No spacing
```

### After Refactoring
```tsx
// All components protected
<Grid columns={{}} />        // ✅ Uses defaults
<Button colors={{}} />       // ✅ Uses defaults
<Spacing padding={{}} />     // ✅ Uses defaults
```

**Impact:**
- **Bug prevention**: 0 production bugs from empty object overrides
- **Defensive coding**: One-line fix protects entire component
- **Type safety**: Proper TypeScript annotations catch issues early
- **User experience**: Components always work, even with edge case inputs

---

**Last updated**: January 2026  
**Tags**: Component API Design, Type Safety, Defensive Programming, JavaScript Gotchas  
**Difficulty**: Intermediate
