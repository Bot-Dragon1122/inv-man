# Inventory Management System - User Guide

## Table of Contents
1. [Getting Started](#getting-started)
2. [Features Overview](#features-overview)
3. [Step-by-Step Usage](#step-by-step-usage)
4. [Error Handling & Troubleshooting](#error-handling--troubleshooting)

---

## Getting Started

### First Login
1. Open the application in your browser
2. Use the default credentials:
   - **Username**: `admin`
   - **Password**: `admin123`
3. After logging in, you'll be redirected to the Dashboard

### User Roles
| Role | Permissions |
|------|-------------|
| **Admin** | Full access - can add, edit, delete all items |
| **Staff** | Can add/edit products, update stock, view reports. Cannot delete items |

---

## Features Overview

### 1. Dashboard
- **Total Products**: Count of all products in inventory
- **Total Stock Units**: Sum of all product quantities
- **Low Stock Items**: Products at or below reorder level
- **Out of Stock**: Products with zero quantity
- **Low Stock Alert Table**: Quick view of items needing attention
- **Recent Transactions**: Latest stock movements

### 2. Product Management
- Add new products with complete details
- Edit existing product information
- Delete products (Admin only)
- Search by product name or SKU
- Filter by category
- Sort by name, quantity, or price

### 3. Stock Management
- Record purchases (increase stock)
- Record sales (decrease stock)
- Automatic quantity updates
- Transaction notes for reference
- Prevention of negative stock

### 4. Categories
- Organize products by category
- Add, edit, delete categories
- View product count per category

### 5. Suppliers
- Store supplier contact information
- Link products to suppliers
- Track supplier relationships

### 6. Transaction History
- Complete log of all stock changes
- Filter by type (purchases/sales)
- View date, quantity, and notes
- Track which user made changes

### 7. Low Stock Alerts
- Dedicated page for low stock items
- Quick restock action buttons
- Visual indicators (warning/danger badges)

---

## Step-by-Step Usage

### Adding a New Product

**Step 1**: Navigate to Products
- Click "Products" in the navigation menu

**Step 2**: Click "Add Product"
- Click the blue "Add Product" button in the top right

**Step 3**: Fill in Product Details
- **Product Name** (required): Enter the product name
- **SKU** (required): Enter a unique product code
- **Description**: Optional product description
- **Category**: Select from existing categories
- **Supplier**: Select from existing suppliers
- **Purchase Price**: Cost price per unit
- **Selling Price**: Retail price per unit
- **Initial Quantity**: Starting stock amount
- **Reorder Level**: Low stock threshold

**Step 4**: Save
- Click "Save Product" to add the product

---

### Updating Stock (Purchase/Sale)

**Step 1**: Find the Product
- Go to Products page
- Use search or filters to find the product

**Step 2**: Click Stock Button
- Click the green stock icon on the product row

**Step 3**: Select Transaction Type
- **Purchase**: Adds stock (receiving inventory)
- **Sale**: Removes stock (selling items)

**Step 4**: Enter Details
- **Quantity**: Number of units
- **Notes**: Optional reason or reference number

**Step 5**: Update
- Click "Update Stock" to complete

---

### Managing Categories

**Step 1**: Go to Categories
- Click "Categories" in the navigation

**Step 2**: Add a Category
- Enter category name in the form
- Add optional description
- Click "Add Category"

**Step 3**: Edit a Category
- Click the edit (pencil) icon on any category
- Form will populate with existing data
- Make changes and click "Save Changes"

**Step 4**: Delete a Category (Admin only)
- Click the delete (trash) icon
- Confirm deletion
- Note: Cannot delete categories with associated products

---

### Managing Suppliers

**Step 1**: Go to Suppliers
- Click "Suppliers" in the navigation

**Step 2**: Add a Supplier
- Click "Add Supplier" button
- Fill in:
  - Supplier Name (required)
  - Contact Person
  - Email
  - Phone
  - Address
- Click "Save Supplier"

**Step 3**: Edit a Supplier
- Click the edit icon on the supplier row
- Update information
- Click "Save Supplier"

---

### Viewing Transactions

**Step 1**: Go to Transactions
- Click "Transactions" in the navigation

**Step 2**: Filter by Type
- Click "All Transactions" to see everything
- Click "Purchases" to see only stock additions
- Click "Sales" to see only stock reductions

**Step 3**: Review Details
- View product name, type, quantity, notes, and user

---

### Checking Low Stock Items

**Step 1**: Go to Low Stock
- Click "Low Stock" in the navigation

**Step 2**: Review Items
- See all products at or below reorder level
- Red badge = Out of stock (0 units)
- Yellow badge = Low stock (above 0 but at/below reorder level)

**Step 3**: Restock
- Click "Restock" button to quickly add stock

---

## Error Handling & Troubleshooting

### Common Errors and Solutions

#### Login Errors

| Error Message | Cause | Solution |
|--------------|-------|----------|
| "Invalid username or password" | Wrong credentials | Check username and password. Default: admin/admin123 |
| "Please log in to access this page" | Session expired | Log in again |

#### Product Errors

| Error Message | Cause | Solution |
|--------------|-------|----------|
| "A product with this SKU already exists" | Duplicate SKU | Use a unique SKU code |
| "Only admins can delete products" | Insufficient permissions | Contact admin to delete |

#### Stock Errors

| Error Message | Cause | Solution |
|--------------|-------|----------|
| "Quantity must be greater than 0" | Zero or negative quantity | Enter a positive number |
| "Cannot sell more than available stock" | Sale exceeds stock | Reduce quantity or add stock first |

#### Category Errors

| Error Message | Cause | Solution |
|--------------|-------|----------|
| "A category with this name already exists" | Duplicate name | Use a unique category name |
| "Cannot delete category with associated products" | Products still linked | Reassign products first, then delete |

#### Supplier Errors

| Error Message | Cause | Solution |
|--------------|-------|----------|
| "Only admins can delete suppliers" | Insufficient permissions | Contact admin to delete |
| "Cannot delete supplier with associated products" | Products still linked | Reassign products first, then delete |

---

### System Errors

#### Database Connection Error
**Symptoms**: Application won't load, "Internal Server Error"

**Solutions**:
1. Check if the database is running
2. Verify DATABASE_URL environment variable is set correctly
3. Restart the application

#### Page Not Loading
**Symptoms**: Blank page or loading indefinitely

**Solutions**:
1. Refresh the page (Ctrl+F5 / Cmd+Shift+R)
2. Clear browser cache
3. Check internet connection
4. Try a different browser

#### Session Expired
**Symptoms**: Redirected to login unexpectedly

**Solutions**:
1. Log in again
2. Check if cookies are enabled
3. Avoid long idle periods

---

### Best Practices

#### Data Entry
- Use consistent SKU formats (e.g., PROD-001, PROD-002)
- Set appropriate reorder levels based on sales velocity
- Keep product descriptions brief but informative
- Always add notes when updating stock for audit purposes

#### Inventory Management
- Review low stock items daily
- Update stock immediately after receiving/selling
- Regularly audit physical stock vs system stock
- Use categories to organize products logically

#### Security
- Change default admin password immediately
- Create individual user accounts (don't share logins)
- Log out when finished
- Regularly review transaction history for anomalies

---

### Getting Help

If you encounter an issue not covered here:

1. **Check the error message** - Most errors include specific guidance
2. **Refresh the page** - Resolves many temporary issues
3. **Log out and back in** - Fixes session-related problems
4. **Clear browser cache** - Resolves display issues
5. **Contact your administrator** - For persistent problems

---

### Quick Reference Card

| Action | Steps |
|--------|-------|
| Add Product | Products → Add Product → Fill form → Save |
| Update Stock | Products → Stock icon → Select type → Enter qty → Update |
| Add Category | Categories → Enter name → Add Category |
| Add Supplier | Suppliers → Add Supplier → Fill form → Save |
| View Low Stock | Low Stock (navigation) |
| View Transactions | Transactions → Select filter tab |
| Search Products | Products → Type in search box → Filter |
| Logout | Click Logout button (top right) |
