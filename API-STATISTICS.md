# API Statistics Summary

## Total API Endpoints: 15

### By Service:
| Service | Number of APIs | Percentage |
|---------|----------------|------------|
| api-node (Node.js) | 9 | 60% |
| api-php (PHP) | 3 | 20% |
| api-python (Python) | 2 | 13% |
| frontend (Next.js) | 8 pages | - |
| **TOTAL REST APIs** | **15** | **100%** |

---

## API Breakdown by HTTP Method:
| Method | Count | APIs |
|--------|-------|------|
| GET | 9 | /api, /api/users, /api/products, /api/products/:id/delete, /api/categories, /api/validate, /api/orders |
| POST | 6 | /api/login, /api/signup, /api/auth/signin, /api/auth/signup, /api/products, /api/products/:id, /api/orders |

---

## Authentication Statistics:
| Type | Count | Percentage |
|------|-------|------------|
| Public APIs (No Auth) | 6 | 40% |
| Protected APIs (JWT Auth) | 9 | 60% |

---

## Service Communication Patterns:

### API Providers:
1. **api-php**: Provides authentication services (3 APIs)
2. **api-node**: Provides catalog services (9 APIs)
3. **api-python**: Provides order services (2 APIs)

### API Consumers:
1. **frontend**: Consumes 8 different APIs from all services
2. **api-python**: Consumes 1 API from api-php (token validation)

### Inter-Service Dependencies:
- **api-php → Database** (direct)
- **api-node → Database** (direct)
- **api-python → Database** (direct)
- **api-python → api-php** (for token validation)
- **frontend → api-php** (authentication)
- **frontend → api-node** (products, categories)
- **frontend → api-python** (orders)

---

## API Categories by Function:

### Authentication (3 APIs):
1. POST /php/api/login
2. POST /php/api/signup
3. GET /php/api/validate

### Product Management (5 APIs):
1. GET /node/api/products
2. POST /node/api/products
3. POST /node/api/products/:id
4. GET /node/api/products/:id/delete
5. GET /node/api/categories

### User Management (4 APIs):
1. GET /node/api/users
2. POST /node/api/auth/signin
3. POST /node/api/auth/signup
4. GET /php/api/validate

### Order Management (2 APIs):
1. GET /python/api/orders
2. POST /python/api/orders

### Health Check (1 API):
1. GET /node/api

---

## Data Flow Summary:

### Inbound Traffic (Frontend → Services):
- **Frontend → api-php**: 3 API calls (login, signup, validate)
- **Frontend → api-node**: 5 API calls (products CRUD, categories)
- **Frontend → api-python**: 2 API calls (orders)
- **Total Frontend API Usage**: 10 unique endpoints

### Service-to-Service Traffic:
- **api-python → api-php**: 1 API call (validate token)

### Database Connections:
- **All 3 backend services connect to the same PostgreSQL database**

---

## Port Allocation:
| Service | Port | Protocol |
|---------|------|----------|
| nginx (proxy) | 80, 443 | HTTP/HTTPS |
| frontend | 3000 | HTTP |
| api-php | 8000 | HTTP |
| api-node | 8001 | HTTP |
| api-python | 8002 | HTTP |
| postgres | 5432 | PostgreSQL |

---

## Technology Stack Distribution:

### Backend Services (3):
- **PHP** (Lumen Framework) - 1 service
- **Node.js** (Express Framework) - 1 service
- **Python** (Flask Framework) - 1 service

### Frontend (1):
- **Next.js** (React Framework)

### Infrastructure (2):
- **Nginx** (Reverse Proxy)
- **PostgreSQL** (Database)

---

## Request/Response Format:
- **Content-Type**: application/json (all services)
- **Authentication**: JWT Bearer token
- **Response Structure**: Consistent across services with 'data' and 'errors' fields

---

## Key Metrics:
- **Total Microservices**: 3 (PHP, Node.js, Python)
- **Total REST Endpoints**: 15
- **Total Frontend Pages**: 8
- **Total Services**: 6 (including proxy, database, frontend)
- **Total Ports Used**: 6
- **Average APIs per Service**: 5
- **Authentication Provider**: Centralized (api-php)
- **Shared Database**: Yes (PostgreSQL)

---

## CSV File Details:
- **File Name**: `microservices-api-documentation.csv`
- **Total Rows**: 31 (including header)
- **Columns**: 10 (Microservice, Port, Endpoint Path, HTTP Method, Authentication Required, Description, Consumer Service, Consumer Type, Request Body/Params, Response Type)

---

## Documentation Files Created:
1. **microservices-api-documentation.csv** - Detailed CSV with all API information
2. **API-DOCUMENTATION.md** - Comprehensive markdown documentation
3. **API-STATISTICS.md** - This statistics summary file

---

## Architecture Pattern:
- **Pattern**: Polyglot Microservices with API Gateway
- **Gateway**: Nginx reverse proxy
- **Database**: Shared PostgreSQL (not fully microservices pattern)
- **Authentication**: Centralized JWT with api-php
- **Communication**: REST over HTTP
- **Deployment**: Docker Compose
