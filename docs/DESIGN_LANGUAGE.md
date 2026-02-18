# TD3 Design Language System

> A sophisticated design system combining **Material Design** principles with **Polymorphic UI** adaptability for the TD3 Construction Draw Management platform.

---

## Table of Contents

1. [Design Philosophy](#1-design-philosophy)
2. [Color System](#2-color-system)
3. [Typography](#3-typography)
4. [Spacing & Layout](#4-spacing--layout)
5. [Elevation & Shadows](#5-elevation--shadows)
6. [Motion & Animation](#6-motion--animation)
7. [Polymorphic Behaviors](#7-polymorphic-behaviors)
8. [Accessibility](#8-accessibility)

---

## 1. Design Philosophy

### 1.1 Core Principles

TD3's design language merges two powerful paradigms:

#### Material Foundation
> *"Digital surfaces that behave like physical materials"*

- **Tactile Realism** — Surfaces have depth, weight, and respond to interaction
- **Elevation Hierarchy** — Shadows communicate spatial relationships
- **Meaningful Motion** — Animations provide continuity and feedback
- **Grid Consistency** — Layouts built on a predictable 4px base unit

#### Polymorphic Intelligence
> *"Interfaces that adapt to context, state, and user needs"*

- **Context-Aware Styling** — Colors and emphasis shift based on content status
- **Emotionally Responsive** — Visual feedback reflects action outcomes
- **Role-Based Adaptation** — Interface emphasis changes per user role
- **Content-Driven Morphing** — Components reshape to fit their data

### 1.2 Design DNA

```
TD3 = Material Depth + Polymorphic Intelligence + Financial Precision
```

The interface should feel:
- **Professional** — Clean, trustworthy, suited for financial workflows
- **Intelligent** — Anticipates needs, surfaces relevant information
- **Responsive** — Every interaction has immediate, satisfying feedback
- **Accessible** — Works for all users in any lighting condition

This document describes the visual language that shapes every screen in TD3. While it includes technical specifications for implementation consistency, the principles and patterns described here directly reflect the user experience. For how these design principles serve the business workflow, see the [README](../README.md).

---

## 2. Color System

### 2.1 Brand Colors

TD3's brand is anchored in a **Dark Red/Maroon** accent that conveys:
- **Authority** — Financial trust and institutional strength
- **Urgency** — Draws attention to important actions
- **Sophistication** — Rich, deep tones for premium feel

#### Primary Accent Scale (Dark Red)

Based on `#950606` with accessibility-optimized variants.

The CSS default (`:root`) is dark mode; light mode is applied via the `.light` class. The accent-500 value (`#950606`) is consistent across both modes.

| Token | Light Mode (`.light`) | Dark Mode (`:root`) | Usage |
|-------|------------|-----------|-------|
| `--accent-50` | `#FEF2F2` | `#1A0808` | Subtle backgrounds, glows |
| `--accent-100` | `#FEE2E2` | `#2D1010` | Hover backgrounds |
| `--accent-200` | `#FECACA` | `#451515` | Active backgrounds |
| `--accent-300` | `#FCA5A5` | `#5C1B1B` | Borders, dividers |
| `--accent-400` | `#F87171` | `#7A2020` | Secondary elements |
| `--accent-500` | `#950606` | `#950606` | **Primary accent** |
| `--accent-600` | `#840505` | `#B00808` | Hover state |
| `--accent-700` | `#740505` | `#C41919` | Text on dark backgrounds |
| `--accent-800` | `#530303` | `#D83232` | High contrast text |
| `--accent-900` | `#320202` | `#E85050` | Maximum contrast |

#### Accent Utility Tokens

| Token | Light Mode | Dark Mode | Usage |
|-------|------------|-----------|-------|
| `--accent` | `var(--accent-500)` | `var(--accent-500)` | Shorthand alias |
| `--accent-hover` | `var(--accent-600)` | `var(--accent-600)` | Hover state alias |
| `--accent-glow` | `rgba(149, 6, 6, 0.2)` | `rgba(149, 6, 6, 0.35)` | Glow effects |
| `--accent-muted` | `rgba(149, 6, 6, 0.1)` | `rgba(149, 6, 6, 0.20)` | Muted backgrounds |

**Accessibility Note:** The accent color `#950606` achieves **9.1:1 contrast ratio** on white (AAA). In dark mode, the same `#950606` is used as the primary accent with lighter tints for higher scales.

### 2.2 Neutral Palette

Carefully calibrated for eye comfort in extended use:

#### Light Mode (`.light` class)

| Token | Value | Usage |
|-------|-------|-------|
| `--bg-primary` | `#FFFFFF` | Page background |
| `--bg-secondary` | `#F9FAFB` | Sidebars, headers |
| `--bg-tertiary` | `#F3F4F6` | Nested containers |
| `--bg-card` | `#FFFFFF` | Cards, elevated surfaces |
| `--bg-card-transparent` | `rgba(255, 255, 255, 0.85)` | Translucent card surfaces |
| `--bg-hover` | `#E5E7EB` | Interactive hover states |
| `--bg-active` | `#D1D5DB` | Active/pressed states |
| `--border` | `#D1D5DB` | Prominent borders |
| `--border-subtle` | `#E5E7EB` | Subtle separators |
| `--border-accent` | `var(--accent-500)` | Accent-colored borders |
| `--text-primary` | `#111827` | Primary content |
| `--text-secondary` | `#374151` | Secondary content |
| `--text-muted` | `#6B7280` | Hints, placeholders |
| `--text-disabled` | `#9CA3AF` | Disabled states |
| `--text-inverse` | `#FFFFFF` | Text on accent backgrounds |

#### Dark Mode (`:root` default)

| Token | Value | Usage |
|-------|-------|-------|
| `--bg-primary` | `#151518` | Page background |
| `--bg-secondary` | `#1C1C20` | Sidebars, headers |
| `--bg-tertiary` | `#232328` | Nested containers |
| `--bg-card` | `#1E1E23` | Cards, elevated surfaces |
| `--bg-card-transparent` | `rgba(30, 30, 35, 0.85)` | Translucent card surfaces |
| `--bg-hover` | `#2D2D33` | Interactive hover states |
| `--bg-active` | `#3A3A40` | Active/pressed states |
| `--border` | `#3F3F46` | Prominent borders |
| `--border-subtle` | `#27272A` | Subtle separators |
| `--border-accent` | `var(--accent-400)` | Accent-colored borders |
| `--text-primary` | `#FAFAFA` | Primary content |
| `--text-secondary` | `#A1A1AA` | Secondary content |
| `--text-muted` | `#71717A` | Hints, placeholders |
| `--text-disabled` | `#52525B` | Disabled states |
| `--text-inverse` | `#151518` | Text on accent backgrounds |

### 2.3 Semantic Colors

Context-aware colors that adapt to light/dark modes. Each semantic color has four variants: base, hover, muted (backgrounds), and glow (focus effects).

| Purpose | Light Mode (`.light`) | Dark Mode (`:root`) | Usage |
|---------|------------|-----------|-------|
| Success | `#059669` | `#059669` | Approvals, completions |
| Success Hover | `#047857` | `#10B981` | Hover state |
| Success Muted | `rgba(5, 150, 105, 0.1)` | `rgba(5, 150, 105, 0.15)` | Success badges/backgrounds |
| Success Glow | `rgba(5, 150, 105, 0.2)` | `rgba(5, 150, 105, 0.25)` | Focus ring glow |
| Warning | `#D97706` | `#D97706` | Pending, attention |
| Warning Hover | `#B45309` | `#F59E0B` | Hover state |
| Warning Muted | `rgba(217, 119, 6, 0.1)` | `rgba(217, 119, 6, 0.15)` | Warning badges/backgrounds |
| Warning Glow | `rgba(217, 119, 6, 0.2)` | `rgba(217, 119, 6, 0.25)` | Focus ring glow |
| Error | `#DC2626` | `#DC2626` | Errors, rejections |
| Error Hover | `#B91C1C` | `#EF4444` | Hover state |
| Error Muted | `rgba(220, 38, 38, 0.1)` | `rgba(220, 38, 38, 0.15)` | Error badges/backgrounds |
| Error Glow | `rgba(220, 38, 38, 0.2)` | `rgba(220, 38, 38, 0.25)` | Focus ring glow |
| Info | `#2563EB` | `#2563EB` | Informational states |
| Info Hover | `#1D4ED8` | `#3B82F6` | Hover state |
| Info Muted | `rgba(37, 99, 235, 0.1)` | `rgba(37, 99, 235, 0.15)` | Info badges/backgrounds |
| Info Glow | `rgba(37, 99, 235, 0.2)` | `rgba(37, 99, 235, 0.25)` | Focus ring glow |

### 2.4 Complementary Accent Colors

For data visualization and special emphasis:

| Color | Value | CSS Token | Usage |
|-------|-------|-----------|-------|
| **Teal** | `#0D9488` | `--teal` | Complementary accent, charts |
| **Gold** | `#D97706` | `--gold` | Premium highlights, high-value |
| **Purple** | `#7C3AED` | `--purple` | Wire/processing status, charts |
| **Deep Blue** | `#2563EB` | `--deep-blue` | Secondary charts, depth |

Each complementary color also has a `-muted` variant (e.g., `--teal-muted`) for background tints.

### 2.5 Data Visualization Palette

All charts in TD3 use a **single stable color mapping** for NAHB construction categories. This ensures a user can visually identify the same category across the Fund Flow (Sankey), Funding Timeline (stacked area), and Budget Breakdown (pie) charts without referring to a legend.

| Category | Hex | Swatch | Construction Phase |
|----------|-----|--------|--------------------|
| Builder Expense | `#64748B` | Slate | Pre-construction |
| General Conditions | `#EF4444` | Red | Project setup |
| Site Work | `#F97316` | Orange | Site preparation |
| Concrete & Foundations | `#A8A29E` | Stone | Foundation |
| Framing | `#3B82F6` | Blue | Structural |
| Exterior Finishes | `#10B981` | Emerald | Exterior |
| Plumbing | `#06B6D4` | Cyan | Rough-in MEP |
| HVAC / Mechanical | `#8B5CF6` | Violet | Rough-in MEP |
| Electrical | `#EAB308` | Yellow | Rough-in MEP |
| Insulation, Drywall & Paint | `#EC4899` | Pink | Interior rough |
| Interior Finishes | `#84CC16` | Lime | Interior finish |
| Landscaping & Exterior Amenities | `#14B8A6` | Teal | Final |

Colors are defined in `lib/constants.ts` as `NAHB_CATEGORY_COLORS` and retrieved via `getCategoryColor()`. Unknown categories fall back to `#9CA3AF` (gray).

> The complementary accent colors in Section 2.4 remain available for non-category data (e.g., wire status, loan-level indicators). Subcategory drill-down charts use a separate categorical palette since they represent items within a single category.

### 2.6 Status-Specific Palettes (Polymorphic)

The interface tints based on workflow status:

| Status | Tint | Application |
|--------|------|-------------|
| **Draft** | Neutral gray | Muted, incomplete feel |
| **Pending Review** | Amber/Gold | Awaiting attention |
| **Staged** | Soft blue | Ready state |
| **Approved** | Green | Positive confirmation |
| **Rejected** | Red | Requires action |
| **Pending Wire** | Purple | Financial processing |
| **Paid/Complete** | Deep green | Final success |

---

## 3. Typography

### 3.1 Font Stack

TD3 uses **Inter** as the primary UI typeface and **JetBrains Mono** for monospaced content (financial figures, code). A display alias maps to Inter for large headlines.

**Why Inter?**
- Designed specifically for computer screens
- Excellent legibility at small sizes
- Clear distinction between similar characters (1, l, I)
- Variable font support for precise weight control

### 3.2 Type Scale

Based on a **1.25 ratio** (Major Third) with 16px base:

| Token | Size | Weight | Line Height | Letter Spacing | Usage |
|-------|------|--------|-------------|----------------|-------|
| `--text-xs` | 11px | 400 | 1.5 | 0.02em | Micro labels, timestamps |
| `--text-sm` | 13px | 400 | 1.5 | 0.01em | Secondary text, captions |
| `--text-base` | 16px | 400 | 1.5 | 0 | Body copy, default |
| `--text-lg` | 18px | 500 | 1.4 | -0.01em | Emphasized body |
| `--text-xl` | 20px | 600 | 1.3 | -0.01em | Section headers |
| `--text-2xl` | 24px | 700 | 1.2 | -0.02em | Page titles |
| `--text-3xl` | 30px | 700 | 1.1 | -0.02em | Dashboard headers |
| `--text-4xl` | 36px | 800 | 1.1 | -0.03em | Hero text |

### 3.3 Font Weights

| Weight | Value | Usage |
|--------|-------|-------|
| Regular | 400 | Body text, descriptions |
| Medium | 500 | Labels, emphasized text |
| Semibold | 600 | Buttons, headers |
| Bold | 700 | Titles, key metrics |
| Extrabold | 800 | Display numbers |

### 3.4 Numeric Typography

Financial data uses **tabular figures** for column alignment. The monospace font stack (JetBrains Mono) is applied alongside `font-variant-numeric: tabular-nums` to ensure equal-width digits, keeping currency columns perfectly aligned regardless of digit composition.

---

## 4. Spacing & Layout

### 4.1 Base Unit

All spacing derives from a **4px base unit**:

| Token | Value | Usage |
|-------|-------|-------|
| `--space-0` | 0px | No spacing |
| `--space-0.5` | 2px | Micro adjustments |
| `--space-1` | 4px | Tight gaps, icon padding |
| `--space-2` | 8px | Related elements |
| `--space-3` | 12px | Component internal padding |
| `--space-4` | 16px | Standard gap |
| `--space-5` | 20px | Card padding |
| `--space-6` | 24px | Section spacing |
| `--space-8` | 32px | Large gaps |
| `--space-10` | 40px | Section dividers |
| `--space-12` | 48px | Page sections |
| `--space-16` | 64px | Major sections |

### 4.2 Border Radius

iOS-inspired rounded corners with consistent proportions:

| Token | Value | Usage |
|-------|-------|-------|
| `--radius-none` | 0px | Sharp corners |
| `--radius-xs` | 4px | Small badges, pills |
| `--radius-sm` | 6px | Buttons, inputs |
| `--radius-md` | 8px | Small cards, dropdowns |
| `--radius-lg` | 12px | Cards, panels |
| `--radius-xl` | 16px | Modals, sheets |
| `--radius-2xl` | 20px | Large containers |
| `--radius-full` | 9999px | Pills, avatars |

### 4.3 Layout Grid

#### Container Widths

| Size | Max Width | Usage |
|------|-----------|-------|
| `--container-sm` | 640px | Narrow content |
| `--container-md` | 768px | Standard content |
| `--container-lg` | 1024px | Wide content |
| `--container-xl` | 1280px | Full layouts |
| `--container-2xl` | 1536px | Maximum width |

#### Column System

- 12-column grid for primary content
- 16px gutters (--space-4)
- Sidebar widths: 256px (16rem) default

---

## 5. Elevation & Shadows

### 5.1 Elevation Scale

Material Design-inspired depth system:

```
┌─────────────────────────────────────────────┐  Level 0: Page canvas
│  ┌───────────────────────────────────────┐  │  Level 1: Sidebars, toolbars
│  │  ┌─────────────────────────────────┐  │  │  Level 2: Cards, panels
│  │  │  ┌───────────────────────────┐  │  │  │  Level 3: Dropdowns, popovers
│  │  │  │  ┌─────────────────────┐  │  │  │  │  Level 4: Modals, sheets
│  │  │  │  │  ┌───────────────┐  │  │  │  │  │  Level 5: Toasts, tooltips
│  │  │  │  │  │               │  │  │  │  │  │
└──┴──┴──┴──┴──┴───────────────┴──┴──┴──┴──┴──┘
```

### 5.2 Shadow Tokens

| Token | Light Mode | Dark Mode |
|-------|------------|-----------|
| `--elevation-0` | none | none |
| `--elevation-1` | `0 1px 2px rgba(0,0,0,0.05)` | `0 1px 2px rgba(0,0,0,0.3)` |
| `--elevation-2` | `0 1px 3px rgba(0,0,0,0.1), 0 1px 2px rgba(0,0,0,0.06)` | `0 1px 3px rgba(0,0,0,0.4), 0 1px 2px rgba(0,0,0,0.3)` |
| `--elevation-3` | `0 4px 6px rgba(0,0,0,0.1), 0 2px 4px rgba(0,0,0,0.06)` | `0 4px 6px rgba(0,0,0,0.5), 0 2px 4px rgba(0,0,0,0.3)` |
| `--elevation-4` | `0 10px 15px rgba(0,0,0,0.1), 0 4px 6px rgba(0,0,0,0.05)` | `0 10px 15px rgba(0,0,0,0.6), 0 4px 6px rgba(0,0,0,0.3)` |
| `--elevation-5` | `0 20px 25px rgba(0,0,0,0.15), 0 8px 10px rgba(0,0,0,0.08)` | `0 20px 25px rgba(0,0,0,0.7), 0 8px 10px rgba(0,0,0,0.4)` |

### 5.3 Glow Effects

Glow effects are constructed using the semantic color tokens. Each semantic color provides a glow variant used for focus rings and interactive emphasis:

| Glow Token | Light Mode | Dark Mode | Usage |
|------------|------------|-----------|-------|
| `--accent-glow` | `rgba(149, 6, 6, 0.2)` | `rgba(149, 6, 6, 0.35)` | Button hover, card interactive hover |
| `--success-glow` | `rgba(5, 150, 105, 0.2)` | `rgba(5, 150, 105, 0.25)` | Success focus rings |
| `--error-glow` | `rgba(220, 38, 38, 0.2)` | `rgba(220, 38, 38, 0.25)` | Error focus rings |
| `--warning-glow` | `rgba(217, 119, 6, 0.2)` | `rgba(217, 119, 6, 0.25)` | Warning focus rings |
| `--info-glow` | `rgba(37, 99, 235, 0.2)` | `rgba(37, 99, 235, 0.25)` | Info focus rings |

### 5.4 Surface Treatments

| Surface | Background | Border | Shadow | Usage |
|---------|------------|--------|--------|-------|
| **Page** | `--bg-primary` | none | none | Base canvas |
| **Panel** | `--bg-secondary` | `--border-subtle` | `--elevation-1` | Sidebars, headers |
| **Card** | `--bg-card` | `--border-subtle` | `--elevation-2` | Content containers |
| **Raised** | `--bg-card` | `--border` | `--elevation-3` | Interactive cards |
| **Floating** | `--bg-card` | `--border` | `--elevation-4` | Dropdowns, popovers |
| **Modal** | `--bg-card` | `--border` | `--elevation-5` | Dialogs, sheets |

---

## 6. Motion & Animation

### 6.1 Timing Functions

| Token | Curve | Description | Usage |
|-------|-------|-------------|-------|
| `--ease-default` | `cubic-bezier(0.4, 0, 0.2, 1)` | Standard deceleration curve | Most transitions |
| `--ease-out` | `cubic-bezier(0, 0, 0.2, 1)` | Fast start, gentle stop | Elements entering the viewport |
| `--ease-in` | `cubic-bezier(0.4, 0, 1, 1)` | Gentle start, fast finish | Elements exiting the viewport |
| `--ease-spring` | `cubic-bezier(0.34, 1.56, 0.64, 1)` | Overshoot with settle | Playful feedback (buttons, toggles) |
| `--ease-bounce` | `cubic-bezier(0.68, -0.55, 0.265, 1.55)` | Bounce at both ends | Success states, celebrations |

### 6.2 Duration Scale

| Token | Value | Usage |
|-------|-------|-------|
| `--duration-instant` | 50ms | Micro-feedback |
| `--duration-fast` | 100ms | Hover states, toggles |
| `--duration-normal` | 200ms | Standard transitions |
| `--duration-slow` | 300ms | Complex animations |
| `--duration-slower` | 400ms | Page transitions |
| `--duration-slowest` | 500ms | Elaborate sequences |

### 6.3 Animation Patterns

#### Micro-interactions

- **Button press** — Buttons scale down slightly (to 97%) on press using the spring timing function, providing tactile feedback that the action registered.
- **Hover lift** — Cards translate upward by 2px on hover, with shadow deepening from elevation-2 to elevation-3, creating a "pick up" effect using the decelerate curve.

#### State Transitions

- **Fade in** — Elements transition from fully transparent to opaque. Used for content appearing in place.
- **Scale in** — Elements grow from 95% to full size while fading in. Used for modals, popovers, and dropdowns entering the viewport.
- **Slide up** — Elements translate upward 10px while fading in. Used for list items, cards, and staggered content loads.
- **Shake** — A horizontal oscillation of 4px left and right, repeating over 5 keyframes. Used for form validation errors to draw attention to the problem field.

#### Loading States

- **Skeleton shimmer** — A gradient slides across the placeholder surface using a 200% background-size animation over 1.5 seconds, creating a "scanning" effect that indicates content is loading.
- **Pulse** — Opacity oscillates between 100% and 50% to indicate an ongoing background process.
- **Spinner** — A continuous 360-degree rotation for determinate or indeterminate loading indicators.

### 6.4 Reduced Motion

TD3 respects the `prefers-reduced-motion` media query. When a user has requested reduced motion at the OS level, all animation durations and transition durations are effectively zeroed out so that state changes are instantaneous without visual motion.

---

## 7. Polymorphic Behaviors

### 7.1 Context-Aware Adaptations

The interface shifts appearance based on context:

| Context | Visual Adaptation |
|---------|-------------------|
| **Light/Dark Mode** | Full palette inversion with contrast preservation |
| **Pipeline View** | Cooler tones, analytical feel |
| **Active Loans** | Warmer accent presence, action-oriented |
| **Historic Data** | Muted saturation, archival feel |
| **Error State** | Red tinting, heightened attention |
| **Success State** | Green pulse, celebratory feedback |

### 7.2 Status-Driven Styling

Components adapt based on data status:

```tsx
// Example: Card tints based on status
const statusTints = {
  draft: 'var(--bg-secondary)',
  pending_review: 'rgba(245, 158, 11, 0.08)',
  staged: 'rgba(59, 130, 246, 0.08)',
  approved: 'rgba(16, 185, 129, 0.08)',
  rejected: 'rgba(239, 68, 68, 0.08)',
  pending_wire: 'rgba(139, 92, 246, 0.08)',
  paid: 'rgba(16, 185, 129, 0.12)',
}
```

These status-driven visual cues are central to the [AI confidence system](ARTIFICIAL_INTELLIGENCE.md#confidence-and-trust), where color communicates match certainty.

### 7.3 Emotionally Responsive Feedback

Actions trigger appropriate emotional responses:

| Action | Visual Feedback |
|--------|-----------------|
| **Form Submit** | Button loading state → success pulse |
| **Validation Pass** | Green checkmark slide-in |
| **Validation Fail** | Red shake + persistent indicator |
| **Task Complete** | Celebratory scale-up + confetti (optional) |
| **Data Load** | Skeleton shimmer |
| **Delete** | Fade out + collapse |
| **Undo Available** | Toast with countdown progress |

### 7.4 Amount-Based Emphasis

Financial values receive dynamic emphasis:

| Amount Range | Treatment |
|--------------|-----------|
| < $10,000 | Standard text |
| $10,000 - $100,000 | Medium weight |
| $100,000 - $1,000,000 | Bold + slightly larger |
| > $1,000,000 | Bold + accent color + gold tint |

### 7.5 Role-Based Adaptations (Future)

| Role | Interface Emphasis |
|------|-------------------|
| **Admin** | Full feature access, system controls visible |
| **Lender** | Portfolio focus, approval workflows prominent |
| **Bookkeeper** | Financial data emphasis, wire actions highlighted |
| **Builder** | Draw submission simplified, status tracking central |

See the [Roadmap](ROADMAP.md#builder--lender-portals) for upcoming role-specific interfaces.

---

## 8. Accessibility

### 8.1 Color Contrast Requirements

All color combinations must meet **WCAG 2.1 AA** standards:

| Usage | Minimum Ratio | Target |
|-------|---------------|--------|
| Body text | 4.5:1 | 7:1 (AAA) |
| Large text (18px+) | 3:1 | 4.5:1 |
| UI components | 3:1 | 4.5:1 |
| Focus indicators | 3:1 | 4.5:1 |

### 8.2 Focus States

All interactive elements must have visible focus indicators. The default focus style uses a 2px accent-colored outline with 2px offset. Buttons use a custom double-ring focus style: a 2px background-colored inner ring surrounded by a 2px accent-colored outer ring, creating a clear halo effect that works on any surface.

### 8.3 Keyboard Navigation

- All interactive elements must be keyboard accessible
- Tab order follows visual flow
- Modal traps focus appropriately
- Escape closes overlays

### 8.4 Screen Reader Support

- Use semantic HTML elements
- Include ARIA labels where needed
- Announce dynamic content changes
- Provide text alternatives for icons

### 8.5 Touch Targets

Minimum touch target size: **44x44px** for all interactive elements.

---

## Appendix A: Color Reference Card

### Dark Red Accent

| Hex | Usage |
|-----|-------|
| `#FEF2F2` | Lightest tint |
| `#950606` | Primary (light mode) |
| `#DC2626` | Primary (dark mode) |
| `#320202` | Darkest shade |

### Complementary Colors

| Hex | Usage |
|-----|-------|
| `#0D9488` | Teal (complementary) |
| `#D97706` | Gold (premium) |
| `#7C3AED` | Purple (wire/processing) |
| `#2563EB` | Deep Blue (depth) |

---


### Upcoming Design Work

See the [Development Roadmap](ROADMAP.md) for detailed timelines on features that will require new design patterns:

- **Builder Portal** — Simplified interface for external builders to view loans and submit draws
- **Lender Portal** — Read-only portfolio view for lending partners
- **Mobile Inspection App** — Touch-optimized interface for field inspections and photo capture

## Related Documentation

| Document | Description |
|----------|-------------|
| [README](../README.md) | Project overview, workflow summary, and documentation index |
| [Architecture](ARCHITECTURE.md) | System architecture, data model, and deployment |
| [Security](SECURITY.md) | Authentication, permissions, data-level enforcement, and audit trail |
| [Artificial Intelligence](ARTIFICIAL_INTELLIGENCE.md) | AI pipeline, cost code system, confidence model, and training data |
| [Glossary](GLOSSARY.md) | Definitions of key construction lending, financial, and platform terms |
| [Roadmap](ROADMAP.md) | Upcoming features, timeline, and development priorities |

---

*TD3 Design Language v1.3 -- © 2024-2026 TD3, built by Grayson Graham -- Last updated: February 2026*
