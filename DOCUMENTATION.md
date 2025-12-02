# Inventory Management System - Technical Documentation

## Version: 1.0.0
## Last Updated: December 2024

---

## Table of Contents

1. [System Overview](#1-system-overview)
2. [Architecture](#2-architecture)
3. [Database Schema](#3-database-schema)
4. [API Routes Reference](#4-api-routes-reference)
5. [Authentication & Authorization](#5-authentication--authorization)
6. [Models Reference](#6-models-reference)
7. [Templates Structure](#7-templates-structure)
8. [Configuration](#8-configuration)
9. [Security Considerations](#9-security-considerations)
10. [Performance Optimization](#10-performance-optimization)

---

## 1. System Overview

### 1.1 Purpose
The Inventory Management System is a web-based application designed to help businesses track products, manage stock levels, record transactions, and monitor inventory health.

### 1.2 Key Features
- User authentication with role-based access control
- Product CRUD operations with SKU tracking
- Real-time stock management (purchases/sales)
- Low stock alerts and notifications
- Category and supplier management
- Transaction history with audit trail
- Dashboard with key metrics

### 1.3 Technology Stack

| Component | Technology |
|-----------|------------|
| Backend Framework | Flask 3.x |
| Database | PostgreSQL |
| ORM | SQLAlchemy |
| Authentication | Flask-Login |
| Template Engine | Jinja2 |
| CSS Framework | Custom CSS (Material Design inspired) |
| Web Server | Gunicorn |
| Font | Inter (Google Fonts) |

---

## 2. Architecture

### 2.1 Application Structure

```
inventory-management/
├── app.py                 # Application factory and configuration
├── main.py                # Entry point
├── models.py              # Database models
├── routes.py              # Route handlers
├── templates/             # Jinja2 templates
│   ├── base.html          # Base template with navigation
│   ├── login.html         # Login page
│   ├── dashboard.html     # Dashboard view
│   ├── products.html      # Products listing
│   ├── product_form.html  # Add/Edit product form
│   ├── stock_form.html    # Stock update form
│   ├── low_stock.html     # Low stock items view
│   ├── categories.html    # Categories management
│   ├── suppliers.html     # Suppliers listing
│   ├── supplier_form.html # Add/Edit supplier form
│   └── transactions.html  # Transaction history
├── static/
│   └── css/
│       └── style.css      # Custom styles
├── DEPLOYMENT_GUIDE.md    # Deployment instructions
├── USER_GUIDE.md          # User documentation
└── DOCUMENTATION.md       # This file
```

### 2.2 Request Flow

```
Client Request
      ↓
   Gunicorn (WSGI Server)
      ↓
   Flask Application
      ↓
   Route Handler (routes.py)
      ↓
   Business Logic + Database (SQLAlchemy)
      ↓
   Template Rendering (Jinja2)
      ↓
   HTML Response
      ↓
Client Browser
```

### 2.3 Design Patterns

- **MVC Pattern**: Models (models.py), Views (templates/), Controllers (routes.py)
- **Factory Pattern**: Application initialization in app.py
- **Decorator Pattern**: Authentication (@login_required) and authorization (@admin_required)

---

## 3. Database Schema

### 3.1 Entity Relationship Diagram

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│    User     │     │   Product   │     │  Category   │
├─────────────┤     ├─────────────┤     ├─────────────┤
│ id (PK)     │     │ id (PK)     │     │ id (PK)     │
│ username    │     │ name        │     │ name        │
│ email       │     │ sku         │     │ description │
│ password_   │     │ description │     │ created_at  │
│   hash      │     │ purchase_   │     └──────┬──────┘
│ role        │     │   price     │            │
│ created_at  │     │ selling_    │            │
└──────┬──────┘     │   price     │            │
       │            │ quantity    │◄───────────┘
       │            │ reorder_    │      category_id (FK)
       │            │   level     │
       │            │ category_id │     ┌─────────────┐
       │            │   (FK)      │     │  Supplier   │
       │            │ supplier_id │     ├─────────────┤
       │            │   (FK)      │◄────┤ id (PK)     │
       │            │ created_at  │     │ name        │
       │            │ updated_at  │     │ contact_    │
       │            └──────┬──────┘     │   name      │
       │                   │            │ email       │
       │                   │            │ phone       │
       │            ┌──────┴──────┐     │ address     │
       │            │ Transaction │     │ created_at  │
       │            ├─────────────┤     └─────────────┘
       └───────────►│ id (PK)     │
         user_id    │ product_id  │
           (FK)     │   (FK)      │
                    │ type        │
                    │ quantity    │
                    │ notes       │
                    │ user_id(FK) │
                    │ created_at  │
                    └─────────────┘
```

### 3.2 Table Definitions

#### Users Table
```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(64) UNIQUE NOT NULL,
    email VARCHAR(120) UNIQUE NOT NULL,
    password_hash VARCHAR(256),
    role VARCHAR(20) DEFAULT 'staff',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | INTEGER | PRIMARY KEY | Unique identifier |
| username | VARCHAR(64) | UNIQUE, NOT NULL | Login username |
| email | VARCHAR(120) | UNIQUE, NOT NULL | User email |
| password_hash | VARCHAR(256) | - | Hashed password |
| role | VARCHAR(20) | DEFAULT 'staff' | User role (admin/staff) |
| created_at | TIMESTAMP | DEFAULT NOW | Account creation time |

#### Products Table
```sql
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    name VARCHAR(200) NOT NULL,
    sku VARCHAR(50) UNIQUE NOT NULL,
    description TEXT,
    purchase_price FLOAT DEFAULT 0.0,
    selling_price FLOAT DEFAULT 0.0,
    quantity INTEGER DEFAULT 0,
    reorder_level INTEGER DEFAULT 10,
    category_id INTEGER REFERENCES categories(id),
    supplier_id INTEGER REFERENCES suppliers(id),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | INTEGER | PRIMARY KEY | Unique identifier |
| name | VARCHAR(200) | NOT NULL | Product name |
| sku | VARCHAR(50) | UNIQUE, NOT NULL | Stock keeping unit |
| description | TEXT | - | Product description |
| purchase_price | FLOAT | DEFAULT 0.0 | Cost price |
| selling_price | FLOAT | DEFAULT 0.0 | Retail price |
| quantity | INTEGER | DEFAULT 0 | Current stock |
| reorder_level | INTEGER | DEFAULT 10 | Low stock threshold |
| category_id | INTEGER | FK → categories | Product category |
| supplier_id | INTEGER | FK → suppliers | Product supplier |
| created_at | TIMESTAMP | DEFAULT NOW | Creation time |
| updated_at | TIMESTAMP | DEFAULT NOW | Last update time |

#### Categories Table
```sql
CREATE TABLE categories (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) UNIQUE NOT NULL,
    description TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

#### Suppliers Table
```sql
CREATE TABLE suppliers (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    contact_name VARCHAR(100),
    email VARCHAR(120),
    phone VARCHAR(20),
    address TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

#### Transactions Table
```sql
CREATE TABLE transactions (
    id SERIAL PRIMARY KEY,
    product_id INTEGER NOT NULL REFERENCES products(id),
    type VARCHAR(20) NOT NULL,
    quantity INTEGER NOT NULL,
    notes TEXT,
    user_id INTEGER REFERENCES users(id),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | INTEGER | PRIMARY KEY | Unique identifier |
| product_id | INTEGER | FK, NOT NULL | Related product |
| type | VARCHAR(20) | NOT NULL | 'purchase' or 'sale' |
| quantity | INTEGER | NOT NULL | Units changed |
| notes | TEXT | - | Transaction notes |
| user_id | INTEGER | FK → users | User who made change |
| created_at | TIMESTAMP | DEFAULT NOW | Transaction time |

---

## 4. API Routes Reference

### 4.1 Authentication Routes

| Route | Method | Auth Required | Description |
|-------|--------|---------------|-------------|
| `/` | GET | No | Redirect to dashboard or login |
| `/login` | GET, POST | No | User login page |
| `/logout` | GET | Yes | User logout |

#### POST /login
**Request Body (form-data):**
```
username: string (required)
password: string (required)
```

**Response:**
- Success: Redirect to `/dashboard`
- Failure: Redirect to `/login` with error flash

---

### 4.2 Dashboard Routes

| Route | Method | Auth Required | Description |
|-------|--------|---------------|-------------|
| `/dashboard` | GET | Yes | Main dashboard view |

**Response Data:**
- `total_products`: Total product count
- `total_stock`: Sum of all quantities
- `low_stock_count`: Products at/below reorder level
- `out_of_stock_count`: Products with quantity = 0
- `low_stock_products`: List of low stock items (limit 5)
- `recent_transactions`: Latest transactions (limit 10)

---

### 4.3 Product Routes

| Route | Method | Auth Required | Admin Only | Description |
|-------|--------|---------------|------------|-------------|
| `/products` | GET | Yes | No | List all products |
| `/products/add` | GET, POST | Yes | No | Add new product |
| `/products/<id>/edit` | GET, POST | Yes | No | Edit product |
| `/products/<id>/delete` | POST | Yes | Yes | Delete product |
| `/products/<id>/stock` | GET, POST | Yes | No | Update stock |

#### GET /products
**Query Parameters:**
| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| search | string | '' | Search by name or SKU |
| category | integer | null | Filter by category ID |
| sort | string | 'name' | Sort column (name, quantity, selling_price, created_at) |
| order | string | 'asc' | Sort order (asc, desc) |

#### POST /products/add
**Request Body (form-data):**
```
name: string (required)
sku: string (required, unique)
description: string
category_id: integer
supplier_id: integer
purchase_price: float (default: 0)
selling_price: float (default: 0)
quantity: integer (default: 0)
reorder_level: integer (default: 10)
```

#### POST /products/<id>/stock
**Request Body (form-data):**
```
action: string ('purchase' or 'sale')
quantity: integer (required, > 0)
notes: string
```

**Validation Rules:**
- Quantity must be greater than 0
- For sales: quantity cannot exceed current stock

---

### 4.4 Category Routes

| Route | Method | Auth Required | Admin Only | Description |
|-------|--------|---------------|------------|-------------|
| `/categories` | GET | Yes | No | List categories |
| `/categories/add` | POST | Yes | No | Add category |
| `/categories/<id>/edit` | POST | Yes | No | Edit category |
| `/categories/<id>/delete` | POST | Yes | Yes | Delete category |

#### POST /categories/add
**Request Body (form-data):**
```
name: string (required, unique)
description: string
```

**Validation:**
- Cannot delete category with associated products

---

### 4.5 Supplier Routes

| Route | Method | Auth Required | Admin Only | Description |
|-------|--------|---------------|------------|-------------|
| `/suppliers` | GET | Yes | No | List suppliers |
| `/suppliers/add` | GET, POST | Yes | No | Add supplier |
| `/suppliers/<id>/edit` | GET, POST | Yes | No | Edit supplier |
| `/suppliers/<id>/delete` | POST | Yes | Yes | Delete supplier |

#### POST /suppliers/add
**Request Body (form-data):**
```
name: string (required)
contact_name: string
email: string
phone: string
address: string
```

---

### 4.6 Transaction Routes

| Route | Method | Auth Required | Description |
|-------|--------|---------------|-------------|
| `/transactions` | GET | Yes | List transactions |

#### GET /transactions
**Query Parameters:**
| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| type | string | 'all' | Filter: 'all', 'purchase', 'sale' |

---

### 4.7 Low Stock Routes

| Route | Method | Auth Required | Description |
|-------|--------|---------------|-------------|
| `/low-stock` | GET | Yes | List low stock items |

---

## 5. Authentication & Authorization

### 5.1 Authentication Flow

```
1. User submits credentials → /login (POST)
2. Server validates credentials
3. If valid:
   - Create session via Flask-Login
   - Redirect to dashboard
4. If invalid:
   - Flash error message
   - Redirect back to login
```

### 5.2 Session Management
- Sessions handled by Flask-Login
- Session data stored server-side
- Cookie contains session ID only
- Session expires on browser close (default)

### 5.3 Password Security
- Passwords hashed using Werkzeug's `generate_password_hash()`
- Default algorithm: scrypt (secure, modern)
- Verification via `check_password_hash()`

### 5.4 Authorization Levels

| Permission | Admin | Staff |
|------------|-------|-------|
| View dashboard | Yes | Yes |
| View products | Yes | Yes |
| Add products | Yes | Yes |
| Edit products | Yes | Yes |
| Delete products | Yes | No |
| Update stock | Yes | Yes |
| Manage categories | Yes | Yes |
| Delete categories | Yes | No |
| Manage suppliers | Yes | Yes |
| Delete suppliers | Yes | No |
| View transactions | Yes | Yes |

### 5.5 Decorators

```python
@login_required  # Requires authenticated user
@admin_required  # Requires admin role
```

---

## 6. Models Reference

### 6.1 User Model

```python
class User(UserMixin, db.Model):
    __tablename__ = 'users'
    
    id: int                    # Primary key
    username: str              # Unique username
    email: str                 # Unique email
    password_hash: str         # Hashed password
    role: str                  # 'admin' or 'staff'
    created_at: datetime       # Account creation time
    
    # Methods
    set_password(password)     # Hash and store password
    check_password(password)   # Verify password
    is_admin()                 # Check if admin role
```

### 6.2 Product Model

```python
class Product(db.Model):
    __tablename__ = 'products'
    
    id: int                    # Primary key
    name: str                  # Product name
    sku: str                   # Unique SKU
    description: str           # Description
    purchase_price: float      # Cost price
    selling_price: float       # Retail price
    quantity: int              # Current stock
    reorder_level: int         # Low stock threshold
    category_id: int           # FK to categories
    supplier_id: int           # FK to suppliers
    created_at: datetime       # Creation time
    updated_at: datetime       # Last update
    
    # Relationships
    category: Category         # Parent category
    supplier: Supplier         # Parent supplier
    transactions: List[Transaction]
    
    # Methods
    is_low_stock()            # quantity <= reorder_level
    is_out_of_stock()         # quantity == 0
```

### 6.3 Category Model

```python
class Category(db.Model):
    __tablename__ = 'categories'
    
    id: int                    # Primary key
    name: str                  # Unique name
    description: str           # Description
    created_at: datetime       # Creation time
    
    # Relationships
    products: List[Product]    # Child products
```

### 6.4 Supplier Model

```python
class Supplier(db.Model):
    __tablename__ = 'suppliers'
    
    id: int                    # Primary key
    name: str                  # Supplier name
    contact_name: str          # Contact person
    email: str                 # Email address
    phone: str                 # Phone number
    address: str               # Physical address
    created_at: datetime       # Creation time
    
    # Relationships
    products: List[Product]    # Supplied products
```

### 6.5 Transaction Model

```python
class Transaction(db.Model):
    __tablename__ = 'transactions'
    
    id: int                    # Primary key
    product_id: int            # FK to products
    type: str                  # 'purchase' or 'sale'
    quantity: int              # Units changed
    notes: str                 # Optional notes
    user_id: int               # FK to users
    created_at: datetime       # Transaction time
    
    # Relationships
    product: Product           # Related product
    user: User                 # User who made change
```

---

## 7. Templates Structure

### 7.1 Template Inheritance

```
base.html (Parent)
    ├── login.html
    ├── dashboard.html
    ├── products.html
    ├── product_form.html
    ├── stock_form.html
    ├── low_stock.html
    ├── categories.html
    ├── suppliers.html
    ├── supplier_form.html
    └── transactions.html
```

### 7.2 Base Template Blocks

```html
{% block title %}{% endblock %}     <!-- Page title -->
{% block content %}{% endblock %}   <!-- Main content -->
```

### 7.3 Template Variables

| Template | Key Variables |
|----------|---------------|
| dashboard.html | total_products, total_stock, low_stock_count, out_of_stock_count, low_stock_products, recent_transactions |
| products.html | products, categories, search, category_id, sort_by, order |
| product_form.html | product, categories, suppliers, action |
| stock_form.html | product |
| low_stock.html | products |
| categories.html | categories |
| suppliers.html | suppliers |
| supplier_form.html | supplier, action |
| transactions.html | transactions, filter_type |

---

## 8. Configuration

### 8.1 Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| DATABASE_URL | Yes | PostgreSQL connection string |
| SESSION_SECRET | Yes | Flask session secret key |

### 8.2 Database Configuration

```python
app.config["SQLALCHEMY_DATABASE_URI"] = os.environ.get("DATABASE_URL")
app.config["SQLALCHEMY_ENGINE_OPTIONS"] = {
    "pool_recycle": 300,    # Recycle connections every 5 min
    "pool_pre_ping": True,  # Test connections before use
}
```

### 8.3 Flask-Login Configuration

```python
login_manager.login_view = 'login'
login_manager.login_message = 'Please log in to access this page.'
login_manager.login_message_category = 'info'
```

---

## 9. Security Considerations

### 9.1 Implemented Security Features

| Feature | Implementation |
|---------|----------------|
| Password Hashing | Werkzeug scrypt |
| Session Management | Flask-Login with server-side sessions |
| CSRF Protection | Flask WTF (via forms) |
| SQL Injection Prevention | SQLAlchemy ORM (parameterized queries) |
| XSS Prevention | Jinja2 auto-escaping |
| Access Control | Role-based (admin/staff) |

### 9.2 Security Recommendations

1. **Change Default Credentials**
   - Immediately change admin/admin123 after deployment

2. **HTTPS**
   - Always use HTTPS in production
   - Configure SSL certificates

3. **Environment Variables**
   - Never commit secrets to version control
   - Use secure secret generation:
   ```python
   python -c "import secrets; print(secrets.token_hex(32))"
   ```

4. **Database Security**
   - Use strong database passwords
   - Limit database user permissions
   - Enable SSL for database connections

5. **Rate Limiting**
   - Consider adding Flask-Limiter for API protection

---

## 10. Performance Optimization

### 10.1 Database Optimization

- **Indexing**: SKU and username columns are indexed (unique constraint)
- **Connection Pooling**: Configured via SQLAlchemy
- **Lazy Loading**: Relationships use lazy='dynamic' where appropriate

### 10.2 Query Optimization

```python
# Efficient: Single query with joins
products = Product.query.options(
    joinedload(Product.category),
    joinedload(Product.supplier)
).all()

# Avoid: N+1 queries
for product in products:
    print(product.category.name)  # Additional query per product
```

### 10.3 Caching Recommendations

For high-traffic deployments, consider:
- Redis for session storage
- Flask-Caching for view caching
- CDN for static assets

### 10.4 Scaling Considerations

| Component | Scaling Strategy |
|-----------|------------------|
| Web Server | Multiple Gunicorn workers |
| Database | Read replicas, connection pooling |
| Static Files | CDN or Nginx serving |
| Sessions | Redis cluster |

---

## Appendix A: Error Codes

| HTTP Code | Meaning | Common Causes |
|-----------|---------|---------------|
| 200 | OK | Successful request |
| 302 | Redirect | After form submission |
| 400 | Bad Request | Invalid form data |
| 401 | Unauthorized | Not logged in |
| 403 | Forbidden | Insufficient permissions |
| 404 | Not Found | Invalid product/category/supplier ID |
| 500 | Server Error | Database or application error |

---

## Appendix B: Flash Message Categories

| Category | CSS Class | Usage |
|----------|-----------|-------|
| success | alert-success | Successful operations |
| error | alert-error | Validation or permission errors |
| info | alert-info | Informational messages |
| warning | alert-warning | Warning messages |

---

## Appendix C: Development Commands

```bash
# Run development server
python -c "from app import app; app.run(debug=True, port=5000)"

# Run production server
gunicorn --bind 0.0.0.0:5000 main:app

# Run with multiple workers
gunicorn --bind 0.0.0.0:5000 --workers 4 main:app

# Database shell
flask shell
>>> from models import *
>>> User.query.all()
```

---

*End of Documentation*
