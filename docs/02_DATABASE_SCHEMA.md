# Database Schema

## Entity Relationship Diagram

```
                              ┌─────────────────────┐
                              │      users          │
                              │─────────────────────│
                              │ id (PK)             │
                              │ email (UNIQUE)      │
                              │ password            │
                              │ first_name          │
                              │ last_name           │
                              │ is_active           │
                              │ last_login_at       │
                              │ created_at          │
                              │ updated_at          │
                              └──────────┬──────────┘
                                         │
                  ┌──────────────────────┼──────────────────────┐
                  │                      │                      │
                  ▼                      ▼                      ▼
        ┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐
        │    roles         │  │  employees       │  │   user_sessions  │
        │──────────────────│  │──────────────────│  │──────────────────│
        │ id (PK)          │  │ id (PK)          │  │ id (PK)          │
        │ name (UNIQUE)    │  │ user_id (FK)     │  │ user_id (FK)     │
        │ description      │  │ employee_code    │  │ token            │
        │ guard_name       │  │ department_id    │  │ ip_address       │
        │ created_at       │  │ designation_id   │  │ user_agent       │
        │ updated_at       │  │ manager_id       │  │ expires_at       │
        └────────┬─────────┘  │ senior_manager_id│  │ created_at       │
                 │            │ branch_id        │  │ updated_at       │
        ┌────────▼──────────┐ │ role_id          │  └──────────────────┘
        │ role_permissions  │ │ is_active        │
        │───────────────────│ │ created_at       │
        │ role_id (FK)      │ │ updated_at       │
        │ permission_id (FK)│ │ deleted_at       │
        │ created_at        │ └────────┬─────────┘
        └─────────┬─────────┘          │
                  │            ┌───────┴────────┐
                  │            │                │
                  ▼            ▼                ▼
        ┌──────────────────┐  │    ┌──────────────────┐
        │ permissions      │  │    │ departments      │
        │──────────────────│  │    │──────────────────│
        │ id (PK)          │  │    │ id (PK)          │
        │ name             │  │    │ code             │
        │ module_name      │  │    │ name             │
        │ description      │  │    │ description      │
        │ guard_name       │  │    │ manager_id (FK)  │
        │ created_at       │  │    │ is_active        │
        │ updated_at       │  │    │ created_at       │
        │                  │  │    │ updated_at       │
        └──────────────────┘  │    │ deleted_at       │
                              │    └──────────────────┘
                              ▼
                 ┌──────────────────────┐
                 │ designations         │
                 │──────────────────────│
                 │ id (PK)              │
                 │ code                 │
                 │ name                 │
                 │ description          │
                 │ is_active            │
                 │ created_at           │
                 │ updated_at           │
                 │ deleted_at           │
                 └──────────────────────┘
                 
                 ┌──────────────────────┐
                 │ branches             │
                 │──────────────────────│
                 │ id (PK)              │
                 │ code                 │
                 │ name                 │
                 │ city                 │
                 │ state                │
                 │ country              │
                 │ address              │
                 │ is_active            │
                 │ created_at           │
                 │ updated_at           │
                 │ deleted_at           │
                 └──────────────────────┘
```

## Core Tables

### 1. Users Table
```sql
CREATE TABLE users (
    id BIGSERIAL PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    email_verified_at TIMESTAMP NULL,
    password VARCHAR(255) NOT NULL,
    first_name VARCHAR(100) NOT NULL,
    last_name VARCHAR(100) NOT NULL,
    phone VARCHAR(20) NULL,
    avatar_url VARCHAR(500) NULL,
    is_active BOOLEAN DEFAULT true,
    last_login_at TIMESTAMP NULL,
    password_changed_at TIMESTAMP NULL,
    two_factor_secret VARCHAR(255) NULL,
    two_factor_confirmed_at TIMESTAMP NULL,
    remember_token VARCHAR(100) NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP NULL,
    
    CONSTRAINT users_email_unique UNIQUE (email),
    INDEX idx_is_active (is_active),
    INDEX idx_created_at (created_at)
);
```

### 2. Roles Table
```sql
CREATE TABLE roles (
    id BIGSERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    guard_name VARCHAR(100) NOT NULL DEFAULT 'web',
    description TEXT NULL,
    is_system_role BOOLEAN DEFAULT false,
    hierarchy_level INT DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    CONSTRAINT roles_name_unique UNIQUE (name, guard_name),
    INDEX idx_guard_name (guard_name),
    INDEX idx_hierarchy_level (hierarchy_level)
);
```

### 3. Permissions Table
```sql
CREATE TABLE permissions (
    id BIGSERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    module_name VARCHAR(100) NOT NULL,
    permission_type VARCHAR(50) NOT NULL,
    description TEXT NULL,
    guard_name VARCHAR(100) NOT NULL DEFAULT 'web',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    CONSTRAINT permissions_name_unique UNIQUE (name, guard_name),
    INDEX idx_module_name (module_name),
    INDEX idx_permission_type (permission_type)
);

-- Permission Types: view, add, edit, delete, approve, reject, export
```

### 4. Role Permissions Table
```sql
CREATE TABLE role_permissions (
    id BIGSERIAL PRIMARY KEY,
    role_id BIGINT NOT NULL REFERENCES roles(id) ON DELETE CASCADE,
    permission_id BIGINT NOT NULL REFERENCES permissions(id) ON DELETE CASCADE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    CONSTRAINT role_permissions_unique UNIQUE (role_id, permission_id),
    INDEX idx_role_id (role_id),
    INDEX idx_permission_id (permission_id)
);
```

### 5. Departments Table
```sql
CREATE TABLE departments (
    id BIGSERIAL PRIMARY KEY,
    code VARCHAR(50) NOT NULL,
    name VARCHAR(150) NOT NULL,
    description TEXT NULL,
    manager_id BIGINT NULL REFERENCES employees(id),
    budget_limit DECIMAL(15,2) DEFAULT 0,
    budget_period VARCHAR(20) DEFAULT 'monthly',
    is_active BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP NULL,
    
    CONSTRAINT departments_code_unique UNIQUE (code),
    INDEX idx_is_active (is_active),
    INDEX idx_manager_id (manager_id)
);
```

### 6. Designations Table
```sql
CREATE TABLE designations (
    id BIGSERIAL PRIMARY KEY,
    code VARCHAR(50) NOT NULL,
    name VARCHAR(150) NOT NULL,
    description TEXT NULL,
    hierarchy_level INT DEFAULT 0,
    is_manager_role BOOLEAN DEFAULT false,
    is_senior_manager_role BOOLEAN DEFAULT false,
    is_active BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP NULL,
    
    CONSTRAINT designations_code_unique UNIQUE (code),
    INDEX idx_is_active (is_active),
    INDEX idx_hierarchy_level (hierarchy_level)
);
```

### 7. Branches Table
```sql
CREATE TABLE branches (
    id BIGSERIAL PRIMARY KEY,
    code VARCHAR(50) NOT NULL,
    name VARCHAR(150) NOT NULL,
    address TEXT NOT NULL,
    city VARCHAR(100) NOT NULL,
    state VARCHAR(100) NOT NULL,
    country VARCHAR(100) NOT NULL DEFAULT 'India',
    postal_code VARCHAR(20) NULL,
    latitude DECIMAL(10,8) NULL,
    longitude DECIMAL(11,8) NULL,
    phone VARCHAR(20) NULL,
    email VARCHAR(100) NULL,
    is_active BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP NULL,
    
    CONSTRAINT branches_code_unique UNIQUE (code),
    INDEX idx_is_active (is_active),
    INDEX idx_city (city)
);
```

### 8. Employees Table
```sql
CREATE TABLE employees (
    id BIGSERIAL PRIMARY KEY,
    user_id BIGINT NOT NULL UNIQUE REFERENCES users(id) ON DELETE CASCADE,
    employee_code VARCHAR(50) NOT NULL,
    department_id BIGINT NOT NULL REFERENCES departments(id),
    designation_id BIGINT NOT NULL REFERENCES designations(id),
    manager_id BIGINT NULL REFERENCES employees(id),
    senior_manager_id BIGINT NULL REFERENCES employees(id),
    branch_id BIGINT NOT NULL REFERENCES branches(id),
    date_of_joining DATE NOT NULL,
    date_of_exit DATE NULL,
    is_active BOOLEAN DEFAULT true,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP NULL,
    
    CONSTRAINT employees_code_unique UNIQUE (employee_code),
    INDEX idx_department_id (department_id),
    INDEX idx_manager_id (manager_id),
    INDEX idx_senior_manager_id (senior_manager_id),
    INDEX idx_is_active (is_active)
);
```

### 9. Expense Categories Table
```sql
CREATE TABLE expense_categories (
    id BIGSERIAL PRIMARY KEY,
    code VARCHAR(50) NOT NULL,
    name VARCHAR(150) NOT NULL,
    description TEXT NULL,
    icon VARCHAR(100) NULL,
    color VARCHAR(20) NULL,
    requires_attachment BOOLEAN DEFAULT false,
    requires_client_name BOOLEAN DEFAULT false,
    is_active BOOLEAN DEFAULT true,
    sort_order INT DEFAULT 0,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP NULL,
    
    CONSTRAINT expense_categories_code_unique UNIQUE (code),
    INDEX idx_is_active (is_active)
);
```

### 10. Expenses Table
```sql
CREATE TABLE expenses (
    id BIGSERIAL PRIMARY KEY,
    expense_code VARCHAR(50) NOT NULL,
    employee_id BIGINT NOT NULL REFERENCES employees(id),
    expense_category_id BIGINT NOT NULL REFERENCES expense_categories(id),
    expense_date DATE NOT NULL,
    amount DECIMAL(12,2) NOT NULL,
    description TEXT NULL,
    client_name VARCHAR(200) NULL,
    manager_id BIGINT NULL REFERENCES employees(id),
    senior_manager_id BIGINT NULL REFERENCES employees(id),
    
    status VARCHAR(50) DEFAULT 'draft',
    approved_at TIMESTAMP NULL,
    approved_by BIGINT NULL REFERENCES employees(id),
    rejection_reason TEXT NULL,
    
    is_travel_expense BOOLEAN DEFAULT false,
    currency VARCHAR(10) DEFAULT 'INR',
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    submitted_at TIMESTAMP NULL,
    deleted_at TIMESTAMP NULL,
    
    CONSTRAINT expenses_code_unique UNIQUE (expense_code),
    INDEX idx_employee_id (employee_id),
    INDEX idx_status (status),
    INDEX idx_expense_date (expense_date),
    INDEX idx_created_at (created_at)
);

-- Status: draft, submitted, manager_approved, senior_manager_approved, final_approved, rejected, sent_back
```

### 11. Travel Expenses Table
```sql
CREATE TABLE travel_expenses (
    id BIGSERIAL PRIMARY KEY,
    expense_id BIGINT NOT NULL UNIQUE REFERENCES expenses(id) ON DELETE CASCADE,
    vehicle_type VARCHAR(50) NOT NULL,
    source_location VARCHAR(255) NOT NULL,
    source_latitude DECIMAL(10,8) NOT NULL,
    source_longitude DECIMAL(11,8) NOT NULL,
    
    total_km DECIMAL(10,2) NOT NULL,
    google_maps_km DECIMAL(10,2) NOT NULL,
    extra_km DECIMAL(10,2) DEFAULT 0,
    extra_km_reason TEXT NULL,
    
    rate_per_km DECIMAL(10,4) NOT NULL,
    calculated_amount DECIMAL(12,2) NOT NULL,
    
    route_data JSON NULL,
    polyline TEXT NULL,
    journey_duration VARCHAR(50) NULL,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    INDEX idx_expense_id (expense_id),
    INDEX idx_vehicle_type (vehicle_type)
);
```

### 12. Travel Destinations Table
```sql
CREATE TABLE travel_destinations (
    id BIGSERIAL PRIMARY KEY,
    travel_expense_id BIGINT NOT NULL REFERENCES travel_expenses(id) ON DELETE CASCADE,
    location VARCHAR(255) NOT NULL,
    latitude DECIMAL(10,8) NOT NULL,
    longitude DECIMAL(11,8) NOT NULL,
    sequence_order INT NOT NULL,
    arrival_time TIME NULL,
    departure_time TIME NULL,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    INDEX idx_travel_expense_id (travel_expense_id),
    INDEX idx_sequence_order (sequence_order)
);
```

### 13. KM Rates Table
```sql
CREATE TABLE km_rates (
    id BIGSERIAL PRIMARY KEY,
    vehicle_type VARCHAR(50) NOT NULL,
    rate_per_km DECIMAL(10,4) NOT NULL,
    effective_from DATE NOT NULL,
    effective_to DATE NULL,
    description TEXT NULL,
    is_active BOOLEAN DEFAULT true,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    CONSTRAINT km_rates_unique UNIQUE (vehicle_type, effective_from),
    INDEX idx_vehicle_type (vehicle_type),
    INDEX idx_effective_from (effective_from),
    INDEX idx_is_active (is_active)
);
```

### 14. Expense Attachments Table
```sql
CREATE TABLE expense_attachments (
    id BIGSERIAL PRIMARY KEY,
    expense_id BIGINT NOT NULL REFERENCES expenses(id) ON DELETE CASCADE,
    file_name VARCHAR(255) NOT NULL,
    file_type VARCHAR(50) NOT NULL,
    file_size BIGINT NOT NULL,
    file_path VARCHAR(500) NOT NULL,
    s3_url VARCHAR(500) NULL,
    uploaded_by BIGINT NOT NULL REFERENCES users(id),
    is_bill BOOLEAN DEFAULT false,
    ocr_text TEXT NULL,
    ocr_processed_at TIMESTAMP NULL,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    INDEX idx_expense_id (expense_id),
    INDEX idx_uploaded_by (uploaded_by)
);
```

### 15. Expense Approvals Table
```sql
CREATE TABLE expense_approvals (
    id BIGSERIAL PRIMARY KEY,
    expense_id BIGINT NOT NULL REFERENCES expenses(id) ON DELETE CASCADE,
    approval_level INT NOT NULL,
    approver_id BIGINT NOT NULL REFERENCES employees(id),
    approval_type VARCHAR(50) NOT NULL,
    status VARCHAR(50) DEFAULT 'pending',
    comments TEXT NULL,
    rejection_reason TEXT NULL,
    approved_at TIMESTAMP NULL,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    CONSTRAINT expense_approvals_unique UNIQUE (expense_id, approval_level),
    INDEX idx_expense_id (expense_id),
    INDEX idx_approver_id (approver_id),
    INDEX idx_status (status)
);

-- Status: pending, approved, rejected, sent_back
-- Approval Type: manager, senior_manager, final
```

### 16. Budget Limits Table
```sql
CREATE TABLE budget_limits (
    id BIGSERIAL PRIMARY KEY,
    department_id BIGINT NULL REFERENCES departments(id),
    employee_id BIGINT NULL REFERENCES employees(id),
    expense_category_id BIGINT NULL REFERENCES expense_categories(id),
    budget_amount DECIMAL(15,2) NOT NULL,
    budget_period VARCHAR(20) NOT NULL,
    fiscal_year INT NOT NULL,
    is_active BOOLEAN DEFAULT true,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    INDEX idx_department_id (department_id),
    INDEX idx_employee_id (employee_id),
    INDEX idx_fiscal_year (fiscal_year)
);

-- Budget Period: monthly, quarterly, yearly
```

### 17. Expense Policies Table
```sql
CREATE TABLE expense_policies (
    id BIGSERIAL PRIMARY KEY,
    policy_code VARCHAR(50) NOT NULL,
    policy_name VARCHAR(150) NOT NULL,
    description TEXT NULL,
    
    max_limit_per_expense DECIMAL(12,2) NULL,
    max_limit_per_day DECIMAL(12,2) NULL,
    max_limit_per_month DECIMAL(12,2) NULL,
    
    requires_approval BOOLEAN DEFAULT true,
    requires_receipt BOOLEAN DEFAULT true,
    max_submission_days INT DEFAULT 30,
    
    is_active BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP NULL,
    
    CONSTRAINT expense_policies_code_unique UNIQUE (policy_code),
    INDEX idx_is_active (is_active)
);
```

### 18. Escalation Rules Table
```sql
CREATE TABLE escalation_rules (
    id BIGSERIAL PRIMARY KEY,
    rule_name VARCHAR(150) NOT NULL,
    condition_type VARCHAR(50) NOT NULL,
    condition_value VARCHAR(255) NOT NULL,
    escalation_to_role_id BIGINT NOT NULL REFERENCES roles(id),
    escalation_days INT DEFAULT 5,
    is_active BOOLEAN DEFAULT true,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    INDEX idx_is_active (is_active)
);

-- Condition Type: pending_days, amount_threshold, department
```

### 19. Audit Logs Table
```sql
CREATE TABLE audit_logs (
    id BIGSERIAL PRIMARY KEY,
    user_id BIGINT NOT NULL REFERENCES users(id),
    model_name VARCHAR(150) NOT NULL,
    model_id BIGINT NOT NULL,
    action VARCHAR(50) NOT NULL,
    old_values JSON NULL,
    new_values JSON NULL,
    changes JSON NULL,
    ip_address VARCHAR(45) NULL,
    user_agent TEXT NULL,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    INDEX idx_user_id (user_id),
    INDEX idx_model_name (model_name),
    INDEX idx_action (action),
    INDEX idx_created_at (created_at)
);

-- Action: created, updated, deleted, viewed, approved, rejected
```

### 20. Activity Logs Table
```sql
CREATE TABLE activity_logs (
    id BIGSERIAL PRIMARY KEY,
    user_id BIGINT NOT NULL REFERENCES users(id),
    activity_type VARCHAR(100) NOT NULL,
    description TEXT NULL,
    related_model VARCHAR(150) NULL,
    related_model_id BIGINT NULL,
    metadata JSON NULL,
    ip_address VARCHAR(45) NULL,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    INDEX idx_user_id (user_id),
    INDEX idx_activity_type (activity_type),
    INDEX idx_created_at (created_at)
);
```

### 21. Notifications Table
```sql
CREATE TABLE notifications (
    id BIGSERIAL PRIMARY KEY,
    user_id BIGINT NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    type VARCHAR(100) NOT NULL,
    title VARCHAR(255) NOT NULL,
    message TEXT NOT NULL,
    related_model VARCHAR(150) NULL,
    related_model_id BIGINT NULL,
    
    read_at TIMESTAMP NULL,
    action_url VARCHAR(500) NULL,
    action_data JSON NULL,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    INDEX idx_user_id (user_id),
    INDEX idx_read_at (read_at),
    INDEX idx_created_at (created_at)
);
```

### 22. Notification Preferences Table
```sql
CREATE TABLE notification_preferences (
    id BIGSERIAL PRIMARY KEY,
    user_id BIGINT NOT NULL UNIQUE REFERENCES users(id) ON DELETE CASCADE,
    email_on_expense_approved BOOLEAN DEFAULT true,
    email_on_expense_rejected BOOLEAN DEFAULT true,
    email_on_approval_request BOOLEAN DEFAULT true,
    sms_on_approval_request BOOLEAN DEFAULT false,
    push_on_approval_request BOOLEAN DEFAULT true,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### 23. RBAC Page Access Table
```sql
CREATE TABLE rbac_page_access (
    id BIGSERIAL PRIMARY KEY,
    role_id BIGINT NOT NULL REFERENCES roles(id) ON DELETE CASCADE,
    page_route VARCHAR(255) NOT NULL,
    can_access BOOLEAN DEFAULT true,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    CONSTRAINT rbac_page_access_unique UNIQUE (role_id, page_route),
    INDEX idx_role_id (role_id)
);
```

### 24. RBAC Menu Access Table
```sql
CREATE TABLE rbac_menu_access (
    id BIGSERIAL PRIMARY KEY,
    role_id BIGINT NOT NULL REFERENCES roles(id) ON DELETE CASCADE,
    menu_item_key VARCHAR(100) NOT NULL,
    is_visible BOOLEAN DEFAULT true,
    sequence_order INT DEFAULT 0,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    CONSTRAINT rbac_menu_access_unique UNIQUE (role_id, menu_item_key),
    INDEX idx_role_id (role_id)
);
```

### 25. RBAC Module Access Table
```sql
CREATE TABLE rbac_module_access (
    id BIGSERIAL PRIMARY KEY,
    role_id BIGINT NOT NULL REFERENCES roles(id) ON DELETE CASCADE,
    module_name VARCHAR(100) NOT NULL,
    can_access BOOLEAN DEFAULT true,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    CONSTRAINT rbac_module_access_unique UNIQUE (role_id, module_name),
    INDEX idx_role_id (role_id)
);
```

## Indexing Strategy

### Primary Indexes
- All primary keys are indexed by default
- Foreign keys are indexed for join performance
- Status columns are indexed for filtering
- Date columns are indexed for range queries
- User IDs are indexed for authorization checks

### Composite Indexes (Performance Optimization)
```sql
CREATE INDEX idx_expenses_employee_date ON expenses(employee_id, expense_date);
CREATE INDEX idx_expenses_status_date ON expenses(status, created_at);
CREATE INDEX idx_expense_approvals_status ON expense_approvals(status, created_at);
CREATE INDEX idx_audit_logs_model ON audit_logs(model_name, model_id);
```

## Views for Reporting

### Employee Expense Summary View
```sql
CREATE VIEW v_employee_expense_summary AS
SELECT
    e.id as employee_id,
    CONCAT(u.first_name, ' ', u.last_name) as employee_name,
    d.name as department,
    COUNT(ex.id) as total_expenses,
    COALESCE(SUM(ex.amount), 0) as total_amount,
    COUNT(CASE WHEN ex.status = 'final_approved' THEN 1 END) as approved_count,
    COUNT(CASE WHEN ex.status = 'rejected' THEN 1 END) as rejected_count,
    COUNT(CASE WHEN ex.status IN ('draft', 'submitted') THEN 1 END) as pending_count
FROM employees e
JOIN users u ON e.user_id = u.id
JOIN departments d ON e.department_id = d.id
LEFT JOIN expenses ex ON e.id = ex.employee_id
GROUP BY e.id, u.first_name, u.last_name, d.name;
```

## Migrations Order

1. Users
2. Roles
3. Permissions
4. Role Permissions
5. Departments
6. Designations
7. Branches
8. Employees
9. Expense Categories
10. Expenses
11. Travel Expenses
12. Travel Destinations
13. KM Rates
14. Expense Attachments
15. Expense Approvals
16. Budget Limits
17. Expense Policies
18. Escalation Rules
19. Audit Logs
20. Activity Logs
21. Notifications
22. Notification Preferences
23. RBAC Page Access
24. RBAC Menu Access
25. RBAC Module Access
