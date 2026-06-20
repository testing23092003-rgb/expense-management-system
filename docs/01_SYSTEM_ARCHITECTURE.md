# System Architecture

## Overview

The Expense Management System is built using a layered architecture with Laravel as the backend framework, Vue.js 3 with Inertia.js for the frontend, and PostgreSQL for data persistence.

## Architecture Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                      Presentation Layer                      │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ Web App (Vue.js 3 + Inertia.js)                      │  │
│  │ Mobile App (React Native / Progressive Web App)      │  │
│  │ Admin Dashboard | Manager Dashboard | Employee App  │  │
│  └──────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
                          ↓ HTTP/REST API ↓
┌─────────────────────────────────────────────────────────────┐
│                    API Gateway Layer                         │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ CORS | Rate Limiting | Request Validation | Logging │  │
│  │ JWT Authentication Middleware                        │  │
│  └──────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
                          ↓ Laravel Routing ↓
┌─────────────────────────────────────────────────────────────┐
│                    Business Logic Layer                      │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ Controllers                                          │  │
│  │ ├─ AuthController                                   │  │
│  │ ├─ EmployeeController                               │  │
│  │ ├─ ExpenseController                                │  │
│  │ ├─ TravelExpenseController                           │  │
│  │ ├─ ApprovalController                               │  │
│  │ ├─ DashboardController                              │  │
│  │ ├─ ReportController                                 │  │
│  │ └─ AdminController                                  │  │
│  └──────────────────────────────────────────────────────┘  │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ Services (Business Logic)                            │  │
│  │ ├─ ExpenseService                                   │  │
│  │ ├─ ApprovalWorkflowService                          │  │
│  │ ├─ TravelExpenseService                             │  │
│  │ ├─ GoogleMapsService                                │  │
│  │ ├─ ReportService                                    │  │
│  │ ├─ NotificationService                              │  │
│  │ ├─ AuditService                                     │  │
│  │ ├─ RBACService                                      │  │
│  │ └─ BudgetService                                    │  │
│  └──────────────────────────────────────────────────────┘  │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ Policies & Authorization                             │  │
│  │ ├─ ExpensePolicy                                    │  │
│  │ ├─ ApprovalPolicy                                   │  │
│  │ └─ AdminPolicy                                      │  │
│  └──────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
                          ↓ Eloquent ORM ↓
┌─────────────────────────────────────────────────────────────┐
│                      Data Access Layer                       │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ Models (20+ Eloquent Models)                         │  │
│  │ ├─ User, Role, Permission, Department               │  │
│  │ ├─ Employee, Designation, Branch                    │  │
│  │ ├─ Expense, ExpenseCategory, ExpenseAttachment      │  │
│  │ ├─ TravelExpense, TravelRoute, KMRate               │  │
│  │ ├─ ApprovalWorkflow, BudgetLimit, ExpensePolicy     │  │
│  │ ├─ AuditLog, ActivityLog, Notification              │  │
│  │ └─ EscalationRule                                   │  │
│  └──────────────────────────────────────────────────────┘  │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ Repository Pattern (Optional but Recommended)        │  │
│  │ ├─ ExpenseRepository                                │  │
│  │ ├─ EmployeeRepository                               │  │
│  │ └─ etc...                                           │  │
│  └──────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
                          ↓ Database Queries ↓
┌─────────────────────────────────────────────────────────────┐
│                    Database Layer                            │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ PostgreSQL 14+                                       │  │
│  │ ├─ Primary Database                                 │  │
│  │ ├─ Backup & Replication                             │  │
│  │ └─ Read Replicas (for scaling)                      │  │
│  └──────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│                    Supporting Services                       │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ Cache Layer (Redis)                                  │  │
│  │ ├─ Session Storage                                  │  │
│  │ ├─ Query Caching                                    │  │
│  │ └─ Rate Limiting Counters                           │  │
│  └──────────────────────────────────────────────────────┘  │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ Queue System (Redis Queue)                           │  │
│  │ ├─ Email Notifications                              │  │
│  │ ├─ Push Notifications                               │  │
│  │ ├─ Report Generation                                │  │
│  │ ├─ OCR Processing                                   │  │
│  │ └─ Audit Logging                                    │  │
│  └──────────────────────────────────────────────────────┘  │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ Real-time Services (Laravel Reverb)                  │  │
│  │ ├─ Live Notifications                               │  │
│  │ ├─ Real-time Dashboard Updates                      │  │
│  │ └─ Approval Status Updates                          │  │
│  └──────────────────────────────────────────────────────┘  │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ External APIs                                        │  │
│  │ ├─ Google Maps API (Distance Calculation)           │  │
│  │ ├─ Twilio API (SMS Notifications)                   │  │
│  │ ├─ SendGrid API (Email)                             │  │
│  │ ├─ AWS S3 (File Storage)                            │  │
│  │ ├─ OCR Service (Bill Scanning)                      │  │
│  │ └─ Sentry (Error Tracking)                          │  │
│  └──────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
```

## Design Patterns

### 1. MVC Pattern
- **Model**: Eloquent Models represent database entities
- **View**: Inertia.js Vue components for rendering
- **Controller**: Handles HTTP requests and orchestrates business logic

### 2. Service Layer Pattern
```
Controller → Service → Model → Database
```
Services encapsulate business logic and are reusable across multiple controllers.

### 3. Repository Pattern (Optional)
Provides an abstraction over the data access layer, making it easier to switch databases or implement caching.

### 4. Policy Pattern
Laravel policies handle authorization logic and are used with middleware to control access.

### 5. Event-Driven Architecture
Events are fired for key actions:
- `ExpenseCreated` → Sends notification to manager
- `ExpenseApproved` → Updates budget, sends notification
- `ApprovalRejected` → Sends notification, creates activity log

### 6. Job Queue Pattern
Asynchronous tasks are queued for:
- Email sending
- PDF/Excel generation
- OCR processing
- Notification delivery

## Request/Response Flow

### Typical Expense Submission Flow

```
1. User submits expense form
   ↓
2. Frontend validates form (Vue.js)
   ↓
3. HTTP POST to /api/expenses
   ↓
4. ExpenseController@store receives request
   ↓
5. StoreExpenseRequest validates input
   ↓
6. ExpenseService processes expense
   ├─ Calculate travel distance (if travel expense)
   ├─ Validate budget limits
   ├─ Create expense record
   ├─ Create initial approval workflow
   └─ Fire ExpenseCreated event
   ↓
7. ExpenseCreated event listener
   ├─ Sends notification to manager
   ├─ Creates activity log
   └─ Updates Redis cache
   ↓
8. Queue jobs for:
   ├─ Send email notification
   └─ Push notification
   ↓
9. Return JSON response with expense data
   ↓
10. Frontend updates UI and shows confirmation
```

## Authentication & Authorization

### Authentication Flow
```
1. User logs in with email/password
   ↓
2. AuthController validates credentials
   ↓
3. JWT token generated (with user claims)
   ↓
4. Token stored in frontend (secure HTTP-only cookie)
   ↓
5. Token sent with each API request in Authorization header
   ↓
6. JWTMiddleware verifies token
   ↓
7. Request proceeds if valid, else returns 401
```

### Authorization Flow
```
1. Authenticated request received
   ↓
2. Check user roles and permissions
   ↓
3. Check resource-level policies
   ↓
4. Check RBAC permissions (module, page, menu access)
   ↓
5. Grant or deny access
```

## RBAC Structure

### Hierarchy
```
Role (Super Admin, Admin, Senior Manager, Manager, Team Lead, Employee)
  ↓
Permissions (View, Add, Edit, Delete, Approve, Reject, Export)
  ↓
Module Access (Employees, Expenses, Reports, Admin Settings)
  ↓
Page Access (Specific UI pages)
  ↓
Menu Access (Menu items visibility)
```

### Permission Matrix

```
┌──────────────┬────┬────┬────┬────┬────────┬────┬────────┐
│ Role         │V  │A  │E  │D  │Approve │Reject│Export│
├──────────────┼────┼────┼────┼────┼────────┼────┼────────┤
│Super Admin   │✓  │✓  │✓  │✓  │✓      │✓   │✓     │
│Admin         │✓  │✓  │✓  │✓  │✓      │✓   │✓     │
│Sr. Manager   │✓  │✗  │✗  │✗  │✓      │✓   │✓     │
│Manager       │✓  │✗  │✗  │✗  │✓      │✓   │✓     │
│Team Lead     │✓  │✗  │✗  │✗  │✗      │✗   │✓     │
│Employee      │✓  │✓  │✓  │✗  │✗      │✗   │✓     │
└──────────────┴────┴────┴────┴────┴────────┴────┴────────┘

V = View, A = Add, E = Edit, D = Delete
```

## Data Flow for Travel Expense

```
1. User selects "Travel" category
   ↓
2. Travel expense form displayed
   ↓
3. User enters:
   ├─ Source location
   ├─ Destination(s)
   └─ Vehicle type
   ↓
4. Frontend calls Google Maps API
   ├─ Calculates total distance
   └─ Gets coordinates and route
   ↓
5. KM field auto-populated (read-only)
   ↓
6. User can add Extra KM (if needed, requires reason)
   ↓
7. Frontend calls Rate Master API to get rate per KM
   ↓
8. Amount auto-calculated: (Total KM) × (Rate per KM)
   ↓
9. User submits expense
   ↓
10. TravelExpenseService:
    ├─ Validates all inputs
    ├─ Creates Travel Expense record
    ├─ Stores route data (coordinates, distance, polyline)
    ├─ Stores audit trail
    └─ Initiates approval workflow
    ↓
11. Expense enters approval pipeline
```

## Scalability Considerations

### Horizontal Scaling
- **Stateless Application**: All servers can handle any request
- **Shared Cache**: Redis for distributed caching
- **Database Replication**: Read replicas for reporting queries
- **Load Balancing**: Nginx/HAProxy for traffic distribution

### Performance Optimization
- **Database Indexing**: Strategic indexes on frequently queried columns
- **Query Optimization**: Eager loading, select specific columns
- **Caching Strategy**: Cache roles, permissions, KM rates
- **Database Connection Pooling**: Optimize database connections
- **API Rate Limiting**: Prevent abuse

## Security Architecture

### Layers
1. **API Gateway**: Rate limiting, CORS validation
2. **Authentication**: JWT with expiration and refresh tokens
3. **Authorization**: Role-based access control
4. **Data Encryption**: SSL/TLS for transport, encryption at rest for sensitive data
5. **Input Validation**: Form requests, sanitization
6. **SQL Injection Prevention**: Parameterized queries via Eloquent
7. **CSRF Protection**: Token validation for state-changing operations
8. **Audit Logging**: Complete trail of all actions

### Security Headers
- `X-Frame-Options: DENY`
- `X-Content-Type-Options: nosniff`
- `X-XSS-Protection: 1; mode=block`
- `Strict-Transport-Security: max-age=31536000`
- `Content-Security-Policy: default-src 'self'`

## Error Handling & Logging

### Error Handling Strategy
```
1. Client validation errors → 400 Bad Request
2. Authentication errors → 401 Unauthorized
3. Authorization errors → 403 Forbidden
4. Resource not found → 404 Not Found
5. Server errors → 500 Internal Server Error
```

### Logging Levels
- **DEBUG**: Development information
- **INFO**: General information about operations
- **WARNING**: Warning conditions
- **ERROR**: Error conditions
- **CRITICAL**: Critical conditions requiring immediate attention

Logs are stored in `/storage/logs` and monitored via Sentry.

## Deployment Architecture

### Development
- Local development with Docker Compose
- SQLite or PostgreSQL for local database
- Hot module reloading for frontend

### Staging
- Replica of production environment
- Full PostgreSQL database
- Redis caching
- AWS S3 for file uploads

### Production
- Multiple application servers behind load balancer
- Managed PostgreSQL database (RDS)
- Managed Redis (ElastiCache)
- CDN for static assets
- AWS S3 for file storage
- CloudWatch for monitoring
- Auto-scaling groups

## Technology Stack Details

### Backend (Laravel Ecosystem)
- **Framework**: Laravel 11
- **Authentication**: Laravel Sanctum + JWT
- **ORM**: Eloquent
- **Queue**: Redis Queue
- **Real-time**: Laravel Reverb (WebSocket)
- **Validation**: Form Requests
- **Testing**: PHPUnit, Pest
- **API Documentation**: OpenAPI/Swagger

### Frontend (Modern JavaScript)
- **Framework**: Vue.js 3 (Composition API)
- **Meta Framework**: Inertia.js
- **Styling**: Tailwind CSS 3
- **State Management**: Vue 3 Stores (Pinia)
- **HTTP Client**: Axios
- **Maps**: Google Maps JavaScript API
- **Charts**: Chart.js / ApexCharts
- **Tables**: TanStack Vue Table
- **Forms**: VeeValidate
- **Build Tool**: Vite

### Database
- **Primary**: PostgreSQL 14+
- **Cache**: Redis 7+

### DevOps
- **Containerization**: Docker
- **Orchestration**: Docker Compose (local), Kubernetes (production)
- **CI/CD**: GitHub Actions
- **Monitoring**: Sentry, CloudWatch
- **Logging**: ELK Stack / CloudWatch Logs

## Performance Metrics

### Target Metrics
- **API Response Time**: < 200ms for 95% of requests
- **Database Query Time**: < 50ms for 95% of queries
- **Page Load Time**: < 3 seconds
- **Time to Interactive**: < 5 seconds
- **Uptime**: 99.9%

### Monitoring
- Real User Monitoring (RUM) via Sentry
- Application Performance Monitoring (APM)
- Database query logging and analysis
- Infrastructure monitoring via CloudWatch
