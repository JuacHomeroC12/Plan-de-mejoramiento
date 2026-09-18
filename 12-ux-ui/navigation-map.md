# Navigation Map — FIXGO

> Defines the screen structure of the FIXGO platform, how screens connect to each other, and what routes exist. It is the reference when frontend and backend discuss endpoints or features.

---

## Frontend route structure

> **Instruction:** Fill this tree with your application's real routes.
> Use the `[method] /route` format for API endpoints where applicable.

```
/                           → Home /landing page
├── /auth
│   ├── /login              → Authentication form
│   ├── /register-driver    → New driver registration
│   └── /register-mechanic  → New mechanic registration
│
├── /driver                 → Driver panel (role: DRIVER)
│   ├── /dashboard          → Active request status and history summary
│   └── /request-assistance → Emergency breakdown request flow
│   └── /profile            → Driver account profile
│ 
├── /[resource-a]           → [Resource A] list
│   ├── /new                → Creation form
│   └── /:id
│       ├── /               → Resource detail
│       └── /edit           → Edit form
│
├── /[resource-b]           → [Resource B] list
│   └── /:id                → Detail
│
├── /admin                  → Administration panel (role: ADMIN)
│   ├── /users              → User management
│   └── /settings           → System configuration
│
└── /profile                → Authenticated user's profile
```

---

## Screen map

| Screen | Route | Component | Minimum role | Backend service |
|---|---|---|---|---|
| Home | `/` | `HomePage` | Public | — |
| Login | `/auth/login` | `LoginPage` | Public | api-gateway / auth |
| Driver Dashboard | `/driver/dashboard` | `DriverDashboardPage` | DRIVER | order-service |
| Request Assistance | `/driver/request-assistance` | `RequestAssistancePage` | DRIVER | order-service |
| Mechanic Dashboard | `/mechanic/dashboard` | `MechanicDashboardPage` | MECHANIC | order-service |
| Active Service | `/mechanic/active-service` | `ActiveServicePage` | MECHANIC | tracking-service |
| Admin Panel | `/admin` | `AdminDashboardPage` | ADMIN | api-gateway |

---

## Main user flows

### Flow 1 — [Name of main flow]

```
[Start screen]
    │
    ▼ [User action]
[Screen 2]
    │
    ├── [Successful case] ──► [OK result screen]
    │
    └── [Error case] ────► [Error screen / feedback]
```

**Related HUs:** HU-[service]-001, HU-[service]-002

### Flow 2 — Authentication

```
Landing (/)
    │
    ▼ Click "Sign in"
Login (/auth/login)
    │
    ├── Valid credentials ──► Dashboard (/dashboard)
    │
    └── Invalid credentials ► Login with error message (max. 5 attempts)
```

**Related HUs:** HU-ORDER-003, HU-TRACK-001

---

## Navigation rules

| Rule | Description |
|---|---|
| Authentication | Routes under `/driver`, `/mechanic`, and `/admin` automatically redirect to `/auth/login` if no valid session token exists |
| Authorization | Accessing `/admin` as a DRIVER or MECHANIC redirects to their respective dashboard |
| 404 | Undefined routes display a custom 404 error screen with a direct link back to the user's dashboard |
| Confirmation | Destructive actions (such as canceling an active emergency request) require explicit confirmation via modal |

---

## Correlations

- Design system (visual components) → `12-ux-ui/design-system.md`
- Wireframes → `12-ux-ui/wireframes/`
- Frontend API contracts → `07-api/contracts/openapi/`
- Roles and permissions → `00-governance/security-policy.md`