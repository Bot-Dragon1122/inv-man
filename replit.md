# Inventory Management System

## Overview
A full-featured Inventory Management System built with Flask and PostgreSQL. The system allows users to manage products, track stock levels, record transactions, monitor low-stock items, and maintain suppliers.

## Tech Stack
- **Backend**: Flask (Python)
- **Database**: PostgreSQL with SQLAlchemy ORM
- **Authentication**: Flask-Login
- **Frontend**: HTML/CSS with Bootstrap-inspired custom styling
- **Deployment**: Gunicorn WSGI server

## Features
- **User Authentication**: Secure login/logout with role-based access (admin/staff)
- **Product Management**: Full CRUD operations with SKU, pricing, categories, and suppliers
- **Stock Management**: Purchase and sale transactions with automatic quantity updates
- **Low Stock Alerts**: Dashboard highlighting products at or below reorder level
- **Categories**: Organize products into categories
- **Suppliers**: Manage supplier information and link to products
- **Transaction History**: Complete log of all stock changes with filtering

## Default Login
- Username: `admin`
- Password: `admin123`

## Project Structure
```
├── app.py              # Flask app configuration and database setup
├── models.py           # SQLAlchemy database models
├── routes.py           # All application routes and views
├── main.py             # Application entry point
├── templates/          # Jinja2 HTML templates
│   ├── base.html       # Base template with navigation
│   ├── login.html      # Login page
│   ├── dashboard.html  # Main dashboard
│   ├── products.html   # Products listing
│   ├── product_form.html
│   ├── stock_form.html
│   ├── low_stock.html
│   ├── categories.html
│   ├── suppliers.html
│   ├── supplier_form.html
│   └── transactions.html
├── static/
│   └── css/
│       └── style.css   # Custom styles
└── design_guidelines.md # UI/UX design specifications
```

## Database Models
- **User**: Authentication with roles (admin/staff)
- **Product**: Inventory items with pricing, quantity, reorder levels
- **Category**: Product categorization
- **Supplier**: Supplier contact information
- **Transaction**: Stock movement history (purchases/sales)

## Running the Application
The application runs on port 5000 using Gunicorn.

## Environment Variables
- `DATABASE_URL`: PostgreSQL connection string
- `SESSION_SECRET`: Flask session secret key

## Documentation
- `USER_GUIDE.md`: Features guide, step-by-step usage instructions, and error handling
- `DOCUMENTATION.md`: Detailed technical documentation including API reference, database schema, and architecture
- `design_guidelines.md`: UI/UX design specifications

## Additional Features
- **CSV Export**: Export products and transactions to CSV files
- **CSV Import**: Bulk import products from CSV (Admin only)
- **Advanced Filtering**: Filter transactions by date range and product
- **Dashboard Charts**: Visual stock trends showing purchases/sales over 30 days
