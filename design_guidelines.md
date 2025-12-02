# Design Guidelines: Inventory Management System

## Design Approach

**Selected Approach**: Design System - Material Design principles  
**Justification**: Data-heavy enterprise application requiring clarity, consistency, and efficient information display. Material Design provides excellent patterns for tables, forms, and data visualization.

**Key Principles**:
- Information clarity over visual flair
- Predictable, consistent interactions
- Efficient data scanning and input
- Clear hierarchy for task completion

---

## Typography System

**Font Family**: Inter or Roboto via Google Fonts CDN

**Hierarchy**:
- Page Titles: 32px, semibold (600)
- Section Headers: 24px, medium (500)
- Card Titles: 18px, medium (500)
- Body Text: 16px, regular (400)
- Table Headers: 14px, medium (500)
- Table Data: 14px, regular (400)
- Labels: 12px, medium (500), uppercase tracking
- Buttons: 14px, medium (500)

---

## Layout System

**Spacing Units**: Use Tailwind spacing of 2, 4, 6, 8, 12, 16 consistently (e.g., p-4, m-8, gap-6)

**Container Structure**:
- Main app container: max-w-7xl with px-6 py-8
- Card padding: p-6
- Form spacing: space-y-6
- Table cell padding: px-4 py-3
- Button padding: px-6 py-2.5

**Grid System**:
- Dashboard cards: grid-cols-1 md:grid-cols-3 gap-6
- Product listings: Full-width responsive tables
- Forms: Single column, max-w-2xl

---

## Component Library

### Navigation
- Top navbar with app logo/title on left
- User profile/logout on right
- Secondary navigation tabs below (Dashboard, Products, Transactions, Suppliers, Categories)
- Active tab indicator with bottom border
- Mobile: Hamburger menu collapsing to drawer

### Dashboard Cards
- Elevated cards with subtle shadow
- Icon on left, metric on right layout
- Large number display (36px, bold) with label below (14px)
- Low stock card uses warning visual treatment

### Data Tables
- Full-width with alternating row background for scanning
- Fixed header row with medium weight text
- Sortable columns with sort indicator icons (Heroicons)
- Row actions (Edit/Delete) as icon buttons on right
- Hover state on rows for interactivity
- Empty state messaging when no data
- Pagination controls at bottom-center

### Forms
- Vertical layout with clear label-input pairing
- Label above input with 8px gap
- Input fields: h-10, rounded corners, border treatment
- Required field indicators (asterisk)
- Helper text below inputs at 12px
- Error states with red accent and icon
- Submit button at bottom-left, Cancel as secondary button beside it

### Search & Filters Bar
- Horizontal layout above tables
- Search input on left (max-w-md)
- Filter dropdowns and sort controls on right
- Clear all filters button
- Sticky positioning when scrolling tables

### Stock Level Indicators
- Badge components for quantity display
- Visual treatment based on stock level:
  - At/above reorder: neutral
  - Below reorder: warning treatment
  - Zero stock: critical treatment
- Inline with product name in tables

### Transaction Logs
- Tabbed interface (All / Purchases / Sales)
- Timeline-style list view
- Each entry shows: product name, quantity change, type, date/time
- Alternating backgrounds for visual separation

### Modals
- Product add/edit forms in centered modal overlays
- Delete confirmation dialogs
- Backdrop blur effect
- Modal max-w-2xl
- Close button top-right (X icon)

### Buttons
**Primary**: Rounded, medium height (h-10), icon + text combination where applicable  
**Secondary**: Outlined variant  
**Danger**: For delete actions  
**Icon-only**: Circular, 40px diameter for table actions

### Alerts
- Top-of-page notification banners for success/error messages
- Auto-dismiss after 5 seconds
- Icons from Heroicons (check-circle, x-circle, exclamation)

---

## Page Layouts

### Login Page
- Centered card on page (max-w-md)
- Logo/title at top
- Username and password fields
- Remember me checkbox
- Submit button full-width

### Dashboard
- 3-column metric cards at top
- Low stock products table below
- Recent transactions widget

### Products Page
- Page title with "Add Product" button on right
- Search/filter bar
- Products table with all columns
- Action column with edit/delete icons

### Add/Edit Product
- Modal form with all product fields
- Category dropdown
- Supplier dropdown
- Number inputs for prices and quantities
- SKU field with validation

### Transaction History
- Tab navigation
- Filterable transaction list
- Export to CSV button top-right

### Categories/Suppliers Management
- Two-column layout: list on left (30%), form on right (70%)
- Add new inline or in right panel
- Edit inline in list

---

## Icons

**Library**: Heroicons via CDN

**Usage**:
- Navigation: home, shopping-bag, document-text, users, tag
- Actions: pencil, trash, plus, download, search
- Status: check-circle, exclamation-triangle, x-circle
- Tables: arrows-up-down for sorting

---

## Animations

Minimal animations for professional feel:
- Table row hover: subtle background transition
- Button hover: slight elevation change
- Modal enter/exit: fade + scale
- Alert slide-in from top

---

## Accessibility

- All form inputs with proper labels and ARIA attributes
- Focus states with visible outline
- Keyboard navigation for all interactive elements
- Table headers with scope attributes
- Color contrast meeting WCAG AA standards
- Screen reader text for icon-only buttons