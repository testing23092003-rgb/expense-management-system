# API Documentation

## Base URL
```
https://api.expensemanagement.com/v1
```

## Authentication

All API endpoints require JWT authentication via `Authorization` header:
```
Authorization: Bearer <jwt_token>
```

## Response Format

All responses follow a standard format:

### Success Response (200, 201)
```json
{
  "success": true,
  "data": {
    "id": 1,
    "name": "John Doe",
    "email": "john@example.com"
  },
  "message": "Resource retrieved successfully"
}
```

### Error Response (400, 401, 403, 404, 422, 500)
```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Validation failed",
    "details": [
      {
        "field": "email",
        "message": "Email is required"
      }
    ]
  }
}
```

## Authentication Endpoints

### 1. User Login
```
POST /auth/login
```

**Request:**
```json
{
  "email": "user@example.com",
  "password": "password123",
  "remember_me": false
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "user": {
      "id": 1,
      "email": "user@example.com",
      "first_name": "John",
      "last_name": "Doe",
      "roles": ["employee"],
      "permissions": ["view_expenses", "add_expenses"]
    },
    "token": "eyJ0eXAiOiJKV1QiLCJhbGc...",
    "expires_in": 86400
  }
}
```

### 2. User Logout
```
POST /auth/logout
```

### 3. Refresh Token
```
POST /auth/refresh
```

### 4. User Profile
```
GET /auth/me
```

## Employee Endpoints

### 1. List Employees
```
GET /employees
```

**Query Parameters:**
```
?page=1&per_page=20&department_id=1&search=john&sort=name&order=asc
```

**Response:**
```json
{
  "success": true,
  "data": [
    {
      "id": 1,
      "employee_code": "EMP001",
      "name": "John Doe",
      "email": "john@example.com",
      "phone": "+91-9999999999",
      "department": {
        "id": 1,
        "name": "Sales"
      },
      "designation": {
        "id": 1,
        "name": "Senior Manager"
      },
      "manager": {
        "id": 2,
        "name": "Jane Smith"
      },
      "branch": {
        "id": 1,
        "name": "Mumbai"
      },
      "is_active": true
    }
  ],
  "pagination": {
    "current_page": 1,
    "per_page": 20,
    "total": 150,
    "last_page": 8
  }
}
```

### 2. Get Employee
```
GET /employees/{id}
```

### 3. Create Employee
```
POST /employees
```

**Request:**
```json
{
  "employee_code": "EMP002",
  "first_name": "Jane",
  "last_name": "Smith",
  "email": "jane@example.com",
  "phone": "+91-9999999998",
  "department_id": 1,
  "designation_id": 1,
  "manager_id": 1,
  "senior_manager_id": 3,
  "branch_id": 1,
  "date_of_joining": "2024-01-15",
  "role_id": 2,
  "password": "password123"
}
```

### 4. Update Employee
```
PUT /employees/{id}
```

### 5. Delete Employee
```
DELETE /employees/{id}
```

## Expense Endpoints

### 1. List Expenses
```
GET /expenses
```

**Query Parameters:**
```
?page=1&per_page=20&status=submitted&category_id=1&employee_id=1&from_date=2024-01-01&to_date=2024-12-31&sort=created_at&order=desc
```

### 2. Get Expense
```
GET /expenses/{id}
```

### 3. Create Expense
```
POST /expenses
```

**Request:**
```json
{
  "expense_category_id": 1,
  "expense_date": "2024-06-20",
  "amount": 2500.00,
  "description": "Client meeting in Mumbai",
  "client_name": "ABC Corporation",
  "currency": "INR"
}
```

### 4. Update Expense
```
PUT /expenses/{id}
```

Only allowed for draft or sent_back expenses.

### 5. Submit Expense
```
POST /expenses/{id}/submit
```

### 6. Upload Attachment
```
POST /expenses/{id}/attachments
```

**Form Data:**
```
file: <file>
```

### 7. Delete Attachment
```
DELETE /expenses/{id}/attachments/{attachment_id}
```

## Travel Expense Endpoints

### 1. Create Travel Expense
```
POST /expenses/travel
```

**Request:**
```json
{
  "expense_date": "2024-06-20",
  "vehicle_type": "car",
  "source_location": "Mumbai Office",
  "destinations": [
    {
      "location": "Client Office 1",
      "sequence_order": 1
    },
    {
      "location": "Client Office 2",
      "sequence_order": 2
    }
  ],
  "extra_km": 5.5,
  "extra_km_reason": "Traffic diversion",
  "description": "Multiple client visits"
}
```

### 2. Calculate Distance
```
POST /travel-expenses/calculate-distance
```

**Request:**
```json
{
  "source_location": "Mumbai Office",
  "destinations": [
    "Client 1",
    "Client 2",
    "Client 3"
  ],
  "vehicle_type": "car"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "total_km": 45.5,
    "rate_per_km": 10.5,
    "calculated_amount": 477.75,
    "route_details": {
      "legs": [...],
      "polyline": "...",
      "duration": "2 hours 30 mins"
    }
  }
}
```

## Approval Endpoints

### 1. Get Pending Approvals
```
GET /approvals/pending
```

**Query Parameters:**
```
?page=1&per_page=20&approval_type=manager&sort=created_at&order=asc
```

### 2. Approve Expense
```
POST /approvals/{expense_id}/approve
```

**Request:**
```json
{
  "comments": "Approved as per policy"
}
```

### 3. Reject Expense
```
POST /approvals/{expense_id}/reject
```

**Request:**
```json
{
  "rejection_reason": "Amount exceeds limit"
}
```

### 4. Send Back for Changes
```
POST /approvals/{expense_id}/send-back
```

**Request:**
```json
{
  "reason": "Please provide more details about the expense",
  "required_changes": ["description", "attachment"]
}
```

## Dashboard Endpoints

### 1. Employee Dashboard
```
GET /dashboards/employee
```

**Response:**
```json
{
  "success": true,
  "data": {
    "summary": {
      "total_submitted": 15,
      "total_approved": 10,
      "total_pending": 3,
      "total_rejected": 2,
      "total_amount_submitted": 45000,
      "total_amount_approved": 35000
    },
    "recent_expenses": [...],
    "monthly_trend": [...],
    "category_breakdown": [...]
  }
}
```

### 2. Manager Dashboard
```
GET /dashboards/manager
```

### 3. Admin Dashboard
```
GET /dashboards/admin
```

## Report Endpoints

### 1. Employee-wise Report
```
GET /reports/employee-wise
```

**Query Parameters:**
```
?from_date=2024-01-01&to_date=2024-12-31&department_id=1&format=json
```

### 2. Department-wise Report
```
GET /reports/department-wise
```

### 3. Category-wise Report
```
GET /reports/category-wise
```

### 4. Manager-wise Report
```
GET /reports/manager-wise
```

### 5. Travel KM Report
```
GET /reports/travel-km
```

### 6. Export Report
```
GET /reports/{report_type}/export
```

**Query Parameters:**
```
?format=excel&from_date=2024-01-01&to_date=2024-12-31
```

Supported formats: excel, pdf, csv

## RBAC Administration Endpoints

### 1. List Roles
```
GET /admin/roles
```

### 2. Create Role
```
POST /admin/roles
```

**Request:**
```json
{
  "name": "Department Head",
  "description": "Department head role",
  "hierarchy_level": 3
}
```

### 3. Update Role Permissions
```
PUT /admin/roles/{role_id}/permissions
```

**Request:**
```json
{
  "permission_ids": [1, 2, 3, 4, 5]
}
```

### 4. Update Page Access
```
PUT /admin/roles/{role_id}/page-access
```

**Request:**
```json
{
  "pages": [
    {
      "route": "/dashboard",
      "can_access": true
    },
    {
      "route": "/admin/settings",
      "can_access": false
    }
  ]
}
```

### 5. Update Menu Access
```
PUT /admin/roles/{role_id}/menu-access
```

### 6. Update Module Access
```
PUT /admin/roles/{role_id}/module-access
```

## Master Data Endpoints

### Departments
```
GET /master/departments
POST /master/departments
PUT /master/departments/{id}
DELETE /master/departments/{id}
```

### Designations
```
GET /master/designations
POST /master/designations
PUT /master/designations/{id}
DELETE /master/designations/{id}
```

### Branches
```
GET /master/branches
POST /master/branches
PUT /master/branches/{id}
DELETE /master/branches/{id}
```

### Expense Categories
```
GET /master/expense-categories
POST /master/expense-categories
PUT /master/expense-categories/{id}
DELETE /master/expense-categories/{id}
```

### KM Rates
```
GET /master/km-rates
POST /master/km-rates
PUT /master/km-rates/{id}
DELETE /master/km-rates/{id}
```

## Error Codes

| Code | HTTP Status | Description |
|------|-------------|-------------|
| UNAUTHORIZED | 401 | Authentication failed |
| FORBIDDEN | 403 | User lacks permissions |
| VALIDATION_ERROR | 422 | Input validation failed |
| NOT_FOUND | 404 | Resource not found |
| DUPLICATE_ENTRY | 409 | Duplicate entry |
| RATE_LIMIT_EXCEEDED | 429 | Too many requests |
| INTERNAL_ERROR | 500 | Server error |

## Rate Limiting

- **Authenticated requests**: 1000 requests per hour per user
- **Public endpoints**: 100 requests per hour per IP
- **File uploads**: 50 requests per hour per user

## Pagination

Default per_page: 20
Max per_page: 100

## Sorting

Supported sort parameters vary by endpoint. Use `sort=field&order=asc|desc`
