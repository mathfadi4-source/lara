# Laravel React Angular CRUD Application

A complete CRUD (Create, Read, Update, Delete) application with a Laravel backend API and two frontend options: React and Angular. Both frontends use Tailwind CSS for styling and communicate with the RESTful API.

## Project Structure

```
laravel-react-angular-crud/
├── backend/                    # Laravel API Backend
│   ├── app/
│   │   ├── Http/
│   │   │   ├── Controllers/Api/V1/  # API Controllers
│   │   │   ├── Requests/Api/V1/     # Form Requests
│   │   │   └── Resources/           # API Resources (Response Formatting)
│   │   ├── Services/          # Business Logic Services
│   │   ├── Repositories/      # Data Access Layer
│   │   ├── Interfaces/        # SOLID Interfaces
│   │   └── Models/            # Eloquent Models
│   ├── database/migrations/   # Database Migrations
│   ├── routes/api.php         # API Routes
│   └── ...
├── frontend-react/            # React Frontend (TypeScript)
│   ├── src/
│   │   ├── components/        # React Components
│   │   ├── services/          # API Client Service
│   │   └── App.tsx            # Main App Component
│   ├── tailwind.config.js     # Tailwind Configuration
│   └── ...
├── frontend-angular/          # Angular Frontend
│   ├── src/
│   │   ├── app/
│   │   │   ├── services/      # Angular Services
│   │   │   ├── components/    # Angular Components
│   │   │   └── app.component.ts
│   │   └── ...
│   ├── tailwind.config.js     # Tailwind Configuration
│   └── ...
└── SETUP.md                   # This file
```

## Prerequisites

### Required Software
- **Node.js** v20.17.0 or higher
- **npm** v10.8.2 or higher
- **PHP** v8.1 or higher
- **Composer** (for PHP dependencies)
- **Git** (for version control)

### Installation Verification
```bash
node --version
npm --version
php --version
composer --version
git --version
```

## Backend Setup (Laravel)

### 1. Navigate to Backend Directory
```bash
cd backend
```

### 2. Install PHP Dependencies
```bash
composer install
```

### 3. Configure Environment
```bash
cp .env.example .env
php artisan key:generate
```

### 4. Database Configuration
By default, the project uses SQLite. If you prefer MySQL/PostgreSQL, update `.env`:
```
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=laravel_crud
DB_USERNAME=root
DB_PASSWORD=password
```

### 5. Run Migrations
```bash
php artisan migrate
```

### 6. Enable CORS (if needed)
Edit `.env` to allow frontend origins:
```
FRONTEND_URLS=http://localhost:3000,http://localhost:4200
```

### 7. Start Laravel Development Server
```bash
php artisan serve
```

The API will be available at `http://localhost:8000`

## API Endpoints

All endpoints follow REST conventions and return JSON responses.

### Products API (v1)

**Base URL:** `http://localhost:8000/api/v1`

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/products` | Get all products |
| GET | `/products/{id}` | Get product by ID |
| POST | `/products` | Create new product |
| PUT | `/products/{id}` | Update product |
| DELETE | `/products/{id}` | Delete product |

### Response Format

**Success Response:**
```json
{
  "success": true,
  "message": "Products retrieved successfully",
  "data": [
    {
      "id": 1,
      "name": "Product Name",
      "description": "Product description",
      "price": 99.99,
      "quantity": 10,
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
  "message": "Error message here"
}
```

## Backend Architecture (SOLID Principles)

### Design Patterns Used

1. **Repository Pattern**
   - `ProductRepository` implements `ProductRepositoryInterface`
   - Abstraction layer for data access
   - Easy to swap with different implementations

2. **Service Layer Pattern**
   - `ProductService` handles business logic
   - Dependency injection of repositories
   - Centralized error handling

3. **Request/Response Pattern**
   - Form Requests (`StoreProductRequest`, `UpdateProductRequest`)
   - Automatic validation
   - Resource classes for response formatting

### File Organization

```
app/
├── Interfaces/
│   └── ProductRepositoryInterface.php    # Dependency abstraction
├── Repositories/
│   └── ProductRepository.php             # Data access implementation
├── Services/
│   └── ProductService.php                # Business logic
├── Http/
│   ├── Controllers/Api/V1/
│   │   └── ProductController.php         # API endpoints
│   ├── Requests/Api/V1/
│   │   ├── StoreProductRequest.php       # Validation for create
│   │   └── UpdateProductRequest.php      # Validation for update
│   └── Resources/
│       └── ProductResource.php           # Response formatting
└── Models/
    └── Product.php                       # Eloquent model
```

### SOLID Principles Applied

- **Single Responsibility:** Each class has one reason to change
- **Open/Closed:** Use interfaces for extension without modification
- **Liskov Substitution:** Repository implementations are interchangeable
- **Interface Segregation:** Focused, minimal interfaces
- **Dependency Inversion:** Depend on abstractions, not concretions

## React Frontend Setup

### 1. Navigate to React Frontend
```bash
cd frontend-react
```

### 2. Install Dependencies
```bash
npm install
```

### 3. Configure API Endpoint
Edit `src/services/api.ts` and update the API_URL if needed:
```typescript
const API_URL = 'http://localhost:8000/api/v1';
```

### 4. Start Development Server
```bash
npm start
```

Access the application at `http://localhost:3000`

### React Project Structure

```
src/
├── components/
│   ├── ProductForm.tsx        # Form for create/edit
│   └── ProductList.tsx        # Table to display products
├── services/
│   └── api.ts                 # API client with TypeScript types
└── App.tsx                    # Main component
```

### React Features

- **TypeScript Support:** Full type safety
- **Functional Components:** Using React Hooks
- **Tailwind CSS:** Utility-first styling
- **Error Handling:** User-friendly error messages
- **Form Validation:** Client-side validation
- **API Integration:** Fetch-based API client

## Angular Frontend Setup

### 1. Navigate to Angular Frontend
```bash
cd frontend-angular
```

### 2. Install Dependencies
```bash
npm install
```

### 3. Configure API Endpoint
Edit `src/app/services/product.service.ts` and update the apiUrl if needed:
```typescript
private apiUrl = 'http://localhost:8000/api/v1/products';
```

### 4. Start Development Server
```bash
ng serve
```

or

```bash
npm start
```

Access the application at `http://localhost:4200`

### Angular Project Structure

```
src/app/
├── components/
│   ├── product-form/          # Form component
│   │   ├── product-form.component.ts
│   │   └── product-form.component.html
│   └── product-list/          # List component
│       ├── product-list.component.ts
│       └── product-list.component.html
├── services/
│   └── product.service.ts     # API service
└── app.component.ts           # Main component
```

### Angular Features

- **Standalone Components:** Modern Angular setup
- **TypeScript Support:** Full type safety
- **Reactive Forms:** FormBuilder for form handling
- **Dependency Injection:** Built-in DI system
- **HttpClient:** Built-in HTTP client
- **Tailwind CSS:** Utility-first styling

## Running the Complete Application

### Terminal 1: Start Backend
```bash
cd backend
php artisan serve
# API running at http://localhost:8000
```

### Terminal 2: Start React Frontend (Optional)
```bash
cd frontend-react
npm start
# React app running at http://localhost:3000
```

### Terminal 3: Start Angular Frontend (Optional)
```bash
cd frontend-angular
ng serve
# Angular app running at http://localhost:4200
```

## Usage

### Creating a Product

1. Fill in the form with product details
2. Click "Create Product" button
3. Product appears in the table below

### Editing a Product

1. Click "Edit" button on any product row
2. Form auto-populates with product data
3. Modify values as needed
4. Click "Update Product" button

### Deleting a Product

1. Click "Delete" button on any product row
2. Confirm deletion in the dialog
3. Product is removed from table

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

## Validation Rules

### Create Product (POST /api/v1/products)
- `name` (required): string, max 255 characters
- `description` (optional): string
- `price` (required): numeric, min 0
- `quantity` (required): integer, min 0

### Update Product (PUT /api/v1/products/{id})
- All fields are optional for partial updates
- Same validation rules as create

## API Testing

### Using cURL

**Get all products:**
```bash
curl -X GET http://localhost:8000/api/v1/products
```

**Create product:**
```bash
curl -X POST http://localhost:8000/api/v1/products \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Laptop",
    "description": "High-performance laptop",
    "price": 1299.99,
    "quantity": 5
  }'
```

**Update product:**
```bash
curl -X PUT http://localhost:8000/api/v1/products/1 \
  -H "Content-Type: application/json" \
  -d '{"price": 1199.99}'
```

**Delete product:**
```bash
curl -X DELETE http://localhost:8000/api/v1/products/1
```

## Troubleshooting

### Port Already in Use

**Laravel (8000):**
```bash
php artisan serve --port=8001
```

**React (3000):**
```bash
PORT=3001 npm start
```

**Angular (4200):**
```bash
ng serve --port=4201
```

### CORS Issues

If frontend can't connect to backend, ensure Laravel CORS is properly configured:

1. Update `.env` with allowed origins
2. Clear Laravel cache: `php artisan config:clear`
3. Verify API URL in frontend matches backend URL

### Database Issues

**Reset database:**
```bash
php artisan migrate:reset
php artisan migrate
```

**Create demo data:**
```bash
php artisan tinker
# Then run:
# Product::create(['name' => 'Test', 'price' => 99.99, 'quantity' => 10])
```

## Production Deployment

### Laravel Backend
1. Set `APP_ENV=production` in `.env`
2. Run `composer install --no-dev`
3. Run `php artisan migrate --force`
4. Use PHP-FPM with Nginx/Apache

### React Frontend
```bash
npm run build
# Deploy dist/ folder to static hosting
```

### Angular Frontend
```bash
ng build --prod
# Deploy dist/frontend-angular folder to static hosting
```

## Best Practices Implemented

### Backend
- ✅ Repository pattern for data access
- ✅ Service layer for business logic
- ✅ Form request validation
- ✅ Resource classes for API responses
- ✅ Proper HTTP status codes
- ✅ Error handling and logging
- ✅ Type hints on all methods

### Frontend (React)
- ✅ Component-based architecture
- ✅ Type-safe with TypeScript
- ✅ Functional components with hooks
- ✅ Separation of concerns (components vs services)
- ✅ Error handling with user feedback
- ✅ Loading states
- ✅ API service abstraction

### Frontend (Angular)
- ✅ Standalone components
- ✅ Type-safe with TypeScript
- ✅ Reactive forms with FormBuilder
- ✅ Service injection with dependency injection
- ✅ Error handling with observables
- ✅ Loading states
- ✅ Component lifecycle management

## Git Workflow

```bash
# Initial commit
git add .
git commit -m "Initial project setup with Laravel, React, and Angular"

# Feature branches
git checkout -b feature/new-feature
# Make changes...
git add .
git commit -m "Add new feature"
git push origin feature/new-feature

# Merge to main
git checkout main
git merge feature/new-feature
git push origin main
```

## Additional Commands

### Laravel
```bash
# Create new model with migration
php artisan make:model Product -m

# Run tests
php artisan test

# Tinker shell
php artisan tinker
```

### React
```bash
# Build for production
npm run build

# Run tests
npm test

# Format code
npm run prettier
```

### Angular
```bash
# Build for production
ng build --prod

# Run tests
ng test

# Format code
ng lint
```

## Support & Documentation

- [Laravel Documentation](https://laravel.com/docs)
- [React Documentation](https://react.dev)
- [Angular Documentation](https://angular.io/docs)
- [Tailwind CSS Documentation](https://tailwindcss.com/docs)

## License

This project is open source and available under the MIT License.

---

**Last Updated:** November 2025
**Version:** 1.0.0
