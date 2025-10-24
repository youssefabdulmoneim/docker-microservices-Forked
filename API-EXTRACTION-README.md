# Microservices API Extraction - Documentation

This documentation provides a complete analysis of all microservices in this repository, including their exposed endpoints and service consumers.

## 📋 Files Created

### 1. `microservices-api-documentation.csv` 
**Main CSV file with complete API catalog**

Contains 30 rows (+ 1 header) with the following columns:
- Microservice name
- Port number
- Endpoint path
- HTTP method
- Authentication requirements
- Endpoint description
- Consumer service name
- Consumer type
- Request body/parameters
- Response type

**Total APIs documented: 15 REST endpoints + 8 frontend pages**

### 2. `API-DOCUMENTATION.md`
**Comprehensive markdown documentation**

Includes:
- Overview of all microservices
- Detailed breakdown of each service's APIs
- Service communication flows (authentication, orders, products)
- API authentication summary
- Inter-service communication patterns
- Database schema
- Technology stack details
- Architecture insights

### 3. `API-STATISTICS.md`
**Statistical analysis and metrics**

Provides:
- API count by service (Node.js: 9, PHP: 3, Python: 2)
- HTTP method distribution (GET: 9, POST: 6)
- Authentication statistics (Public: 6, Protected: 9)
- Service communication patterns
- API categories by function
- Data flow summary
- Port allocation
- Technology stack distribution
- Key metrics and architecture pattern

## 🏗️ Architecture Summary

### Microservices (3):
1. **api-php** (Port 8000) - Authentication & User Management
   - 3 APIs: login, signup, validate
   
2. **api-node** (Port 8001) - Product & Category Management
   - 9 APIs: products CRUD, categories, users, auth alternatives
   
3. **api-python** (Port 8002) - Order Management
   - 2 APIs: get orders, create orders

### Supporting Services:
- **Frontend** (Next.js on port 3000) - 8 pages
- **Nginx Proxy** (Port 80/443) - Reverse proxy
- **PostgreSQL** (Port 5432) - Shared database

## 🔗 Service Dependencies

```
Frontend → api-php (authentication)
Frontend → api-node (products, categories)
Frontend → api-python (orders)
api-python → api-php (token validation)
All backend services → PostgreSQL
```

## 📊 Quick Statistics

- **Total REST APIs**: 15
- **Total Frontend Pages**: 8
- **Total Services**: 6
- **Public APIs**: 6 (40%)
- **Protected APIs**: 9 (60%)
- **Inter-service calls**: 1 (Python → PHP)

## 🔐 Authentication Flow

1. User logs in via `POST /php/api/login`
2. Receives JWT token
3. Token validated via `GET /php/api/validate`
4. Token used for all protected endpoints
5. Python service validates tokens with PHP service

## 📝 How to Use

### View in CSV Format:
```bash
cat microservices-api-documentation.csv
```

### View as Table (Linux/Mac):
```bash
column -t -s, microservices-api-documentation.csv
```

### Import to Excel/Google Sheets:
Simply open the CSV file in your spreadsheet application.

### Read Markdown Documentation:
```bash
cat API-DOCUMENTATION.md
cat API-STATISTICS.md
```

## 🎯 Use Cases

1. **API Discovery**: Quickly find all available endpoints
2. **Integration**: Understand which services consume which APIs
3. **Documentation**: Share with team members and stakeholders
4. **Planning**: Identify dependencies before making changes
5. **Onboarding**: Help new developers understand the architecture

## 📌 Key Insights

1. **Polyglot Architecture**: Uses PHP, Node.js, and Python
2. **Centralized Authentication**: PHP service handles all auth
3. **Shared Database**: All services use same PostgreSQL instance
4. **Frontend Integration**: Next.js frontend consumes 10 endpoints
5. **Service Communication**: Only Python calls PHP (token validation)

---

**Generated**: 2025-10-24  
**Repository**: docker-microservices-Forked  
**Documentation covers**: 15 REST APIs, 8 Frontend Pages, 6 Services
