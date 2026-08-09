# SnapFun Studio ERP System

A comprehensive Enterprise Resource Planning (ERP) system for SnapFun Studio, a photo booth and event services company. This system manages assets, inventory, events, procurement, customers, and user management with full audit trail capabilities.

## Table of Contents

1. [System Overview](#system-overview)
2. [Prerequisites](#prerequisites)
3. [Installation Guide](#installation-guide)
4. [Configuration](#configuration)
5. [Running the Application](#running-the-application)
6. [Database Schema](#database-schema)
7. [API Documentation](#api-documentation)
8. [User Roles and Permissions](#user-roles-and-permissions)
9. [Features](#features)
10. [Troubleshooting](#troubleshooting)
11. [Deployment](#deployment)

## System Overview

This ERP system consists of:
- **Frontend**: React.js with Vite, Tailwind CSS, and Lucide icons
- **Backend**: Node.js with Express.js
- **Database**: MySQL
- **Authentication**: JWT-based with email verification
- **File Upload**: Multer for image attachments

### Project Structure

```
capstone/
├── server/                 # Backend API server
│   ├── routes/            # API route handlers
│   ├── middleware/        # Authentication middleware
│   ├── uploads/           # File upload directory
│   ├── server.js          # Main server entry point
│   └── package.json       # Backend dependencies
├── src/                   # Frontend React application
│   ├── pages/            # Page components
│   ├── components/       # Reusable components
│   ├── context/         # React context providers
│   └── utils/            # Utility functions
├── package.json          # Frontend dependencies
└── README.md            # This file
```

## Prerequisites

Before installing the system, ensure you have the following installed:

### Required Software
- **Node.js**: v18.0.0 or higher ([Download](https://nodejs.org/))
- **MySQL**: v8.0.0 or higher ([Download](https://dev.mysql.com/downloads/mysql/))
- **npm**: Comes with Node.js
- **Git**: For version control (optional but recommended)

### System Requirements
- **Operating System**: Windows, macOS, or Linux
- **RAM**: Minimum 4GB (8GB recommended)
- **Disk Space**: Minimum 2GB free space

## Installation Guide

### Step 1: Clone or Extract the Project

If you have the project as a zip file:
1. Extract the zip file to your desired location
2. Navigate to the project directory

If using Git:
```bash
git clone <repository-url>
cd capstone
```

### Step 2: Install Backend Dependencies

Navigate to the server directory and install dependencies:

```bash
cd server
npm install
```

**Note for Windows Users**: If you encounter PowerShell execution policy errors, use one of these methods:

**Option A: Enable PowerShell Script Execution**
```powershell
# Run as Administrator
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
npm install
```

**Option B: Use Command Prompt**
```cmd
cd "c:\path\to\capstone\server"
npm install
```

**Option C: Use Node.js Command Prompt**
```cmd
cd "c:\path\to\capstone\server"
npm install
```

### Step 3: Install Frontend Dependencies

Navigate back to the root directory and install frontend dependencies:

```bash
cd ..
npm install
```

### Step 4: Setup MySQL Database

#### Option A: Using MySQL Command Line
```bash
mysql -u root -p
```

Then run the following SQL commands:
```sql
CREATE DATABASE snapfun_erp;
USE snapfun_erp;
-- The tables will be automatically created when you start the server
```

#### Option B: Using MySQL Workbench/phpMyAdmin
1. Open MySQL Workbench or phpMyAdmin
2. Create a new database named `snapfun_erp`
3. No tables need to be created manually - they will be auto-generated

### Step 5: Configure Environment Variables

Create a `.env` file in the `server` directory with the following configuration:

```env
PORT=5000
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=your_mysql_password
DB_NAME=snapfun_erp
```

**Important**: Replace `your_mysql_password` with your actual MySQL root password.

### Step 6: Configure Frontend Environment

The frontend uses the API base URL defined in `vite.config.js`. Ensure it matches your backend server:

```javascript
// vite.config.js
export default defineConfig({
  server: {
    proxy: {
      '/api': {
        target: 'http://localhost:5000',
        changeOrigin: true
      }
    }
  }
})
```

## Configuration

### Database Configuration

The database schema is automatically created when you start the server for the first time. The following tables will be created:

- `users` - User accounts and authentication
- `procurement_requests` - Procurement requests
- `procurement_request_items` - Items in procurement requests
- `events` - Events and bookings
- `calendar_activities` - Calendar activities
- `assets` - Asset management
- `customers` - Customer information
- `customer_visits` - Customer visit records
- `inventory` - Inventory management
- `categories` - Item categories
- `uom` - Unit of measurements
- `vendors` - Vendor information
- `promotions` - Promotional campaigns

### Email Configuration (Optional)

If you want to enable email verification, configure the email settings in `server/server.js`. Currently, the system uses console logging for verification codes (development mode).

## Running the Application

### Development Mode

#### Terminal 1: Start Backend Server
```bash
cd server
npm run dev
```

The backend server will start on `http://localhost:5000`

#### Terminal 2: Start Frontend Development Server
```bash
cd ..
npm run dev
```

The frontend will start on `http://localhost:5173`

### Production Mode

#### Build Frontend
```bash
npm run build
```

#### Start Backend Server
```bash
cd server
npm start
```

The built frontend will be served from the `dist` directory.

## Database Schema

### Key Tables and Their Purpose

#### Users Table
- Stores user accounts with roles (admin, user)
- Includes audit trail fields: `created_by`, `updated_by`
- Password hashing with bcrypt

#### Procurement Tables
- `procurement_requests`: Main procurement request records
- `procurement_request_items`: Individual items in each request
- Status workflow: Draft → Waiting Approval → Approved → Received → Rejected

#### Events Table
- Event and booth booking management
- Asset assignment with stock validation
- Date range validation (start_date <= end_date)
- Custom pricing for various services

#### Assets Table
- Asset tracking with QR barcode generation
- Stock quantity management
- Photo attachment support

#### Inventory Table
- Inventory item management
- Stock quantity and minimum stock alerts
- Category and UOM references

## API Documentation

### Authentication Endpoints

#### Register User
- **POST** `/api/auth/register`
- **Body:**
  ```json
  {
    "full_name": "John Doe",
    "email": "john@example.com",
    "password": "Password123!",
    "role": "user",
    "unique_code": "ITADMINREGISTERATION789"
  }
  ```
- **Note**: `unique_code` is required only for admin registration

#### Verify Email
- **POST** `/api/auth/verify-email`
- **Body:**
  ```json
  {
    "email": "john@example.com",
    "verification_code": "12345"
  }
  ```

#### Login
- **POST** `/api/auth/login`
- **Body:**
  ```json
  {
    "email": "john@example.com",
    "password": "Password123!",
    "role": "user"
  }
  ```

### Main API Endpoints

All endpoints (except auth) require JWT authentication in the header:
```
Authorization: Bearer <token>
```

- **GET/POST** `/api/assets` - Asset management
- **GET/POST/PUT/DELETE** `/api/events` - Events management
- **GET/POST/PUT/DELETE** `/api/procurement` - Procurement management
- **GET/POST/PUT/DELETE** `/api/customers` - Customer management
- **GET/POST/PUT/DELETE** `/api/inventory` - Inventory management
- **GET/POST/PUT/DELETE** `/api/users` - User management (admin only)
- **GET** `/api/dashboard` - Dashboard statistics

## User Roles and Permissions

### Admin Role
- Full access to all modules
- Can create, edit, and delete users
- Can approve procurement requests
- Can manage all system data
- **Cannot delete users who have created records in the system**

### User Role
- Can create and manage assets, inventory, events
- Can create procurement requests
- Can view and manage customers
- Cannot delete users
- Cannot approve procurement requests (requires admin)

## Features

### Core Modules

#### 1. Dashboard
- Overview of system statistics
- Quick access to main modules
- Auto-draft procurement from low stock alerts

#### 2. Assets Management
- Create, view, edit, and delete assets
- QR barcode generation and scanning
- Asset usage tracking
- Photo attachment support
- Stock quantity management

#### 3. Inventory Management
- Inventory item management
- Stock quantity and minimum stock alerts
- Category and UOM management
- Auto-draft procurement for low stock items

#### 4. Events & Booths
- Event creation and management
- Asset assignment with stock validation
- Date range validation
- Custom pricing for various services
- Customer association
- Status tracking (upcoming, in_progress, completed, cancelled)

#### 5. Procurement
- Procurement request creation
- Status workflow (Draft → Waiting Approval → Approved → Received → Rejected)
- Item-level cost management
- Attachment support
- Audit trail (created_by, updated_by)

#### 6. Customers
- Customer information management
- Visit tracking
- Customer association with events

#### 7. User Management
- User creation and management
- Role assignment
- Status management (active/inactive)
- Deletion restriction for users with records
- Audit trail

### Security Features

- Password requirements (8-16 chars, letters, numbers, symbols)
- JWT-based authentication
- Email verification system
- Input validation for negative numbers
- Stock validation prevents over-allocation
- Date range validation
- QR code scan validation

### Audit Trail

All major tables include:
- `created_by` - User who created the record
- `updated_by` - User who last updated the record
- `created_at` - Timestamp of creation
- `updated_at` - Timestamp of last update

## Troubleshooting

### Common Issues

#### 1. Database Connection Error
**Problem**: Cannot connect to MySQL database
**Solution**:
- Verify MySQL is running
- Check `.env` file credentials
- Ensure database `snapfun_erp` exists
- Check firewall settings

#### 2. Port Already in Use
**Problem**: Port 5000 or 5173 already in use
**Solution**:
- Change PORT in `.env` file
- Or kill the process using the port:
  ```bash
  # Windows
  netstat -ano | findstr :5000
  taskkill /PID <PID> /F
  ```

#### 3. npm Install Fails
**Problem**: npm install command fails
**Solution**:
- Clear npm cache: `npm cache clean --force`
- Delete `node_modules` folder and `package-lock.json`
- Run npm install again
- Try using a different terminal (cmd instead of PowerShell)

#### 4. Frontend Cannot Connect to Backend
**Problem**: API calls fail with connection error
**Solution**:
- Verify backend server is running
- Check API base URL in `vite.config.js`
- Ensure CORS is properly configured
- Check browser console for specific errors

#### 5. File Upload Fails
**Problem**: Image upload not working
**Solution**:
- Ensure `uploads` directory exists in server folder
- Check file size limits in Multer configuration
- Verify directory write permissions

## Deployment

### Production Deployment Steps

#### 1. Build Frontend
```bash
npm run build
```

#### 2. Configure Production Environment
Update `.env` file for production:
```env
PORT=5000
DB_HOST=your_production_db_host
DB_USER=your_production_db_user
DB_PASSWORD=your_production_db_password
DB_NAME=snapfun_erp
NODE_ENV=production
```

#### 3. Serve Frontend with Backend
The backend is configured to serve the built frontend from the `dist` directory.

#### 4. Use Process Manager (Recommended)
Install PM2 for process management:
```bash
npm install -g pm2
pm2 start server/server.js --name snapfun-erp
pm2 save
pm2 startup
```

#### 5. Configure Reverse Proxy (Nginx/Apache)
Set up a reverse proxy to serve the application on port 80/443.

### Security Considerations for Production

- Use strong database passwords
- Enable HTTPS
- Configure firewall rules
- Regular database backups
- Monitor logs for suspicious activity
- Keep dependencies updated
- Use environment variables for sensitive data

## Support and Maintenance

### Regular Maintenance Tasks
- Database backups (daily recommended)
- Log file rotation
- Dependency updates
- Security patches

### Contact Information
For technical support or questions about this system, contact the development team.

## License

This system is proprietary software for SnapFun Studio. All rights reserved.

## Version History

- **v1.0.0** - Initial release with core ERP functionality
  - User management with authentication
  - Assets and inventory management
  - Events and procurement modules
  - Audit trail implementation
  - QR code generation and scanning
  - Security validations and input restrictions
