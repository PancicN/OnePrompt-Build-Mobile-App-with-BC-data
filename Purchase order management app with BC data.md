Build me a React Native app with the Expo Go SDK 54 for a purchase order management app called "PurchaseEase" in the GitHub organization "[GITHUB_ORG]".

The app is used by the purchase department to create, manage, and track purchase orders in Microsoft Business Central. Users can create purchase orders manually by selecting vendors and items, or from a scanned document (placeholder for Azure Document Intelligence). The app uses BC standard v2.0 APIs — no custom APIs needed.

Step 0 — Gather configuration from me:
Before writing any code, ask me ONLY for these values. Show the defaults below — if I press enter / leave blank, use these defaults:

Entra ID Client ID (default: [CLIENT_ID])
Entra ID Tenant ID (default: [TENANT_ID])
Entra ID Client Secret (default: [CLIENT_SECRET])
BC Environment Name (default: Production)
BC Company ID (default: [COMPANY_ID])
All other configuration uses these fixed values — do NOT ask for them:

Business Central API (all standard v2.0 — no custom APIs):

Base URL: https://api.businesscentral.dynamics.com/v2.0/{tenantId}/{environment}/api/v2.0/companies({companyId})
OAuth Token URL: https://login.microsoftonline.com/{tenantId}/oauth2/v2.0/token
OAuth Scope: https://api.businesscentral.dynamics.com/.default
Endpoints used:
GET/POST/PATCH/DELETE /purchaseOrders — list, create, update, delete purchase orders
GET/POST/PATCH/DELETE /purchaseOrders({id})/purchaseOrderLines — manage order lines
POST /purchaseOrders({id})/Microsoft.NAV.receiveAndInvoice — receive & invoice a PO
GET /vendors — list vendors (properties: id, number, displayName, addressLine1, city, state, country, phoneNumber, email, balance)
GET /items — list items (properties: id, number, displayName, type, unitPrice, unitCost, inventory, baseUnitOfMeasureCode, blocked)
GET /purchaseInvoices — list posted/invoiced purchase documents (properties: id, number, vendorName, totalAmountIncludingTax, status, postingDate)
GET /unitsOfMeasure — list units of measure
GET /locations — list warehouse locations
App:

Demo login: [USERNAME] / [PASSWORD]
App display name: PurchaseEase
Primary brand color: #0078D4
Store all values in src/config/appConfig.ts (gitignored) with appConfig.example.ts showing placeholders.

Step 1 — Create the GitHub repository:

Create a new repo called PurchaseEase under the [GITHUB_ORG] org
Initialize with a comprehensive README including:
Community logo
App description: "Mobile purchase order management app integrated with Business Central"
Features list
Tech stack and architecture overview
Business Central Setup Guide:
How to register an Entra ID (Azure AD) app for OAuth2 client credentials
Required API permissions: Dynamics 365 Business Central → API.ReadWrite.All
How to enable standard BC APIs (v2.0)
Confirm these entities have API pages enabled: Purchase Orders, Purchase Order Lines, Vendors, Items, Purchase Invoices, Units of Measure, Locations
Azure Document Intelligence (Future):
Placeholder section explaining how ADI can be configured for purchase invoice scanning
Custom model training for extracting vendor name, invoice number, line items, quantities, unit costs
Local Development Setup: Node.js, React Native CLI, iOS/Android prerequisites, how to run
How to copy appConfig.example.ts to appConfig.ts and fill in credentials
Screenshots placeholder sections
Step 2 — Create GitHub Issues as a backlog:
Create these issues and assign each one to copilot so the GitHub Copilot coding agent picks them up automatically. Label with priority and type:

[Infra] Project scaffolding & config — React Native + TypeScript project structure. Create src/config/appConfig.ts with real values from Step 0 and appConfig.example.ts with placeholders. Add appConfig.ts to .gitignore. Theme constants with primary color #0078D4. P0

[Infra] Centralized API service layer — Single src/services/bcApi.ts that handles:

OAuth2 client credentials token acquisition and caching (auto-refresh on expiry)
All BC API calls reading from appConfig — zero hardcoded values
Methods: getVendors(), getItems(), getPurchaseOrders(), getPurchaseOrder(id), createPurchaseOrder(data), updatePurchaseOrder(id, data), deletePurchaseOrder(id), getPurchaseOrderLines(orderId), createPurchaseOrderLine(orderId, data), updatePurchaseOrderLine(orderId, lineId, data), deletePurchaseOrderLine(orderId, lineId), receiveAndInvoice(orderId), getPurchaseInvoices(), getUnitsOfMeasure(), getLocations()
OData query support: $filter, $orderby, $top, $expand
Proper error handling with typed error responses
P0
[Infra] TypeScript types — Full interfaces matching BC API v2.0 schemas:

PurchaseOrder — id, number, orderDate, postingDate, vendorId, vendorNumber, vendorName, status (Draft/In Review/Open), totalAmountExcludingTax, totalTaxAmount, totalAmountIncludingTax, fullyReceived, buyFromAddress fields, shipToAddress fields, currencyCode, paymentTermsId, shipmentMethodId, requestedReceiptDate, discountAmount, lastModifiedDateTime
PurchaseOrderLine — id, documentId, sequence, itemId, lineType (Item/Account/Resource), lineObjectNumber, description, unitOfMeasureCode, quantity, directUnitCost, discountPercent, discountAmount, amountExcludingTax, amountIncludingTax, taxCode, expectedReceiptDate, receivedQuantity, invoicedQuantity
Vendor — id, number, displayName, addressLine1/2, city, state, country, postalCode, phoneNumber, email, balance, blocked, currencyCode, paymentTermsId
Item — id, number, displayName, type (Inventory/Service/Non-Inventory), unitPrice, unitCost, inventory, baseUnitOfMeasureCode, blocked, itemCategoryCode
PurchaseInvoice — id, number, postingDate, invoiceDate, vendorName, vendorNumber, totalAmountExcludingTax, totalAmountIncludingTax, status, orderNumber
App types: DraftOrder, FilterState, UserProfile
Navigation route param types
P0
[Infra] Navigation setup — Bottom tab navigator with icons (react-native-vector-icons/MaterialCommunityIcons):

Dashboard (home icon) — stack: DashboardHome → OrderDetail → CreateOrder
Orders (file-document icon) — stack: OrderList → OrderDetail → EditOrder
Create (plus-circle icon) — stack: CreateOrder (manual) / ScanDocument (placeholder)
Invoices (receipt icon) — PostedInvoices list
Settings (cog icon) — Settings screen
Full TypeScript route param types. P0
[Feature] Login screen — Demo credentials login ([USERNAME] / [PASSWORD]). Community branding, professional corporate blue design. AsyncStorage persistence for login state. P1

[Feature] Dashboard screen — Summary cards: Total Open Orders, Orders This Month, Pending Receipt, Total Amount (from BC). Recent orders list (last 5). Quick actions: New Order, Scan Document, View All Orders, View Invoices. Pull-to-refresh. Data from BC via centralized service. P1

[Feature] Create Purchase Order screen — Step-by-step form:

Step 1: Select vendor from searchable list (fetched from GET /vendors, show displayName, number, city). Search/filter vendors by name.
Step 2: Add line items — search and select items from GET /items (show displayName, number, unitCost, inventory). For each line: set quantity, directUnitCost (pre-filled from item.unitCost), discountPercent. Add multiple lines. Show running total.
Step 3: Order details — orderDate (date picker), requestedReceiptDate, notes/description
Step 4: Review & Submit — show full order summary with vendor info, all lines with amounts, totals. Submit button calls POST /purchaseOrders then POST /purchaseOrderLines for each line.
Show success with order number, navigate to order detail.
P1
[Feature] Order List & Detail screens —

Order List: Fetches from GET /purchaseOrders?$orderby=orderDate desc. Filter tabs: All / Draft / Open / Received. Search by order number or vendor name. Each card shows: number, vendorName, orderDate, status badge (color-coded), totalAmountIncludingTax. Pull-to-refresh.
Order Detail: Full order view with header card (vendor info, status, dates, totals), line items list (item, qty, unit cost, amount), action buttons based on status:
Draft: Edit, Delete, Submit (PATCH status to Open)
Open: Receive & Invoice (POST .../Microsoft.NAV.receiveAndInvoice), Edit
Received: View only
Edit order: PATCH order header, add/edit/delete lines
P1
[Feature] Document Scan screen (placeholder) — UI-only placeholder screen for future ADI integration. Shows: camera/file picker button, a mock "scanning..." animation, a placeholder result card with extracted fields (vendor, invoice number, line items, amounts). A "Create Order from Scan" button that navigates to Create Order screen with pre-filled mock data. Banner saying "AI Document Extraction — Coming Soon" with a brief description. P1

[Feature] Posted Invoices screen — Fetches from GET /purchaseInvoices?$orderby=postingDate desc. List view with: invoice number, vendorName, postingDate, totalAmountIncludingTax, status badge. Search and date range filter. Tap to view detail (show all invoice fields in a read-only card). P1

[Feature] Settings/Profile screen — User info display (logged in user). Clear cached data button. App version. Logout button. About section describing the app. API connection status indicator (test BC connection on screen load). P2

[Feature] Reusable components — VendorCard, ItemCard, OrderCard, InvoiceCard, StatusBadge (color-coded: Draft=gray, Open=blue, In Review=orange, Received=green), FormInput, SearchableList, QuantityInput, CurrencyDisplay, EmptyState, LoadingOverlay, SectionHeader, ActionButton. P2

Step 3 — Build each issue:
For each issue: create a feature branch (feature/issue-{number}-short-name), implement the code, push it, open a PR linked to the issue, and auto-merge the PR into main. Do this for all 12 issues sequentially.

Step 4 — After all PRs are merged to main:
Once all code is merged, clone the repo locally (or pull latest main), install dependencies (npm install), install iOS pods (cd ios && pod install), and start the iOS simulator (npx react-native run-ios).

Tech stack:

React Native 0.80+ with TypeScript
React Navigation 7 (bottom tabs + native stack)
axios for HTTP requests
@react-native-async-storage/async-storage
@react-native-community/datetimepicker
react-native-vector-icons (MaterialCommunityIcons)
react-native-gesture-handler + react-native-reanimated
Design:

Corporate blue (#0078D4) primary color, white backgrounds, subtle gray borders
Professional card-based layouts with shadows
Status badges: Draft (#8E8E93 gray), Open (#0078D4 blue), In Review (#FF9500 orange), Received/Posted (#34C759 green)
Consistent 16px padding, 12px border radius
Proper loading spinners, error states with retry, empty states with illustrations
SafeAreaView and StatusBar on all screens
Currency formatting for all amounts
Pull-to-refresh on all list screens
