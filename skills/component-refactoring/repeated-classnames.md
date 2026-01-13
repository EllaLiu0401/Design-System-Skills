# Eliminating Repeated className Patterns with Props API

> **Design System Principle**: When you see the same className structure repeated 3+ times, refactor it into a props-based API.

## The Principle: Abstract Repeated Patterns

When developers manually write the same HTML structure and classNames repeatedly across your codebase, it's a signal to abstract that pattern into your component's API.

### Why?

1. **Reduce boilerplate** - Developers write 70-80% less code for common use cases
2. **Ensure consistency** - One implementation = no variations in structure
3. **Easier maintenance** - Update once, fixes everywhere
4. **Better DX** - Simple props are more intuitive than memorizing className patterns

## Case Study: Card Component

### Context

A Card component had these repeated patterns across the codebase:

- `<div className='card-body'>` - **20+ occurrences**
- `<h2 className='card-title'>` - **15+ occurrences**
- `<div className='card-actions'>` - **11+ occurrences**

**Team feedback:**
> "If we're using the same stuff repeatedly, there's quite a lot of custom classes. Where you've got a card, you're always gonna have a card body. The title could be a prop on the card component itself."

### ❌ Problem: Manual Structure Everywhere

Every developer had to remember and write this exact structure:

```tsx
<Card className='w-96'>
  <div className='card-body'>
    <h2 className='card-title'>Card Title</h2>
    <p>Card description goes here</p>
    <div className='card-actions'>
      <button className='btn btn-primary'>Buy Now</button>
    </div>
  </div>
</Card>
```

**Problems:**
- ❌ 7 lines of boilerplate for every card
- ❌ Easy to forget `card-body` wrapper
- ❌ Repeated structure in 11+ places
- ❌ Inconsistent structure across the codebase
- ❌ High cognitive load for new developers

### ✅ Solution: Props-Based API

Abstract the repeated patterns into optional props:

```tsx
<Card
  title='Card Title'
  description='Card description goes here'
  actions={<button className='btn btn-primary'>Buy Now</button>}
/>
```

**Benefits:**
- ✅ 1 line instead of 7 (85% less code)
- ✅ Impossible to forget card-body (automatic)
- ✅ Consistent structure enforced by component
- ✅ Intuitive API that's self-documenting
- ✅ Still flexible with children for complex cases

## Implementation Strategy

### Step 1: Identify Repeated Patterns

Search your codebase for repeated className usage:

```bash
# Find repeated patterns
grep -r "card-body" src/
grep -r "card-title" src/
grep -r "card-actions" src/
```

**Threshold**: If a pattern appears **3+ times**, consider abstracting it.

### Step 2: Design Props API

Add optional props that represent the repeated structure:

```tsx
interface CardProps {
  // Visual variants
  variant?: 'default' | 'outlined' | 'elevated';
  size?: 'sm' | 'md' | 'lg';

  // NEW abstraction props
  title?: ReactNode;       // Replaces manual <h2 className='card-title'>
  description?: ReactNode; // Replaces manual <p>
  image?: ReactNode;       // Replaces manual <img>
  actions?: ReactNode;     // Replaces manual <div className='card-actions'>

  // Standard HTML props
  className?: string;
  children?: ReactNode;
}
```

**Key decisions:**
- Use `ReactNode` for flexibility (can pass strings or JSX)
- Provide sensible defaults (e.g., `variant='default'`)
- Support both new props and children for flexibility

### Step 3: Implement with Backward Compatibility

Use a detection flag to support both old and new usage:

```tsx
function Card({ title, description, image, actions, children, ...props }: CardProps) {
  // Detect if using new abstraction API
  const hasAbstractionProps = !!(title || description || image || actions);

  return (
    <div className='card' {...props}>
      {image && <div className='card-image'>{image}</div>}

      {/* NEW API: Automatic card-body wrapper */}
      {hasAbstractionProps ? (
        <div className='card-body'>
          {title && <h2 className='card-title'>{title}</h2>}
          {description && <p className='card-description'>{description}</p>}
          {children}
          {actions && <div className='card-actions'>{actions}</div>}
        </div>
      ) : (
        // OLD API: Direct children (backward compatibility)
        children
      )}
    </div>
  );
}
```

**Backward compatibility strategy:**
- If ANY new prop is provided → use abstraction (wrap in card-body)
- If NO new props → render children directly (old behavior)
- Both patterns work simultaneously

### Step 4: Update All Usage to New API

Update existing usages to use the cleaner API:

```tsx
// Before: Manual structure everywhere (repeated 20+ times)
<Card>
  <div className='card-body'>
    <h2 className='card-title'>Product Name</h2>
    <p>Product description</p>
    <div className='card-actions'>
      <button>Add to Cart</button>
    </div>
  </div>
</Card>

// After: Clean props API
<Card
  title='Product Name'
  description='Product description'
  actions={<button>Add to Cart</button>}
/>
```

## Design Decisions

### Props-Based API vs Sub-Components

You have two choices for abstraction:

#### Option A: Sub-Components (NOT recommended for this case)

```tsx
<Card>
  <CardBody>
    <CardTitle>Title</CardTitle>
    <CardDescription>Description</CardDescription>
    <CardActions>...</CardActions>
  </CardBody>
</Card>
```

**When to use:**
- Maximum flexibility needed
- Complex, varied structures
- Composition patterns (like Radix UI)

**Downside:** Doesn't reduce boilerplate (still 6 lines)

#### Option B: Props API (✅ Recommended for reducing boilerplate)

```tsx
<Card title='Title' description='Description' actions={...} />
```

**When to use:**
- Repeated patterns with consistent structure
- Want to reduce boilerplate significantly
- Most use cases are simple

**Why Props API:**
1. Pattern is highly consistent (always: title → description → actions)
2. Reduces 7 lines to 1 line
3. Team feedback suggested props-based approach

### Hybrid Approach: Props + Children

The best solution supports both:

```tsx
// Simple case: Just props
<Card title='Title' description='Description' actions={...} />

// Complex case: Props + custom children
<Card title='Product Name' actions={...}>
  <div className='price-badge'>$29.99</div>
  <ul className='features-list'>
    <li>Feature 1</li>
    <li>Feature 2</li>
  </ul>
</Card>

// Old case: Pure children (backward compatibility)
<Card>
  <div className='card-body'>
    <h2>Custom Structure</h2>
  </div>
</Card>
```

This gives you:
- ✅ Simplicity for common cases (props)
- ✅ Flexibility for complex cases (props + children)
- ✅ Backward compatibility (pure children)

## Before & After Comparison

### Before Refactoring

**Code volume:**
- `card-body` wrapper: 20 times
- `card-title` element: 15 times
- `card-actions` container: 11 times
- **Total: 46+ repeated patterns**

**Example usage:**
```tsx
<Card>
  <div className='card-body'>
    <h2 className='card-title'>Product</h2>
    <p>Description text</p>
    <div className='card-actions'>
      <button className='btn'>Buy</button>
    </div>
  </div>
</Card>
```

**Lines per card:** 7 lines
**Cognitive load:** High (must remember exact structure)

### After Refactoring

**Code volume:**
- All patterns abstracted into Card component
- **Total: 0 manual patterns**

**Example usage:**
```tsx
<Card
  title='Product'
  description='Description text'
  actions={<button className='btn'>Buy</button>}
/>
```

**Lines per card:** 1 line
**Cognitive load:** Low (intuitive props)

**Improvement:** 85% less code, much clearer intent

## When to Apply This Principle

### ✅ Refactor to props API when:

- Same className pattern appears **3+ times**
- Structure is highly consistent (same order, same elements)
- Most use cases are simple and similar
- You want to reduce boilerplate significantly
- Team members complain about repetitive code

### ⚠️ Keep manual structure when:

- Pattern appears less than 3 times
- High variability in structure/order
- Complex composition needs (nested dynamic structures)
- Abstraction would be more complex than manual approach

## Implementation Checklist

When refactoring a component to eliminate className repetition:

- [ ] **Analyze patterns**: Search codebase for repeated className usage
- [ ] **Count occurrences**: Ensure pattern appears 3+ times
- [ ] **Design props API**: Create intuitive prop names
- [ ] **Implement detection logic**: Support both old and new usage
- [ ] **Maintain backward compatibility**: Existing code continues working
- [ ] **Update all usages**: Migrate codebase to new API
- [ ] **Update documentation**: Show new recommended approach
- [ ] **Test edge cases**: Verify complex scenarios work
- [ ] **Verify no breaking changes**: Run tests and builds

## Key Takeaway

> **"If you're doing the same pattern over and over again (3+ times), refactor it into the design system."**

Don't make developers memorize and manually write the same className structures repeatedly. Abstract common patterns into your component's props API. Keep it simple for the 80% case, but maintain flexibility for the 20% edge cases with children.

---

## Related Principles

- **Minimal by default**: Only abstract when you see real repetition (3+ times)
- **Progressive disclosure**: Simple props for simple cases, children for complex cases
- **Backward compatibility**: Old code continues working while new code is cleaner

## Real-World Impact

### Metrics
- **Before**: 46+ repeated className patterns across codebase
- **After**: 0 manual patterns in application code
- **Code reduction**: 70-85% less code per card usage
- **Consistency**: Uniform structure across entire codebase

### Example: Product Card

**Before (9 lines):**
```tsx
<Card>
  <div className='card-body'>
    <div className='product-header'>
      <h2 className='card-title'>Premium Plan</h2>
      <span className='price'>$29/mo</span>
    </div>
    <ul className='feature-list'>
      <li>Feature A</li>
      <li>Feature B</li>
    </ul>
    <div className='card-actions'>
      <button className='btn-primary'>Subscribe</button>
    </div>
  </div>
</Card>
```

**After (6 lines):**
```tsx
<Card
  title={<>Premium Plan <span className='price'>$29/mo</span></>}
  actions={<button className='btn-primary'>Subscribe</button>}
>
  <ul className='feature-list'>
    <li>Feature A</li>
    <li>Feature B</li>
  </ul>
</Card>
```

**Eliminated:**
- ❌ Manual `card-body` wrapper
- ❌ Separate div for actions
- ❌ Memorizing the exact structure

**Result:** 3 lines saved (33% reduction), clearer structure

---

**Last updated**: January 2026
**Tags**: Component API Design, DX Improvement, Refactoring, Boilerplate Reduction
**Difficulty**: Intermediate
