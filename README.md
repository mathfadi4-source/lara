# Laravel React Angular CRUD Project

A complete, production-ready CRUD application demonstrating best practices in full-stack development.

## 🚀 Quick Start

### Prerequisites
- Node.js v20.17.0+
- PHP 8.1+
- Composer
- Git

### Start Backend (Terminal 1)
```bash
cd backend
composer install
php artisan migrate
php artisan serve
```
API: `http://localhost:8000`

### Start React Frontend (Terminal 2) - Optional
```bash
cd frontend-react
npm install
npm start
```
App: `http://localhost:3000`

### Start Angular Frontend (Terminal 3) - Optional
```bash
cd frontend-angular
npm install
ng serve
```
App: `http://localhost:4200`

## 📁 Project Structure

```
├── backend/           # Laravel 12 API with SOLID principles
├── frontend-react/    # React 18 with TypeScript & Tailwind
├── frontend-angular/  # Angular 17 with Tailwind
├── SETUP.md          # Comprehensive setup guide
└── README.md         # This file
```

## ✨ Features

### Backend (Laravel)
- ✅ RESTful API with versioning (`/api/v1`)
- ✅ SOLID principles implementation
- ✅ Repository pattern for data access
- ✅ Service layer for business logic
- ✅ Form request validation
- ✅ API resource transformers
- ✅ Comprehensive error handling

### Frontend (React)
- ✅ Modern functional components with Hooks
- ✅ TypeScript for type safety
- ✅ Tailwind CSS styling
- ✅ Fetch API integration
- ✅ Form validation
- ✅ Error boundaries

### Frontend (Angular)
- ✅ Standalone components
- ✅ Reactive forms with FormBuilder
- ✅ HttpClient for API calls
- ✅ Dependency injection
- ✅ Tailwind CSS styling
- ✅ Type-safe services

## 📚 API Endpoints

| Method | Endpoint | Action |
|--------|----------|--------|
| GET | `/api/v1/products` | List all products |
| GET | `/api/v1/products/{id}` | Get product |
| POST | `/api/v1/products` | Create product |
| PUT | `/api/v1/products/{id}` | Update product |
| DELETE | `/api/v1/products/{id}` | Delete product |

## 🏗️ Architecture

### Backend Design Patterns
- **Repository Pattern**: Abstraction layer for data operations
- **Service Pattern**: Business logic separation
- **Request/Response Pattern**: Input validation and output formatting

### SOLID Principles Applied
- Single Responsibility
- Open/Closed
- Liskov Substitution
- Interface Segregation
- Dependency Inversion

## 🛠️ Technology Stack

### Backend
- Laravel 12
- PHP 8.1+
- SQLite/MySQL/PostgreSQL
- Composer

### Frontend (React)
- React 18
- TypeScript
- Tailwind CSS
- Fetch API

### Frontend (Angular)
- Angular 17
- TypeScript
- Tailwind CSS
- RxJS

## 📖 Documentation

See [SETUP.md](./SETUP.md) for:
- Detailed setup instructions
- Database configuration
- API documentation with examples
- Troubleshooting guide
- Production deployment steps

## 🔧 Common Commands

### Backend
```bash
cd backend

# Install dependencies
composer install

# Run migrations
php artisan migrate

# Start development server
php artisan serve

# Run tests
php artisan test

# Database reset
php artisan migrate:reset
php artisan migrate
```

### React
```bash
cd frontend-react

# Install dependencies
npm install

# Start dev server
npm start

# Build for production
npm run build
```

### Angular
```bash
cd frontend-angular

# Install dependencies
npm install

# Start dev server
ng serve

# Build for production
ng build --prod
```

## 💡 Usage

1. **Create Product**: Fill form and click "Create Product"
2. **View Products**: All products appear in the table
3. **Edit Product**: Click "Edit" to modify product
4. **Delete Product**: Click "Delete" and confirm

## 🚨 Troubleshooting

**Port conflicts?**
```bash
# Change Laravel port
php artisan serve --port=8001

# Change React port
PORT=3001 npm start

# Change Angular port
ng serve --port=4201
```

**Database error?**
```bash
cd backend
php artisan migrate:reset
php artisan migrate
```

**CORS issues?**
- Ensure backend is running
- Check API URL in frontend matches backend URL
- Backend CORS should be configured automatically

## 📝 Best Practices Implemented

✅ Type-safe code (TypeScript everywhere)
✅ Separation of concerns
✅ DRY (Don't Repeat Yourself)
✅ Component reusability
✅ Error handling
✅ Loading states
✅ Input validation
✅ RESTful API design
✅ Environment configuration
✅ Git workflow ready

## 📄 License

MIT License - feel free to use this project as a starting point for your applications.

## 🤝 Support

For detailed information, refer to:
- [SETUP.md](./SETUP.md) - Complete setup guide
- [Laravel Docs](https://laravel.com/docs)
- [React Docs](https://react.dev)
- [Angular Docs](https://angular.io/docs)
- [Tailwind Docs](https://tailwindcss.com/docs)

---

**Version:** 1.0.0  
**Last Updated:** November 2025

Happy coding! 🎉
