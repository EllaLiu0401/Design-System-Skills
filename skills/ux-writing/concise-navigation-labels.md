# Concise Navigation Labels

> **One-sentence principle**: Menu labels should be as short as possible while remaining clear - remove redundant verbs and qualifiers.

## The Principle: Ruthlessly Simplify Menu Text

Navigation labels compete for user attention and screen space. Every extra word increases cognitive load and reduces scanability. Strip menu items down to their essential noun or concept.

### Why?

1. **Faster scanning** - Users can process "Analytics" much faster than "View Analytics Dashboard"
2. **Better mobile experience** - Shorter labels work better on constrained screens
3. **Cleaner visual hierarchy** - Concise labels create a less cluttered interface
4. **Improved translation** - Shorter English text usually translates more compactly

## Case Study: Dashboard Navigation Menu

### Context

During a code review for a dashboard application, we found menu items that were unnecessarily verbose. The navigation sidebar showed items like "Help & Support", "View Analytics", and "Manage Team" - all carrying redundant qualifiers.

- **Before**: Verbose menu labels with redundant verbs and qualifiers
- **After**: Simplified to core nouns/concepts only

### ❌ Wrong Approach: Overly Descriptive Labels

Using full phrases as if explaining the action to a new user in every instance.

**Problems:**

- ❌ Takes longer to scan and find the right option
- ❌ Creates visual clutter in the navigation
- ❌ Wastes precious screen real estate
- ❌ Makes mobile navigation harder to design
- ❌ Translation becomes wordier and harder to fit

### ✅ Correct Approach: Essential-Only Labels

Strip away redundant verbs (View, Manage, Show) and qualifiers (& Support) - keep only the core concept.

**Benefits:**

- ✅ Users scan menu items 2-3x faster
- ✅ Cleaner, more professional appearance
- ✅ More space for other UI elements
- ✅ Better mobile responsiveness
- ✅ Easier to translate and maintain consistency

## Code Comparison

### Before: Verbose Navigation Labels

```json
{
  "layout": {
    "nav": {
      "dashboard": "Dashboard",
      "settings": "Settings",
      "help": "Help & Support",
      "viewAnalytics": "View Analytics",
      "manageTeam": "Manage Team",
      "callHistory": "Call History"
    }
  }
}
```

```json
{
  "quickActions": {
    "title": "Quick Actions",
    "createAssistant": "Create New Assistant",
    "viewAnalytics": "View Analytics",
    "viewAnalyticsLabel": "View call analytics and reports",
    "manageTeam": "Manage Team",
    "manageTeamLabel": "Invite and manage team members",
    "callHistory": "View Call History",
    "callHistoryLabel": "Browse past call records"
  }
}
```

### After: Concise Navigation Labels

```json
{
  "layout": {
    "nav": {
      "dashboard": "Dashboard",
      "settings": "Settings",
      "help": "Help",
      "viewAnalytics": "Analytics",
      "manageTeam": "Team",
      "callHistory": "Call History"
    }
  }
}
```

```json
{
  "quickActions": {
    "title": "Quick Actions",
    "createAssistant": "Create New Assistant",
    "viewAnalytics": "Analytics",
    "viewAnalyticsLabel": "View call analytics and reports",
    "manageTeam": "Team",
    "manageTeamLabel": "Invite and manage team members",
    "callHistory": "Call History",
    "callHistoryLabel": "Browse past call records"
  }
}
```

**Result**: 30-50% shorter text, improved readability, and better user experience

## Simplification Rules

### Remove These Redundant Patterns:

1. **Action verbs in navigation**
   - ❌ "View Analytics" → ✅ "Analytics"
   - ❌ "Manage Team" → ✅ "Team"
   - ❌ "Show Reports" → ✅ "Reports"

2. **"& Support" qualifiers**
   - ❌ "Help & Support" → ✅ "Help"
   - ❌ "Documentation & Guides" → ✅ "Documentation"

3. **Redundant "View" prefix**
   - ❌ "View Call History" → ✅ "Call History"
   - ❌ "View Dashboard" → ✅ "Dashboard"

4. **Unnecessary adjectives**
   - ❌ "Detailed Reports" → ✅ "Reports"
   - ❌ "Advanced Settings" → ✅ "Settings"

### Keep These When Necessary:

1. **Action verbs for creation actions**
   - ✅ "Create Assistant" (not just "Assistant" - that's ambiguous)
   - ✅ "New Project" (clear action)

2. **Qualifiers that disambiguate**
   - ✅ "Call History" (not just "History" - too vague)
   - ✅ "User Settings" vs "System Settings" (when both exist)

## When to Apply This Principle

### ✅ Apply when:

- Designing primary navigation menus (sidebar, top nav)
- Creating quick action menus
- Building mobile navigation
- The context makes the action obvious (clicking "Analytics" obviously views analytics)
- The label appears in a list with similar items

### ⚠️ Don't apply when:

- The action is ambiguous without a verb ("Create Assistant" vs just "Assistant")
- You're writing button labels that need explicit calls-to-action
- The shortened version loses critical meaning
- Multiple items would have the same simplified name
- Writing onboarding tutorials or help documentation

## Implementation Checklist

### For Navigation Labels:

- [ ] Identify all navigation menu items
- [ ] Remove action verbs (View, Manage, Show, Display)
- [ ] Remove "& Support" or similar qualifiers
- [ ] Check that shortened labels are still clear in context
- [ ] Verify no ambiguity between items
- [ ] Update all i18n locale files consistently
- [ ] Test on mobile viewports for spacing
- [ ] Run past UX review for clarity

### For i18n Updates:

- [ ] Update base locale (e.g., en-AU)
- [ ] Update all translation locales (e.g., es-ES, zh-CN)
- [ ] Maintain consistency across all translation keys
- [ ] Check character lengths don't break mobile layouts
- [ ] Verify translations maintain the same level of conciseness

## Visual Differentiation

### Special Treatment for Different Action Types

If you have menu items that serve different purposes (e.g., one is a creation action while others are navigation), consider:

1. **Visual separation** - Use dividers or spacing
2. **Different styling** - Primary button style vs text links
3. **Icons** - Use distinctive icons to clarify meaning
4. **Grouping** - Place similar items together

Example from code review:

> "The Create Assistant section is different in nature from the other menu items. Consider changing its style or separating it visually."

```tsx
// Visual differentiation example
<nav>
  {/* Standard navigation items */}
  <NavItem href="/analytics">Analytics</NavItem>
  <NavItem href="/team">Team</NavItem>
  <NavItem href="/history">Call History</NavItem>

  {/* Visually separated primary action */}
  <Divider />
  <PrimaryButton href="/assistants/new">Create Assistant</PrimaryButton>
</nav>
```

## Multi-Language Considerations

When simplifying English labels, ensure your translations follow the same principles:

```json
// English: en-AU
{
  "help": "Help",           // Not "Help & Support"
  "viewAnalytics": "Analytics",  // Not "View Analytics"
  "manageTeam": "Team"      // Not "Manage Team"
}

// Spanish: es-ES
{
  "help": "Ayuda",          // Not "Ayuda y Soporte"
  "viewAnalytics": "Análisis",   // Not "Ver Análisis"
  "manageTeam": "Equipo"    // Not "Administrar Equipo"
}
```

## Key Takeaway

> **In navigation menus, context provides the verb. Your labels only need to provide the noun.**

When users see items in a navigation menu, they already know they're making choices about where to go or what to view. Menu labels should be scannable landmarks, not full sentences. Save the descriptive text for tooltips, help text, or onboarding.

---

## Related Principles

- **Progressive Disclosure**: Show details on hover/focus, not in the initial label
- **Consistent Voice**: If you remove "View" from one item, remove it from all similar items
- **Mobile-First Design**: Design for the smallest screen first, then scale up

## Real-World Impact

From the case study:

- **6 menu items updated** across 2 locales (en-AU, es-ES)
- **Both navigation and quick actions** maintained consistency
- **Code review feedback** integrated into design system documentation
- **Reusable pattern** established for future menu items

---

**Last updated**: January 2026  
**Tags**: UX Writing, Navigation, i18n, Simplification, Menu Design  
**Difficulty**: Beginner

## Source

Based on code review feedback from Aetheron Connect V2 project:

> "Some of the menu item texts feel a bit verbose. Could simplify them a bit, like Analytics/Team/Call History."

Implemented across `apps/web/src/locales/*/common.json` files.
