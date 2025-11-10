# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Project Overview

This is a full-stack CRUD application with three main components:
- **Backend**: Laravel 12 API with SOLID principles and Repository pattern
- **Frontend React**: Modern React 19 with TypeScript and Tailwind CSS
- **Frontend Angular**: Angular 17 with standalone components and Tailwind CSS

All components work together through a RESTful API at `/api/v1/products`.

---

## Quick Start Commands

### One-Time Setup (Windows PowerShell)

```powershell
# Backend setup
cd backend
composer install
php artisan key:generate
php artisan migrate
cd ..

# React setup
cd frontend-react
npm install
cd ..

# Angular setup
cd frontend-angular
npm install
cd ..
```

### Running the Application (3 Terminal Windows)

**Terminal 1 - Backend API:**
```bash
cd backend
php artisan serve
# Runs on http://localhost:8000
```

**Terminal 2 - React Frontend (Optional):**
```bash
cd frontend-react
npm start
# Runs on http://localhost:3000
```

**Terminal 3 - Angular Frontend (Optional):**
```bash
cd frontend-angular
ng serve
# Runs on http://localhost:4200
```

---

## Backend (Laravel) Commands

Navigate to `backend/` directory before running these commands.

### Installation & Setup
```bash
# Install PHP dependencies
composer install

# Generate application key
php artisan key:generate

# Create SQLite database file
touch database/database.sqlite

# Run database migrations
php artisan migrate

# Seed database with sample data (if seeders exist)
php artisan db:seed
```

### Development
```bash
# Start development server (port 8000)
php artisan serve

# Start on different port
php artisan serve --port=8001

# Run all development services (Laravel, Queue, Logs, Vite)
composer run dev
```

### Database
```bash
# Create new migration
php artisan make:migration create_table_name

# Run pending migrations
php artisan migrate

# Rollback last migration
php artisan migrate:rollback

# Reset all migrations
php artisan migrate:reset

# Reset and re-run migrations
php artisan migrate:refresh

# Reset and seed
php artisan migrate:refresh --seed
```

### Testing
```bash
# Run all tests
composer run test

# Run specific test file
php artisan test tests/Feature/ProductControllerTest.php

# Run with coverage
php artisan test --coverage
```

### Code Quality
```bash
# Lint PHP code with Pint
php artisan pint

# Lint specific file
php artisan pint app/Services/ProductService.php

# Check without fixing
php artisan pint --test
```

### Utilities
```bash
# Access Laravel Tinker shell (interactive)
php artisan tinker

# Clear all caches
php artisan cache:clear
php artisan config:clear
php artisan route:clear
php artisan view:clear

# View all routes
php artisan route:list

# Generate API documentation
php artisan scribe:generate
```

### Build
```bash
# Build assets for production
npm run build

# Watch assets during development
npm run dev
```

---

## Frontend React Commands

Navigate to `frontend-react/` directory before running these commands.

### Installation & Setup
```bash
# Install dependencies
npm install

# Install with exact versions
npm ci
```

### Development
```bash
# Start development server (port 3000)
npm start

# Start on different port (Windows)
set PORT=3001 && npm start

# Start on different port (macOS/Linux)
PORT=3001 npm start

# Watch for file changes and rebuild
npm run build -- --watch
```

### Building & Production
```bash
# Build for production
npm run build
# Output: build/ directory ready for deployment

# Analyze bundle size
npm run build -- --stats
```

### Testing
```bash
# Run tests in interactive mode
npm test

# Run tests once
npm test -- --watchAll=false

# Run tests with coverage
npm test -- --coverage --watchAll=false

# Run specific test file
npm test ProductForm
```

### Configuration
API endpoint is configured in `src/services/api.ts`:
```typescript
const API_URL = 'http://localhost:8000/api/v1';
```

Change this if your backend runs on a different URL.

---

## Frontend Angular Commands

Navigate to `frontend-angular/` directory before running these commands.

### Installation & Setup
```bash
# Install dependencies
npm install

# Install with exact versions
npm ci
```

### Development
```bash
# Start development server (port 4200)
ng serve

# Start with open browser
ng serve --open

# Start on different port
ng serve --port 4201

# Start with polling (useful on some file systems)
ng serve --poll=2000
```

### Building & Production
```bash
# Build for production
ng build --configuration production
# Output: dist/frontend-angular

# Build with optimization
ng build --configuration production --optimization

# Build for serving (includes source maps)
ng build
```

### Testing
```bash
# Run tests (Karma + Jasmine)
ng test

# Run tests once
ng test --watch=false

# Run with code coverage
ng test --code-coverage

# Run specific test file
ng test --include='**/product.service.spec.ts'
```

### Linting
```bash
# Lint code
ng lint

# Lint and fix
ng lint --fix
```

### Code Generation
```bash
# Generate new component
ng generate component components/product-form

# Generate service
ng generate service services/product

# Generate module
ng generate module modules/products

# Short form
ng g c components/product-list
ng g s services/api
```

### Configuration
API endpoint is configured in `src/app/services/product.service.ts`:
```typescript
private apiUrl = 'http://localhost:8000/api/v1/products';
```

---

## Environment Configuration

### Backend (.env)
```bash
# Database (default is SQLite)
DB_CONNECTION=sqlite
DB_DATABASE=database/database.sqlite

# For MySQL
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=laravel_crud
DB_USERNAME=root
DB_PASSWORD=password

# For PostgreSQL
DB_CONNECTION=pgsql
DB_HOST=127.0.0.1
DB_PORT=5432
DB_DATABASE=laravel_crud
DB_USERNAME=postgres
DB_PASSWORD=password

# CORS - Allow frontend origins
FRONTEND_URLS=http://localhost:3000,http://localhost:4200

# App settings
APP_ENV=local
APP_DEBUG=true
APP_URL=http://localhost:8000
```

### Frontend Configuration
Both React and Angular need the correct backend API URL:

**React** (`src/services/api.ts`):
```typescript
const API_URL = 'http://localhost:8000/api/v1';
```

**Angular** (`src/app/services/product.service.ts`):
```typescript
private apiUrl = 'http://localhost:8000/api/v1/products';
```

---

## Architecture & Code Structure

### Backend Architecture (Laravel)

**Request Flow:**
```
HTTP Request
    ↓
Route (routes/api.php)
    ↓
Controller (app/Http/Controllers/Api/V1/ProductController.php)
    ↓
Form Request Validation (app/Http/Requests/Api/V1/*)
    ↓
Service Layer (app/Services/ProductService.php)
    ↓
Repository Layer (app/Repositories/ProductRepository.php)
    ↓
Eloquent Model (app/Models/Product.php)
    ↓
Database (SQLite/MySQL/PostgreSQL)
    ↓
Response (app/Http/Resources/ProductResource.php)
    ↓
JSON Response
```

**Key Directories:**
- `app/Http/Controllers/Api/V1/` - API endpoint controllers
- `app/Http/Requests/Api/V1/` - Form request validation classes
- `app/Http/Resources/` - Response transformation resources
- `app/Services/` - Business logic (ProductService)
- `app/Repositories/` - Data access layer (ProductRepository)
- `app/Interfaces/` - Contracts for dependency injection
- `app/Models/` - Eloquent models (Product, User)
- `routes/api.php` - API route definitions
- `database/migrations/` - Schema definitions

**SOLID Principles Implementation:**
- **Single Responsibility**: ProductController handles HTTP, ProductService handles logic, ProductRepository handles data access
- **Open/Closed**: Interface-based repository allows alternative implementations without changing controller
- **Liskov Substitution**: ProductRepositoryInterface can be swapped for different implementations
- **Interface Segregation**: ProductRepositoryInterface defines only necessary methods
- **Dependency Inversion**: Controller depends on ProductService, Service depends on ProductRepositoryInterface

### Frontend Architecture

#### React Structure
```
src/
├── components/
│   ├── ProductForm.tsx       - Create/Edit form component
│   └── ProductList.tsx       - Table display component
├── services/
│   └── api.ts                - API client with TypeScript types
├── App.tsx                   - Main component (state management)
├── App.css                   - Tailwind styles
└── index.tsx                 - Application entry point
```

#### Angular Structure
```
src/app/
├── components/
│   ├── product-form/
│   │   ├── product-form.component.ts
│   │   ├── product-form.component.html
│   │   └── product-form.component.css
│   └── product-list/
│       ├── product-list.component.ts
│       ├── product-list.component.html
│       └── product-list.component.css
├── services/
│   └── product.service.ts    - API service (HttpClient)
└── app.component.ts          - Root component
```

---

## API Endpoints

All endpoints are under `/api/v1` and return JSON responses.

### Products Resource

| Method | Endpoint | Description | Status |
|--------|----------|-------------|--------|
| GET | `/products` | List all products | 200 |
| GET | `/products/{id}` | Get single product | 200 |
| POST | `/products` | Create new product | 201 |
| PUT | `/products/{id}` | Update product | 200 |
| DELETE | `/products/{id}` | Delete product | 200 |

### Response Format

**Success Response (GET /api/v1/products):**
```json
{
  "success": true,
  "message": "Products retrieved successfully",
  "data": [
    {
      "id": 1,
      "name": "Laptop",
      "description": "High-performance laptop",
      "price": 1299.99,
      "quantity": 5,
      "created_at": "2025-01-01T10:00:00Z",
      "updated_at": "2025-01-01T10:00:00Z"
    }
  ]
}
```

**Error Response:**
```json
{
  "success": false,
  "message": "Error description here"
}
```

### Validation Rules

**Create Product (POST /api/v1/products):**
- `name` - Required, string, max 255
- `description` - Optional, string
- `price` - Required, numeric, min 0
- `quantity` - Required, integer, min 0

**Update Product (PUT /api/v1/products/{id}):**
- All fields optional
- Same validation rules as create

---

## Database Schema

### Products Table
```sql
CREATE TABLE products (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL,
    description TEXT NULL,
    price DECIMAL(10, 2) NOT NULL,
    quantity INT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

Create migration:
```bash
php artisan make:migration create_products_table
```

---

## Troubleshooting

### Port Already in Use

**Backend (8000):**
```bash
# Windows
netstat -ano | findstr :8000
# Kill process: taskkill /PID <PID> /F

# macOS/Linux
lsof -i :8000
# Kill process: kill -9 <PID>

# Use different port
php artisan serve --port=8001
```

**React (3000):**
```bash
# Windows
netstat -ano | findstr :3000

# Use different port
$env:PORT=3001; npm start
```

**Angular (4200):**
```bash
# Use different port
ng serve --port=4201
```

### CORS Issues
1. Verify backend is running on correct URL
2. Check `FRONTEND_URLS` in `.env` includes frontend URL
3. Clear Laravel cache: `php artisan config:clear`
4. Verify frontend API URL matches backend

### Database Errors
```bash
# Reset database completely
php artisan migrate:reset
php artisan migrate

# Check migration status
php artisan migrate:status
```

### npm/Composer Issues
```bash
# Clear npm cache
npm cache clean --force
rm -rf node_modules package-lock.json
npm install

# Update Composer
composer update

# Clear Composer cache
composer clearcache
```

---

## Development Workflows

### Adding a New Feature (Backend)

1. **Create Migration** (if needed):
   ```bash
   php artisan make:migration add_column_to_products
   ```

2. **Create Model** (if new resource):
   ```bash
   php artisan make:model Category -m
   ```

3. **Create Repository & Interface**:
   - `app/Interfaces/CategoryRepositoryInterface.php`
   - `app/Repositories/CategoryRepository.php`

4. **Create Service**:
   ```bash
   php artisan make:service CategoryService
   ```

5. **Create Controller**:
   ```bash
   php artisan make:controller Api/V1/CategoryController --api
   ```

6. **Create Requests**:
   ```bash
   php artisan make:request Api/V1/StoreCategoryRequest
   php artisan make:request Api/V1/UpdateCategoryRequest
   ```

7. **Create Resource**:
   ```bash
   php artisan make:resource CategoryResource
   ```

8. **Register Routes** in `routes/api.php`

9. **Register Service Provider** in `app/Providers/AppServiceProvider.php`

### Testing an Endpoint

```bash
# Using curl
curl -X GET http://localhost:8000/api/v1/products

curl -X POST http://localhost:8000/api/v1/products \
  -H "Content-Type: application/json" \
  -d '{"name":"Test","price":99.99,"quantity":5}'

# Using PowerShell
$headers = @{ "Content-Type" = "application/json" }
$body = @{ name = "Test"; price = 99.99; quantity = 5 } | ConvertTo-Json
Invoke-WebRequest -Uri "http://localhost:8000/api/v1/products" `
  -Method POST -Headers $headers -Body $body
```

---

## File Locations Reference

### Backend Files
- Controllers: `backend/app/Http/Controllers/Api/V1/`
- Services: `backend/app/Services/`
- Repositories: `backend/app/Repositories/`
- Models: `backend/app/Models/`
- Routes: `backend/routes/api.php`
- Migrations: `backend/database/migrations/`
- Config: `backend/config/`
- Environment: `backend/.env`

### React Files
- Components: `frontend-react/src/components/`
- Services: `frontend-react/src/services/`
- Main: `frontend-react/src/App.tsx`
- Config: `frontend-react/src/services/api.ts`

### Angular Files
- Components: `frontend-angular/src/app/components/`
- Services: `frontend-angular/src/app/services/`
- Main: `frontend-angular/src/app/app.component.ts`
- Config: `frontend-angular/src/app/services/product.service.ts`

---

## Key Files to Understand

### Understanding the Flow
1. Start with `backend/routes/api.php` - See route definitions
2. Check `backend/app/Http/Controllers/Api/V1/ProductController.php` - Controller logic
3. Review `backend/app/Services/ProductService.php` - Business logic
4. Examine `backend/app/Repositories/ProductRepository.php` - Data access
5. Look at `backend/app/Models/Product.php` - Database model

### Frontend Integration
- **React**: `frontend-react/src/services/api.ts` - How requests are made
- **Angular**: `frontend-angular/src/app/services/product.service.ts` - HttpClient usage

---

## Performance & Optimization Tips

### Backend
- Use database indexes on frequently queried columns
- Cache repeated queries: `Cache::remember('products', 60, function() { ... })`
- Use eager loading to prevent N+1 queries: `Product::with('relations')->get()`
- Use pagination: `Product::paginate(15)`

### Frontend React
- Use `React.memo()` for component memoization
- Implement code splitting with `React.lazy()`
- Use `useCallback` for expensive computations
- Lazy load images

### Frontend Angular
- Use `trackBy` in `*ngFor` loops
- Unsubscribe from observables in `ngOnDestroy`
- Use `OnPush` change detection strategy
- Implement lazy loading for routes

---

## Deployment

### Backend (Production)
```bash
# Set production mode
APP_ENV=production APP_DEBUG=false

# Install without dev dependencies
composer install --no-dev

# Run migrations
php artisan migrate --force

# Clear caches
php artisan config:cache
php artisan route:cache
php artisan view:cache

# Use PHP-FPM + Nginx/Apache in production
```

### React Frontend
```bash
# Build production bundle
npm run build

# Deploy dist/ folder to CDN or web server
```

### Angular Frontend
```bash
# Build with production optimizations
ng build --configuration production

# Deploy dist/frontend-angular folder to CDN or web server
```

---

## Additional Resources

- [Laravel Documentation](https://laravel.com/docs)
- [React Documentation](https://react.dev)
- [Angular Documentation](https://angular.io/docs)
- [Tailwind CSS](https://tailwindcss.com/docs)
- [RxJS (Angular)](https://rxjs.dev)

---

**Last Updated**: November 2025
**Project Version**: 1.0.0
