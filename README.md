# Enterprise Expense Management System

A comprehensive, production-ready expense management platform built with Laravel, designed for multi-department enterprises with advanced role-based access control, intelligent approval workflows, and real-time travel expense tracking.

## System Overview

### Core Features
- **Advanced RBAC System**: Granular control over page access, menu access, module access, and permissions
- **Multi-Department Support**: Sales, Marketing, HR, Finance, Operations, and custom departments
- **Employee Management**: Complete employee lifecycle management with hierarchical reporting structure
- **Expense Management**: Multi-category expense tracking with attachments and OCR support
- **Travel Expenses**: Google Maps API integration for automatic distance calculation with multi-destination support
- **Approval Workflows**: Multi-level approval process (Employee → Manager → Senior Manager → Final Approved)
- **Comprehensive Dashboards**: Employee, Manager, and Admin dashboards with real-time metrics
- **Advanced Reporting**: Employee-wise, Department-wise, Category-wise, Manager-wise, Monthly/Yearly, Travel KM reports
- **Audit & Compliance**: Complete audit logs, activity tracking, and compliance management
- **Mobile & Web**: Responsive UI with dedicated mobile app support
- **Notifications**: Email and push notifications for all key events
- **Budget Management**: Department and employee-level budget limits with tracking

### Technology Stack
- **Backend**: Laravel 11, PHP 8.2+
- **Frontend**: Blade Templates, Vue.js 3, Inertia.js
- **Database**: PostgreSQL 14+
- **Authentication**: JWT + Laravel Sanctum
- **Storage**: AWS S3 for file uploads
- **APIs**: Google Maps, Twilio (SMS), SendGrid (Email)
- **Real-time**: Laravel Reverb for WebSocket support
- **Queue**: Redis for background jobs
- **Cache**: Redis with predis
- **Monitoring**: Sentry for error tracking

## Project Structure

```
expense-management-system/
├── app/
│   ├── Models/
│   ├── Http/
│   │   ├── Controllers/
│   │   ├── Requests/
│   │   └── Resources/
│   ├── Services/
│   ├── Policies/
│   ├── Jobs/
│   ├── Events/
│   ├── Listeners/
│   ├── Mail/
│   ├── Notifications/
│   └── Traits/
├── database/
│   ├── migrations/
│   ├── seeders/
│   └── factories/
├── routes/
├── resources/
├── storage/
├── tests/
├── config/
└── docker/
```

## Quick Start

```bash
# Clone the repository
git clone https://github.com/testing23092003-rgb/expense-management-system.git
cd expense-management-system

# Copy environment file
cp .env.example .env

# Install dependencies
composer install
npm install

# Generate application key
php artisan key:generate

# Run migrations and seeders
php artisan migrate --seed

# Build frontend assets
npm run build

# Start the development server
php artisan serve
```

## Documentation

- [System Architecture](./docs/01_SYSTEM_ARCHITECTURE.md)
- [Database Schema](./docs/02_DATABASE_SCHEMA.md)
- [API Documentation](./docs/03_API_DOCUMENTATION.md)
- [Installation Guide](./docs/04_INSTALLATION.md)
- [Development Guide](./docs/05_DEVELOPMENT_GUIDE.md)
- [RBAC & Permissions](./docs/06_RBAC_GUIDE.md)
- [User Flows](./docs/07_USER_FLOWS.md)
- [UI Screen Specifications](./docs/08_UI_SCREENS.md)
- [Deployment Guide](./docs/09_DEPLOYMENT_GUIDE.md)
- [Testing Guide](./docs/10_TESTING_GUIDE.md)

## Key Roles

1. **Super Admin**: Full system control
2. **Admin**: Department/Organization management with RBAC control
3. **Senior Manager**: Approval authority, team oversight
4. **Manager**: Employee management, expense approval
5. **Team Lead**: Team monitoring, limited approvals
6. **Employee**: Expense submission

## Features Roadmap

### Phase 1 (MVP) - Months 1-2
- ✅ User Authentication & RBAC
- ✅ Employee Management
- ✅ Expense Categories Master
- ✅ Basic Expense Submission
- ✅ Simple Approval Workflow

### Phase 2 - Months 2-3
- ✅ Travel Expense with Google Maps
- ✅ Advanced RBAC Controls
- ✅ Dashboards
- ✅ Basic Reporting

### Phase 3 - Months 3-4
- ✅ OCR Bill Scanning
- ✅ Advanced Reporting
- ✅ Budget Management
- ✅ Email & Push Notifications
- ✅ Mobile App Support

### Phase 4 - Months 4-5
- ✅ Audit & Compliance
- ✅ Auto Escalation Rules
- ✅ Advanced Analytics
- ✅ Performance Optimization

### Phase 5 - Month 5+
- ✅ Mobile Native Apps (iOS/Android)
- ✅ Advanced Features & Enhancements
- ✅ Integration with third-party systems

## License

Proprietary - All rights reserved.

## Support

For support, contact: dev-team@company.com
