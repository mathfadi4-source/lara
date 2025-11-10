# Quick Start Guide

## 🚀 Get Running in 5 Minutes

### 1️⃣ Start the Backend
```bash
cd backend
php artisan migrate
php artisan serve
```
✅ API ready at `http://localhost:8000`

### 2️⃣ Choose Your Frontend

**Option A: React**
```bash
cd frontend-react
npm install
npm start
```
✅ App at `http://localhost:3000`

**Option B: Angular**
```bash
cd frontend-angular
npm install
ng serve
```
✅ App at `http://localhost:4200`

## 📝 What's Included

| Item | Location | Purpose |
|------|----------|---------|
| **API** | `backend/` | RESTful CRUD API with SOLID architecture |
| **React UI** | `frontend-react/` | React CRUD interface |
| **Angular UI** | `frontend-angular/` | Angular CRUD interface |
| **Docs** | `SETUP.md` | Comprehensive setup guide |
| **Docs** | `README.md` | Project overview |

## 🧪 Test the API

```bash
# Get all products
curl http://localhost:8000/api/v1/products

# Create a product
curl -X POST http://localhost:8000/api/v1/products \
  -H "Content-Type: application/json" \
  -d '{"name":"Laptop","price":999.99,"quantity":5}'

# Update a product
curl -X PUT http://localhost:8000/api/v1/products/1 \
  -H "Content-Type: application/json" \
  -d '{"price":899.99}'

# Delete a product
curl -X DELETE http://localhost:8000/api/v1/products/1
```

## 🏗️ Architecture Overview

```
Request → Frontend (React/Angular)
          ↓
       API Call (HTTP)
          ↓
Backend API (Laravel v1/products)
  ├→ Request Validation
  ├→ Service Layer
  ├→ Repository Layer
  ├→ Database
  └→ Response Formatting
          ↓
        Frontend
          ↓
        Display
```

## 💾 Database

**Default:** SQLite (works out of the box)  
**File:** `backend/database/database.sqlite`

**Schema:**
```
products
├── id (PK)
├── name (string)
├── description (text, nullable)
├── price (decimal)
├── quantity (integer)
├── created_at
└── updated_at
```

## 🛠️ Common Issues

| Issue | Solution |
|-------|----------|
| Port 8000 in use | `php artisan serve --port=8001` |
| Port 3000 in use | `PORT=3001 npm start` |
| Port 4200 in use | `ng serve --port=4201` |
| npm install fails | Delete `node_modules`, try `npm install` again |
| Database errors | Run `php artisan migrate:reset && php artisan migrate` |
| API connection fails | Check `src/services/api.ts` or `product.service.ts` |

## 📚 Key Files

### Backend
- `app/Models/Product.php` - Database model
- `app/Http/Controllers/Api/V1/ProductController.php` - API endpoints
- `app/Services/ProductService.php` - Business logic
- `app/Repositories/ProductRepository.php` - Data access
- `routes/api.php` - API routes

### React
- `src/App.tsx` - Main component
- `src/components/ProductForm.tsx` - Form component
- `src/components/ProductList.tsx` - List component
- `src/services/api.ts` - API client

### Angular
- `src/app/app.component.ts` - Main component
- `src/app/components/product-form/` - Form component
- `src/app/components/product-list/` - List component
- `src/app/services/product.service.ts` - API service

## ✨ Features

✅ Full CRUD operations  
✅ Type-safe code (TypeScript)  
✅ RESTful API design  
✅ Form validation  
✅ Error handling  
✅ Tailwind CSS styling  
✅ SOLID principles  
✅ Repository pattern  
✅ Service layer  
✅ Production ready  

## 🔄 Workflow

1. **Add a Product**
   - Fill form → Click "Create" → Appears in table

2. **Edit a Product**
   - Click "Edit" → Form populates → Update → Click "Update"

3. **Delete a Product**
   - Click "Delete" → Confirm → Gone from table

## 📖 Learn More

- **Full Setup Guide**: See `SETUP.md`
- **API Details**: See `SETUP.md` API section
- **Best Practices**: See `SETUP.md` Best Practices
- **Deployment**: See `SETUP.md` Production section

## 🎯 Next Steps

1. ✅ Backend running? Test API with curl
2. ✅ Frontend running? Try CRUD operations
3. ✅ Ready to deploy? See `SETUP.md` Production section
4. ✅ Need customization? See `SETUP.md` for architecture details

## 💡 Pro Tips

- Use both React and Angular to compare frameworks
- Extend with authentication (JWT)
- Add pagination to product list
- Implement search/filter
- Add image upload
- Create user roles
- Add unit tests

## 🆘 Still Stuck?

1. Check `SETUP.md` Troubleshooting section
2. Verify all services are running on correct ports
3. Check console for error messages
4. Ensure database migrations ran successfully

---

**Ready to code?** Start with the 2️⃣ commands above! 🚀
