# 12 — UX/UI

> **What is this?** The user experience design: how the FIXGO system looks, how it is navigated, and how it behaves from the end user's perspective (Drivers, Mechanics, and Administrators).

## Why design comes before code

Changing a wireframe or a Figma component takes 5 minutes. Changing the code takes hours. Changing the code in production with real users can cost days and reputation.

**Design first → implement later.**

---

## What is here and how to fill it in

### `navigation-map.md` ⭐ (Start here)
The map of all screens/pages and how they connect in the FIXGO platform.
**Fill in:** navigation tree, from which screen you reach which, what role can access what.

**Format example for FIXGO:**
```markdown
## Navigation map

### Public area (no authentication)
- / (Landing page)
  - /login
  - /register-driver
  - /register-mechanic

### Private area — Role: Driver
- /dashboard
  - /request-assistance
    - /request-assistance/map-selection
    - /request-assistance/waiting-mechanic
  - /order-history
  - /profile

### Private area — Role: Mechanic
- /dashboard
  - /available-orders
  - /active-service
    - /active-service/navigation
  - /earnings

## Access matrix
| Screen | [Role 1] | [Role 2] | [Admin] |
|--------|---------|---------|---------|
| /dashboard | ✅ | ✅ | ✅ |
| /admin | ❌ | ❌ | ✅ |
```

### `wireframes.md`
Low-fidelity designs of the main screens.
**Fill in:** wireframes in ASCII, Figma, or Balsamiq. Focus on structure, not colors.

### `design-system.md`
The project's design system: tokens, components, patterns.
**Fill in:** color palette, typography, spacing, base components (buttons, forms, tables).

**Format:**
```markdown
## Design tokens

### Colors
| Token | Value | Use |
|-------|-------|-----|
| --color-primary | #FF6D00 | Main actions (e.g., Request Assistance button) |
| --color-secondary | #1E1E1E | Backgrounds, dark mode surfaces |
| --color-error | #D32F2F | Cancellations, payment failures |
| --color-success | #388E3C | Order completed, payment successful |

### Typography
| Level | Size | Weight | Use |
|-------|------|--------|-----|
| H1 | 32px | 700 | Main screen titles |
| Body | 16px | 400 | General text, form labels |

## Components
### Emergency Action Button
Large, high-contrast button, always accessible at the bottom of the driver's screen.

### Map View
Integrated map component showing current location (driver) or route (mechanic), utilizing custom markers for vehicle types.

---

## Correlations with other sections

| This section is fed by... | And feeds into... |
|---------------------------|-------------------|
| `04-requirements/user-stories.md` → what flows exist | Screens implementing each HU |
| `02-domain/entities-and-rules.md` → what data to display | Fields in wireframes |
| `09-microservices/` → what APIs the frontend consumes | What data arrives at each screen |

---

## Questions this section must answer

- How many screens does the system have?
- How does each type of user navigate?
- What visual components are repeated?
- What is the system's visual language?
