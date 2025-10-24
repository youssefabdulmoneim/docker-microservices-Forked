# Microservices API Documentation

This directory contains comprehensive documentation of all microservices, their endpoints, and service dependencies in this Docker-based microservices architecture.

## 📊 Documentation Files

### 1. **microservices-api-documentation.csv**
**Purpose**: Detailed endpoint-level documentation  
**Contents**: Every API endpoint with complete details including:
- Service name, type, and port
- Endpoint path and HTTP method
- Full URL (via proxy)
- Authentication requirements
- Purpose and description
- Request/response formats
- Service dependencies and consumers
- Internal service URLs
- Technology stack

**Use this when**: You need detailed information about a specific API endpoint

### 2. **microservices-summary.csv**
**Purpose**: High-level service overview  
**Contents**: Summary of each microservice including:
- Service type and technology
- Total number of endpoints
- Overall purpose
- Dependencies (what it consumes)
- Dependents (what consumes it)
- Database connections
- Key features

**Use this when**: You need a quick overview of the entire architecture

### 3. **service-communication-flow.csv**
**Purpose**: Service-to-service communication patterns  
**Contents**: How services interact with each other:
- Source and target services
- Communication type (HTTP REST, Database, Proxy)
- Purpose of communication
- Data flow direction
- Authentication methods
- Example use cases

**Use this when**: You need to understand how services communicate

### 4. **microservices-statistics.csv**
**Purpose**: Architecture metrics and statistics  
**Contents**: Quantitative overview including:
- Total counts (services, endpoints, routes)
- Service categorization
- Dependencies count
- Authentication methods
- Technologies used
- Port mappings
- Architecture patterns

**Use this when**: You need metrics about the architecture

## 🏗️ Architecture Overview

This application follows a microservices architecture with 6 main services:

### Backend Services (APIs)
1. **api-php** (Port 8000) - Authentication & User Management
   - 3 endpoints
   - Technology: PHP/Lumen
   - Handles: Login, Signup, Token Validation

2. **api-node** (Port 8001) - Product & Category Management
   - 11 endpoints
   - Technology: Node.js/Express/Sequelize
   - Handles: Products CRUD, Categories, Users
   - Database: PostgreSQL

3. **api-python** (Port 8002) - Order Management
   - 2 endpoints
   - Technology: Python/Flask/SQLAlchemy
   - Handles: Order Creation, Order Listing
   - Depends on: api-php (for authentication)

### Frontend Service
4. **frontend** (Port 3000) - User Interface
   - 8 routes
   - Technology: Next.js/React
   - Provides: Server-side rendered pages for buyers and sellers
   - Consumes: All three backend APIs

### Infrastructure Services
5. **proxy** (Port 80) - Reverse Proxy & API Gateway
   - Technology: Nginx
   - Routes external traffic to internal services:
     - `/` → frontend:3000
     - `/php/api/*` → api-php:8000
     - `/node/api/*` → api-node:8001
     - `/python/api/*` → api-python:8002

6. **postgres** (Port 5432) - Database
   - Technology: PostgreSQL 11
   - Used by: api-node
   - Stores: Users, Products, Categories, Orders

## 📈 Key Statistics

- **Total API Endpoints**: 16 (3 + 11 + 2)
- **Frontend Routes**: 8
- **Public Endpoints**: 5 (no authentication required)
- **Protected Endpoints**: 11 (JWT token required)
- **Inter-Service Dependencies**: 2
  - api-python → api-php (auth validation)
  - api-node → postgres (data storage)

## 🔐 Authentication Flow

1. User logs in via frontend → api-php `/login` endpoint
2. api-php validates credentials and returns JWT token
3. Frontend stores token in cookie
4. For protected endpoints:
   - Frontend includes token in Authorization header
   - Backend services validate token (api-python validates via api-php)
   - If valid, request proceeds

## 🔄 Common User Flows

### Buyer Flow
1. Login → Browse Products → Add to Cart → Create Order → View Orders
2. Services used: api-php → api-node → api-python

### Seller Flow
1. Login → View Stock → Add Products → Manage Inventory
2. Services used: api-php → api-node

## 🚀 Getting Started

To explore these services:

1. **View endpoint details**: Open `microservices-api-documentation.csv`
2. **Understand service roles**: Open `microservices-summary.csv`
3. **Trace communication**: Open `service-communication-flow.csv`
4. **See metrics**: Open `microservices-statistics.csv`

## 📝 Notes

- All services communicate over a Docker bridge network named `backend`
- External access is only through the Nginx proxy (port 80)
- JWT tokens are used for authentication across all services
- api-python delegates authentication to api-php for consistency
- The architecture follows API Gateway pattern with Nginx as the gateway

## 🛠️ Technology Stack

| Layer | Technologies |
|-------|-------------|
| Frontend | Next.js, React |
| Backend APIs | PHP/Lumen, Node.js/Express, Python/Flask |
| Database | PostgreSQL 11 |
| Proxy/Gateway | Nginx |
| Container Orchestration | Docker, Docker Compose |
| Authentication | JWT (JSON Web Tokens) |

---

**Total Services**: 6  
**Total Endpoints**: 16 API + 8 Frontend = 24  
**Architecture Pattern**: Microservices with API Gateway  
**Last Updated**: 2025-10-24
