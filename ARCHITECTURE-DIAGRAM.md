# Microservices Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              END USERS (Browser)                             │
└────────────────────────────────┬────────────────────────────────────────────┘
                                 │ HTTP Requests
                                 ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                        NGINX PROXY (Port 80)                                │
│                           API Gateway                                        │
│  Routes:                                                                     │
│    /              → frontend:3000                                           │
│    /php/api/*     → api-php:8000                                            │
│    /node/api/*    → api-node:8001                                           │
│    /python/api/*  → api-python:8002                                         │
└──────┬───────────────────┬───────────────────┬────────────────┬─────────────┘
       │                   │                   │                │
       ▼                   ▼                   ▼                ▼
┌─────────────┐  ┌──────────────────┐  ┌──────────────┐  ┌──────────────┐
│  FRONTEND   │  │    API-PHP       │  │   API-NODE   │  │  API-PYTHON  │
│  (Next.js)  │  │  (PHP/Lumen)     │  │ (Node/Express)│  │(Python/Flask)│
│  Port: 3000 │  │   Port: 8000     │  │  Port: 8001  │  │  Port: 8002  │
├─────────────┤  ├──────────────────┤  ├──────────────┤  ├──────────────┤
│ 8 Routes:   │  │ 3 Endpoints:     │  │ 11 Endpoints:│  │ 2 Endpoints: │
│             │  │                  │  │              │  │              │
│ • /         │  │ • POST /login    │  │ • GET /api   │  │ • GET /orders│
│ • /register │  │ • POST /signup   │  │ • Auth:      │  │ • POST /order│
│ • /market   │  │ • GET /validate  │  │   - signin   │  │              │
│ • /cart     │  │                  │  │   - signup   │  │ Auth via:    │
│ • /orders   │  │ Purpose:         │  │ • Users:     │  │ api-php      │
│ • /stock    │  │ - JWT Auth       │  │   - GET list │  │              │
│ • /stock/add│  │ - User Mgmt      │  │ • Products:  │  │              │
│ • /categories│ │                  │  │   - GET list │  │              │
│             │  │                  │  │   - POST new │  │              │
│ Purpose:    │  │                  │  │   - POST edit│  │              │
│ - UI for    │  │                  │  │   - GET del  │  │              │
│   buyers &  │  │                  │  │ • Categories:│  │              │
│   sellers   │  │                  │  │   - GET list │  │              │
│             │  │                  │  │              │  │              │
│             │  │                  │  │ Purpose:     │  │ Purpose:     │
│             │  │                  │  │ - Products   │  │ - Order Mgmt │
│             │  │                  │  │ - Categories │  │              │
└──────┬──────┘  └──────────┬───────┘  └───────┬──────┘  └──────┬───────┘
       │                    │                  │                 │
       │                    │                  │                 │
       │    Consumes APIs   │                  │                 │
       └────────────────────┼──────────────────┼─────────────────┘
                            │                  │
                            │                  │ DB Connection
                            │                  ▼
                            │         ┌──────────────────┐
                            │         │   POSTGRESQL     │
                            │         │   Port: 5432     │
                            │         ├──────────────────┤
                            │         │ Stores:          │
                            │         │ • users          │
                            │         │ • products       │
                            │         │ • categories     │
                            │         │ • orders         │
                            │         │ • details        │
                            │         └──────────────────┘
                            │
                            │ Token Validation
                            └─────────────────────────┐
                                                      │
                            ┌─────────────────────────┘
                            │
                            ▼
                    api-python validates
                    auth via api-php


═══════════════════════════════════════════════════════════════════════════════

                         SERVICE DEPENDENCIES

┌─────────────┐
│  Frontend   │
└──────┬──────┘
       │
       ├─────► api-php     (Login, Signup, Validate)
       ├─────► api-node    (Products, Categories)
       └─────► api-python  (Orders)

┌─────────────┐
│ api-python  │
└──────┬──────┘
       │
       └─────► api-php     (Token Validation)

┌─────────────┐
│  api-node   │
└──────┬──────┘
       │
       └─────► postgres    (Data Storage)


═══════════════════════════════════════════════════════════════════════════════

                          AUTHENTICATION FLOW

    1. User Login
       └─► Frontend → api-php (/login)
           └─► Returns JWT Token

    2. Protected Request
       └─► Frontend → Any API (with token)
           ├─► api-node: Validates token via middleware
           └─► api-python: Validates token via api-php (/validate)

    3. Token Storage
       └─► Stored in browser cookies
           └─► Included in Authorization header for API requests


═══════════════════════════════════════════════════════════════════════════════

                            USER FLOWS

    BUYER FLOW:
    1. Login → api-php
    2. Browse Products → api-node
    3. Add to Cart → frontend (local storage)
    4. Create Order → api-python
    5. View Orders → api-python

    SELLER FLOW:
    1. Login → api-php
    2. View Stock → api-node
    3. Add Product → api-node
    4. Manage Inventory → api-node
    5. View Categories → api-node


═══════════════════════════════════════════════════════════════════════════════

                          TECHNOLOGY STACK

    Frontend:           Next.js, React, JavaScript
    API Gateway:        Nginx (reverse proxy)
    Auth Service:       PHP 7.4, Lumen (micro-framework)
    Product Service:    Node.js 14, Express, Sequelize ORM
    Order Service:      Python 3.8, Flask, SQLAlchemy ORM
    Database:           PostgreSQL 11
    Orchestration:      Docker, Docker Compose
    Network:            Docker Bridge Network (backend)
    Authentication:     JWT (JSON Web Tokens)


═══════════════════════════════════════════════════════════════════════════════

                       PORT MAPPINGS

    External Port  →  Internal Service
    ─────────────────────────────────
    80            →  proxy (Nginx)
    3000          →  frontend (Next.js)
    8000          →  api-php (Lumen)
    8001          →  api-node (Express)
    8002          →  api-python (Flask)
    5432          →  postgres (PostgreSQL)

    Note: External users only access port 80 (proxy)
          All other ports are for internal Docker network communication


═══════════════════════════════════════════════════════════════════════════════
```

## Quick Reference

### Total Counts
- **Services**: 6
- **API Endpoints**: 16 (3 + 11 + 2)
- **Frontend Routes**: 8
- **Inter-Service Calls**: 2 (api-python→api-php, api-node→postgres)

### Service Responsibilities
- **api-php**: Authentication & User Management
- **api-node**: Products, Categories, Users
- **api-python**: Order Management
- **frontend**: UI for Buyers & Sellers
- **proxy**: API Gateway & Routing
- **postgres**: Data Persistence

### Key Patterns
- **API Gateway**: Nginx routes all traffic
- **JWT Auth**: Centralized in api-php
- **Microservices**: Independent, loosely coupled services
- **Docker Network**: Internal communication via 'backend' network
- **Database per Service**: Only api-node has direct DB access
