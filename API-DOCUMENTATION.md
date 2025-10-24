# Microservices API Documentation Summary

## Overview
This repository contains a microservices-based web application with the following services:

### Total API Count: 15 REST APIs
- **PHP Service (api-php)**: 3 APIs
- **Node.js Service (api-node)**: 9 APIs  
- **Python Service (api-python)**: 2 APIs
- **Frontend (Next.js)**: 8 pages
- **Infrastructure**: 1 nginx proxy + 1 PostgreSQL database

---

## Microservices Breakdown

### 1. API-PHP Service (Port 8000)
**Technology**: Lumen (PHP Framework)
**Purpose**: Authentication and User Management
**Database**: PostgreSQL

#### APIs Exposed (3):
1. **POST /api/login** - User authentication, returns JWT token
2. **POST /api/signup** - User registration
3. **GET /api/validate** - JWT token validation (requires authentication)

#### APIs Consumed:
- None (This is an authentication provider service)

---

### 2. API-NODE Service (Port 8001)
**Technology**: Express.js (Node.js Framework)
**Purpose**: Product and Category Management
**Database**: PostgreSQL with Sequelize ORM

#### APIs Exposed (9):
1. **GET /api** - Health check endpoint
2. **POST /api/auth/signin** - User signin (alternative auth)
3. **POST /api/auth/signup** - User signup (alternative auth)
4. **GET /api/users** - List all users
5. **GET /api/products** - List products (paginated, requires auth)
6. **POST /api/products** - Add new product (requires auth)
7. **POST /api/products/:id** - Update product (requires auth)
8. **GET /api/products/:id/delete** - Delete product (requires auth)
9. **GET /api/categories** - List all categories (requires auth)

#### APIs Consumed:
- None (Independent service)

---

### 3. API-PYTHON Service (Port 8002)
**Technology**: Flask (Python Framework)
**Purpose**: Order Management
**Database**: PostgreSQL with SQLAlchemy ORM

#### APIs Exposed (2):
1. **GET /api/orders** - Get all orders for buyer users (requires auth)
2. **POST /api/orders** - Create new order (requires auth)

#### APIs Consumed:
1. **GET http://api-php:8000/api/validate** - Validates JWT tokens with PHP service before processing orders

---

### 4. Frontend Service (Port 3000)
**Technology**: Next.js (React Framework)
**Purpose**: User Interface

#### Pages (8):
1. **/** (index) - Login page
2. **/register** - User registration
3. **/market** - Browse products marketplace
4. **/stock** - Stock management (seller view)
5. **/stock/add** - Add new product
6. **/cart** - Shopping cart
7. **/orders** - View orders
8. **/categories** - Browse categories

#### APIs Consumed:
| Frontend Page | APIs Called | Purpose |
|---------------|-------------|---------|
| / (Login) | POST /php/api/login, GET /php/api/validate | User authentication |
| /register | POST /php/api/signup | User registration |
| /market | GET /node/api/products, GET /node/api/categories | Display products |
| /stock | GET /node/api/products, GET /node/api/categories | Manage inventory |
| /stock/add | GET /node/api/categories, POST /node/api/products | Add products |
| /cart | POST /python/api/orders | Place orders |
| /orders | GET /python/api/orders | View order history |
| /categories | GET /node/api/categories | Browse categories |

---

### 5. Proxy Service (Nginx - Port 80/443)
**Technology**: Nginx
**Purpose**: Reverse proxy and routing

#### Route Mappings:
- **/** → http://nextjs:3000 (Frontend)
- **/php/api/** → http://api-php:8000/api (PHP Service)
- **/node/api/** → http://api-node:8001/api (Node.js Service)
- **/python/api/** → http://api-python:8002/api (Python Service)

---

### 6. Database Service (Port 5432)
**Technology**: PostgreSQL 11
**Purpose**: Data persistence for all services

#### Consumers:
- api-php (users, authentication)
- api-node (products, categories, users)
- api-python (orders, order details)

---

## Service Communication Flow

### Authentication Flow:
1. Frontend → PHP Service: User logs in via POST /php/api/login
2. PHP Service → Frontend: Returns JWT token
3. Frontend → PHP Service: Validates token via GET /php/api/validate
4. Frontend stores token for subsequent authenticated requests

### Order Creation Flow:
1. Frontend → Python Service: POST /python/api/orders with JWT token
2. Python Service → PHP Service: GET /php/api/validate to verify token
3. PHP Service → Python Service: Returns validated user info
4. Python Service → Database: Creates order and order details
5. Python Service → Frontend: Returns order confirmation

### Product Management Flow:
1. Frontend → Node Service: GET /node/api/products with JWT token
2. Node Service → Database: Fetches products
3. Node Service → Frontend: Returns product list with pagination

---

## API Authentication Summary

### Public APIs (No Authentication Required): 5
- POST /php/api/login
- POST /php/api/signup
- POST /node/api/auth/signin
- POST /node/api/auth/signup
- GET /node/api
- GET /node/api/users

### Protected APIs (JWT Authentication Required): 9
- GET /php/api/validate
- GET /node/api/products
- POST /node/api/products
- POST /node/api/products/:id
- GET /node/api/products/:id/delete
- GET /node/api/categories
- GET /python/api/orders
- POST /python/api/orders

---

## Inter-Service Communication

### Python Service → PHP Service
- **Endpoint**: GET http://api-php:8000/api/validate
- **Purpose**: Token validation for order operations
- **Frequency**: Every authenticated request to Python service

### Frontend → All Services
- **PHP Service**: Authentication (login, signup, validate)
- **Node Service**: Products and categories management
- **Python Service**: Order management

---

## Database Schema

### Tables:
1. **Users** (api-php, api-node, api-python)
   - id, name, email, password, usertype, timestamps

2. **Categories** (api-node)
   - id, name, description, timestamps

3. **Products** (api-node)
   - id, name, description, price, stock, categoryId, userId, timestamps

4. **Orders** (api-python)
   - id, seller_id, buyer_id, date, total, timestamps

5. **Order Details** (api-python)
   - id, order_id, product_id, amount, price, total, timestamps

---

## Key Insights

### API Distribution:
- **Authentication**: Handled by PHP service (3 APIs)
- **Business Logic**: Handled by Node.js service (9 APIs)
- **Transactions**: Handled by Python service (2 APIs)

### Service Dependencies:
- **PHP Service**: Independent (no dependencies on other services)
- **Node Service**: Independent (no dependencies on other services)
- **Python Service**: Depends on PHP service for authentication validation
- **Frontend**: Depends on all three backend services

### Technology Stack:
- **Frontend**: Next.js (React)
- **Backend**: PHP (Lumen), Node.js (Express), Python (Flask)
- **Database**: PostgreSQL
- **Proxy**: Nginx
- **Containerization**: Docker & Docker Compose

---

## Total Endpoint Count: 15 REST APIs + 8 Frontend Pages

This architecture demonstrates a polyglot microservices approach with clear separation of concerns:
- Authentication is centralized in PHP
- Product catalog is managed by Node.js
- Order processing is handled by Python
- All services share a common PostgreSQL database
