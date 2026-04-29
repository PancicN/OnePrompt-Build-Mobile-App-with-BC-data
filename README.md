# OnePrompt — Build a Mobile App with Business Central Data

A collection of **single-prompt blueprints** for generating a complete, production-ready React Native purchase order management app using AI coding agents (e.g. GitHub Copilot Coding Agent).

Each prompt in this repository is a self-contained specification that takes an AI agent from zero to a fully scaffolded, feature-complete mobile app — including GitHub repo creation, issue backlog, feature branches, pull requests, and auto-merge — all triggered by one prompt.

---

## What Is This?

This repo stores "OnePrompt" specifications. A OnePrompt is a single, comprehensive prompt you paste into an AI coding agent. The agent then:

1. Asks you for a small set of configuration values (credentials, environment name, etc.)
2. Creates a GitHub repository with a full README
3. Generates a GitHub Issues backlog (assigned to Copilot for autonomous coding)
4. Builds each feature on its own branch, opens a PR, and merges it
5. Optionally runs the app in a simulator after all PRs are merged

No manual scaffolding. No partial specs. One prompt → working app.

---

## Prompts in This Repo

### 1. [Purchase order management app with BC data](./Purchase%20order%20management%20app%20with%20BC%20data.md)

Builds **PurchaseEase** — a React Native purchase order management app fully integrated with **Microsoft Business Central** via standard v2.0 APIs.

| Detail | Value |
|---|---|
| App name | PurchaseEase |
| Backend | Microsoft Business Central (standard v2.0 APIs) |
| Auth | OAuth2 client credentials (Entra ID) |
| Target SDK | Expo Go SDK 54 / React Native 0.80+ |
| Primary color | `#0078D4` |

**What gets built:**
- OAuth2 token acquisition and caching against Entra ID
- Full BC API service layer (purchase orders, lines, vendors, items, invoices, UoM, locations)
- Login, Dashboard, Create PO (4-step wizard), Order List & Detail, Posted Invoices, Document Scan placeholder, Settings
- 12 GitHub Issues → 12 feature branches → 12 PRs → merged to `main`

**Configuration asked at runtime:**
- Entra ID Client ID, Tenant ID, Client Secret
- BC Environment Name (default: `Production`)
- BC Company ID

---

### 2. [OnePrompt Build Mobile App with DEMO-DATA](./OnePrompt%20Build%20Mobile%20App%20with%20DEMO-DATA.md)

Builds **PurchaseEase** in **demo mode** — identical UI and feature set, but powered entirely by a local mock data layer. No real API credentials required.

| Detail | Value |
|---|---|
| App name | PurchaseEase (configurable) |
| Backend | Local mock data (`src/services/mockData.ts`) |
| Auth | Demo login (configurable username/password) |
| Target SDK | Expo Go SDK 54 / React Native 0.80+ |
| Primary color | `#0078D4` |

**What gets built:**
- In-memory mock service with simulated async delays (300–600 ms)
- Seeded demo data: 10+ orders, 8+ vendors, 15+ items, 5+ invoices, standard UoM, 3 locations
- Full state persistence within the app session (creates/updates/deletes reflected in memory)
- "Reset to seed data" button in Settings
- "Running in Demo Mode" banner throughout the app
- Same 12-issue → 12-branch → 12-PR workflow as the BC variant

**Configuration asked at runtime:**
- Demo username and password
- App environment name (default: `Demo`)

---

## App Features (Both Variants)

- **Login screen** — branded corporate blue login with AsyncStorage session persistence
- **Dashboard** — summary cards (open orders, this month, pending receipt, total amount) + recent orders + quick actions
- **Create Purchase Order** — 4-step wizard: select vendor → add line items → order details → review & submit
- **Order List & Detail** — filterable by status (All / Draft / Open / Received), search by order number or vendor, full detail view with status-driven action buttons
- **Document Scan (placeholder)** — UI shell for future Azure Document Intelligence integration with "Coming Soon" banner
- **Posted Invoices** — searchable, date-filtered list with detail view
- **Settings / Profile** — connection status, cache controls, logout, app version
- **Reusable component library** — StatusBadge, VendorCard, ItemCard, OrderCard, InvoiceCard, SearchableList, CurrencyDisplay, EmptyState, LoadingOverlay, and more

---

## Tech Stack

| Layer | Technology |
|---|---|
| Framework | React Native 0.80+ with TypeScript |
| Navigation | React Navigation 7 (bottom tabs + native stack) |
| HTTP | axios |
| Storage | @react-native-async-storage/async-storage |
| Date picker | @react-native-community/datetimepicker |
| Icons | react-native-vector-icons (MaterialCommunityIcons) |
| Gestures | react-native-gesture-handler + react-native-reanimated |

---

## Design System

- Primary color: `#0078D4` (Microsoft blue)
- White backgrounds, subtle gray borders (`#E5E5EA`)
- Card-based layouts with soft shadows
- Status badge colors: Draft `#8E8E93`, Open `#0078D4`, In Review `#FF9500`, Received/Posted `#34C759`
- Consistent 16 px padding, 12 px border radius
- SafeAreaView + StatusBar on every screen
- Pull-to-refresh on all list screens
- Loading spinners, typed error states with retry, illustrated empty states

---

## How to Use a Prompt

1. Open your AI coding agent (e.g. GitHub Copilot Workspace or Claude Code)
2. Paste the contents of the prompt file you want to use
3. Answer the short configuration questions when asked
4. Let the agent do the rest — it will create the repo, issues, branches, PRs, and merge everything automatically

---

## Repository Structure

```
OnePrompt-Build-Mobile-App-with-BC-data/
├── README.md                                          ← this file
├── Purchase order management app with BC data.md     ← BC API variant prompt
└── OnePrompt Build Mobile App with DEMO-DATA.md      ← Demo data variant prompt
```

---

## Future Prompts (Planned)

- **OnePrompt — Sales Order App with BC data**
- **OnePrompt — Inventory Management App with BC data**
- **OnePrompt — Warehouse App with BC data**
