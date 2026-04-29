Build me a React Native app with the Expo Go SDK 54 for a purchase order management app called "[APPLICATION_NAME]" in the GitHub organization "[GITHUB_ORG]".

The app is used by the purchase department to create, manage, and track purchase orders. Users can create purchase orders manually by selecting vendors and items, or from a scanned document (placeholder for Azure Document Intelligence). The app uses local demo data — no real API connection needed.

Step 0 — Gather configuration from me:
Before writing any code, ask me ONLY for these values. Show the defaults below — if I press enter / leave blank, use these defaults:

Demo Username (default: [USERNAME])
Demo Password (default: [PASSWORD])
App Environment Name (default: Demo)
All other configuration uses these fixed values — do NOT ask for them:

Demo Data (static mock — no real API calls):

The app uses a local mock data layer stored in src/services/mockData.ts. All API service methods return this data instead of making real HTTP requests.

Mock data includes:

Purchase Orders — at least 10 sample orders across statuses: Draft, Open, In Review, Received
Purchase Order Lines — 2–4 lines per order with realistic item/qty/cost data
Vendors — at least 8 vendors with: id, number, displayName, addressLine1, city, state, country, phoneNumber, email, balance
Items — at least 15 items with: id, number, displayName, type, unitPrice, unitCost, inventory, baseUnitOfMeasureCode, blocked
Purchase Invoices — at least 5 posted invoices with: id, number, vendorName, totalAmountIncludingTax, status, postingDate
Units of Measure — standard list (EA, BOX, KG, LTR, PCS)
Locations — at least 3 warehouse locations
App:

Demo login: [USERNAME] / [PASSWORD]
App display name: PurchaseEase
Primary brand color: #0078D4
Store all values in src/config/appConfig.ts (gitignored) with appConfig.example.ts showing placeholders.
Step 1 — Create the GitHub repository:

Create a new repo called PurchaseEase under the [GITHUB_ORG] org
Initialize with a comprehensive README including:
Community logo
App description: "Mobile purchase order management app with demo data mode"
Features list
Tech stack and architecture overview
Demo Mode Guide:
How the mock data layer works
How to add or modify demo vendors, items, and orders in src/services/mockData.ts
How to swap mock service with a real BC API service in the future
Azure Document Intelligence (Future):
Placeholder section explaining how ADI can be configured for purchase invoice scanning
Custom model training for extracting vendor name, invoice number, line items, quantities, unit costs
Local Development Setup: Node.js, React Native CLI, iOS/Android prerequisites, how to run
How to copy appConfig.example.ts to appConfig.ts and fill in credentials
Screenshots placeholder sections
Step 2 — Create GitHub Issues as a backlog:
Create these issues and assign each one to copilot so the GitHub Copilot coding agent picks them up automatically. Label with priority and type:

[Infra] Project scaffolding & config — React Native + TypeScript project structure. Create src/config/appConfig.ts with demo values from Step 0 and appConfig.example.ts with placeholders. Add appConfig.ts to .gitignore. Theme constants with primary color #0078D4. P0

[Infra] Mock data service layer — Single src/services/mockData.ts and src/services/bcApi.ts that handles:

All methods return mock data with simulated async delay (300–600ms) to mimic real API behavior
Methods: getVendors(), getItems(), getPurchaseOrders(), getPurchaseOrder(id), createPurchaseOrder(data), updatePurchaseOrder(id, data), deletePurchaseOrder(id), getPurchaseOrderLines(orderId), createPurchaseOrderLine(orderId, data), updatePurchaseOrderLine(orderId, lineId, data), deletePurchaseOrderLine(orderId, lineId), receiveAndInvoice(orderId), getPurchaseInvoices(), getUnitsOfMeasure(), getLocations()
In-memory state management so creates/updates/deletes persist during the app session
Client-side filtering support: filter, orderby, top
Proper error simulation with typed error responses
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

[Feature] Dashboard screen — Summary cards: Total Open Orders, Orders This Month, Pending Receipt, Total Amount (from mock data). Recent orders list (last 5). Quick actions: New Order, Scan Document, View All Orders, View Invoices. Pull-to-refresh. Data from mock service layer. P1

[Feature] Create Purchase Order screen — Step-by-step form:

Step 1: Select vendor from searchable list (from mock vendors, show displayName, number, city). Search/filter vendors by name.
Step 2: Add line items — search and select items from mock items (show displayName, number, unitCost, inventory). For each line: set quantity, directUnitCost (pre-filled from item.unitCost), discountPercent. Add multiple lines. Show running total.
Step 3: Order details — orderDate (date picker), requestedReceiptDate, notes/description
Step 4: Review & Submit — show full order summary with vendor info, all lines with amounts, totals. Submit button calls createPurchaseOrder(data) then createPurchaseOrderLine(orderId, data) for each line against the mock service.
Show success with order number, navigate to order detail.
P1
[Feature] Order List & Detail screens —

Order List: Fetches from mock service ordered by orderDate desc. Filter tabs: All / Draft / Open / Received. Search by order number or vendor name. Each card shows: number, vendorName, orderDate, status badge (color-coded), totalAmountIncludingTax. Pull-to-refresh.
Order Detail: Full order view with header card (vendor info, status, dates, totals), line items list (item, qty, unit cost, amount), action buttons based on status:
Draft: Edit, Delete, Submit (updates status to Open in mock state)
Open: Receive & Invoice (calls receiveAndInvoice(orderId) on mock service), Edit
Received: View only
Edit order: update order header, add/edit/delete lines in mock state
P1
[Feature] Document Scan screen (placeholder) — UI-only placeholder screen for future ADI integration. Shows: camera/file picker button, a mock "scanning..." animation, a placeholder result card with extracted fields (vendor, invoice number, line items, amounts). A "Create Order from Scan" button that navigates to Create Order screen with pre-filled mock data. Banner saying "AI Document Extraction — Coming Soon" with a brief description. P1

[Feature] Posted Invoices screen — Fetches from mock service ordered by postingDate desc. List view with: invoice number, vendorName, postingDate, totalAmountIncludingTax, status badge. Search and date range filter. Tap to view detail (show all invoice fields in a read-only card). P1

[Feature] Settings/Profile screen — User info display (logged in user). Clear cached data button (resets in-memory mock state to original seed data). App version. Logout button. About section describing the app. Demo mode indicator banner showing "Running in Demo Mode — No live data connection." P2

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
Proper loading spinners, error states with retry, and empty states with illustrations
SafeAreaView and StatusBar on all screens
Currency formatting for all amounts
Pull-to-refresh on all list screens

After the complete process is done, look for the errors and bugs in the code and do the analysis and provide the solution by fixing each of the errors in the code.
