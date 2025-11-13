# Project Creation Guide

This guide documents how to create a new full-stack CRUD application following the same architecture and best practices as this project.

---

## Prerequisites

Before starting, ensure you have:

- **Node.js** v18+ ([download](https://nodejs.org))
- **npm** v9+ (comes with Node.js)
- **PHP** 8.1+ ([download](https://www.php.net/downloads))
- **Composer** ([download](https://getcomposer.org/download))
- **Git** ([download](https://git-scm.com/downloads))

### Verify Installation

```bash
node --version          # v18.0.0 or higher
npm --version           # v9.0.0 or higher
php --version           # 8.1 or higher
composer --version      # Latest version
git --version           # Any recent version
```

---

## Project Structure Template

Create the following directory structure:

```
project-name/
├── backend/                    # Laravel 11+ API
├── frontend-react/             # React 18+ Frontend
├── frontend-angular/           # Angular 17+ Frontend
├── .gitignore                  # Git ignore rules
├── README.md                   # Project documentation
├── SETUP.md                    # Setup instructions
├── QUICK_START.md              # Quick start guide
├── WARP.md                     # AI agent documentation
└── PROJECT_CREATION_GUIDE.md   # This guide
```

---

## Step 1: Create Project Directory

```bash
# Create and navigate to project directory
mkdir project-name
cd project-name

# Initialize git
git init
```

---

## Step 2: Create Backend (Laravel 11+)

### 2.1 Create Laravel Project

```bash
# Create new Laravel project
composer create-project laravel/laravel backend

# Navigate to backend
cd backend

# Create SQLite database
touch database/database.sqlite

# Generate application key
php artisan key:generate

# Run migrations
php artisan migrate

cd ..
```

### 2.2 Install Backend Dependencies

```bash
cd backend

# Install dev dependencies for Tailwind/Vite
npm install

# Install Tailwind CSS
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p

cd ..
```

### 2.3 Create Backend Structure (SOLID Architecture)

Inside `backend/`, create the following directories and files:

#### A. Create Directories

```bash
cd backend

# Create required directories
mkdir -p app/Http/Controllers/Api/V1
mkdir -p app/Http/Requests/Api/V1
mkdir -p app/Http/Resources
mkdir -p app/Services
mkdir -p app/Repositories
mkdir -p app/Interfaces

cd ..
```

#### B. Create Model and Migration

```bash
cd backend

# Create Product model with migration
php artisan make:model Product -m

cd ..
```

Edit `backend/database/migrations/*_create_products_table.php`:

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('products', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->text('description')->nullable();
            $table->decimal('price', 10, 2);
            $table->integer('quantity');
            $table->timestamps();
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('products');
    }
};
```

#### C. Update Model

Edit `backend/app/Models/Product.php`:

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Product extends Model
{
    protected $fillable = ['name', 'description', 'price', 'quantity'];

    protected $casts = [
        'price' => 'decimal:2',
        'quantity' => 'integer',
    ];
}
```

#### D. Create Interface

Create `backend/app/Interfaces/ProductRepositoryInterface.php`:

```php
<?php

namespace App\Interfaces;

interface ProductRepositoryInterface
{
    public function all();
    public function find($id);
    public function create(array $data);
    public function update($id, array $data);
    public function delete($id);
}
```

#### E. Create Repository

Create `backend/app/Repositories/ProductRepository.php`:

```php
<?php

namespace App\Repositories;

use App\Interfaces\ProductRepositoryInterface;
use App\Models\Product;

class ProductRepository implements ProductRepositoryInterface
{
    public function all()
    {
        return Product::all();
    }

    public function find($id)
    {
        return Product::findOrFail($id);
    }

    public function create(array $data)
    {
        return Product::create($data);
    }

    public function update($id, array $data)
    {
        $product = Product::findOrFail($id);
        $product->update($data);
        return $product;
    }

    public function delete($id)
    {
        $product = Product::findOrFail($id);
        return $product->delete();
    }
}
```

#### F. Create Service

Create `backend/app/Services/ProductService.php`:

```php
<?php

namespace App\Services;

use App\Interfaces\ProductRepositoryInterface;
use Exception;

class ProductService
{
    protected $productRepository;

    public function __construct(ProductRepositoryInterface $productRepository)
    {
        $this->productRepository = $productRepository;
    }

    public function getAllProducts()
    {
        try {
            return $this->productRepository->all();
        } catch (Exception $e) {
            throw new Exception('Failed to fetch products: ' . $e->getMessage());
        }
    }

    public function getProductById($id)
    {
        try {
            return $this->productRepository->find($id);
        } catch (Exception $e) {
            throw new Exception('Product not found: ' . $e->getMessage());
        }
    }

    public function createProduct(array $data)
    {
        try {
            return $this->productRepository->create($data);
        } catch (Exception $e) {
            throw new Exception('Failed to create product: ' . $e->getMessage());
        }
    }

    public function updateProduct($id, array $data)
    {
        try {
            return $this->productRepository->update($id, $data);
        } catch (Exception $e) {
            throw new Exception('Failed to update product: ' . $e->getMessage());
        }
    }

    public function deleteProduct($id)
    {
        try {
            return $this->productRepository->delete($id);
        } catch (Exception $e) {
            throw new Exception('Failed to delete product: ' . $e->getMessage());
        }
    }
}
```

#### G. Create Form Requests

Create `backend/app/Http/Requests/Api/V1/StoreProductRequest.php`:

```php
<?php

namespace App\Http\Requests\Api\V1;

use Illuminate\Foundation\Http\FormRequest;

class StoreProductRequest extends FormRequest
{
    public function authorize(): bool
    {
        return true;
    }

    public function rules(): array
    {
        return [
            'name' => 'required|string|max:255',
            'description' => 'nullable|string',
            'price' => 'required|numeric|min:0',
            'quantity' => 'required|integer|min:0',
        ];
    }
}
```

Create `backend/app/Http/Requests/Api/V1/UpdateProductRequest.php`:

```php
<?php

namespace App\Http\Requests\Api\V1;

use Illuminate\Foundation\Http\FormRequest;

class UpdateProductRequest extends FormRequest
{
    public function authorize(): bool
    {
        return true;
    }

    public function rules(): array
    {
        return [
            'name' => 'sometimes|required|string|max:255',
            'description' => 'nullable|string',
            'price' => 'sometimes|required|numeric|min:0',
            'quantity' => 'sometimes|required|integer|min:0',
        ];
    }
}
```

#### H. Create Resource

Create `backend/app/Http/Resources/ProductResource.php`:

```php
<?php

namespace App\Http\Resources;

use Illuminate\Http\Request;
use Illuminate\Http\Resources\Json\JsonResource;

class ProductResource extends JsonResource
{
    public function toArray(Request $request): array
    {
        return [
            'id' => $this->id,
            'name' => $this->name,
            'description' => $this->description,
            'price' => $this->price,
            'quantity' => $this->quantity,
            'created_at' => $this->created_at,
            'updated_at' => $this->updated_at,
        ];
    }
}
```

#### I. Create Controller

Create `backend/app/Http/Controllers/Api/V1/ProductController.php`:

```php
<?php

namespace App\Http\Controllers\Api\V1;

use App\Http\Controllers\Controller;
use App\Http\Requests\Api\V1\StoreProductRequest;
use App\Http\Requests\Api\V1\UpdateProductRequest;
use App\Http\Resources\ProductResource;
use App\Services\ProductService;
use Illuminate\Http\JsonResponse;

class ProductController extends Controller
{
    protected $productService;

    public function __construct(ProductService $productService)
    {
        $this->productService = $productService;
    }

    public function index(): JsonResponse
    {
        try {
            $products = $this->productService->getAllProducts();
            return response()->json([
                'success' => true,
                'message' => 'Products retrieved successfully',
                'data' => ProductResource::collection($products),
            ], 200);
        } catch (\Exception $e) {
            return response()->json([
                'success' => false,
                'message' => $e->getMessage(),
            ], 500);
        }
    }

    public function store(StoreProductRequest $request): JsonResponse
    {
        try {
            $product = $this->productService->createProduct($request->validated());
            return response()->json([
                'success' => true,
                'message' => 'Product created successfully',
                'data' => new ProductResource($product),
            ], 201);
        } catch (\Exception $e) {
            return response()->json([
                'success' => false,
                'message' => $e->getMessage(),
            ], 500);
        }
    }

    public function show($id): JsonResponse
    {
        try {
            $product = $this->productService->getProductById($id);
            return response()->json([
                'success' => true,
                'message' => 'Product retrieved successfully',
                'data' => new ProductResource($product),
            ], 200);
        } catch (\Exception $e) {
            return response()->json([
                'success' => false,
                'message' => $e->getMessage(),
            ], 404);
        }
    }

    public function update(UpdateProductRequest $request, $id): JsonResponse
    {
        try {
            $product = $this->productService->updateProduct($id, $request->validated());
            return response()->json([
                'success' => true,
                'message' => 'Product updated successfully',
                'data' => new ProductResource($product),
            ], 200);
        } catch (\Exception $e) {
            return response()->json([
                'success' => false,
                'message' => $e->getMessage(),
            ], 500);
        }
    }

    public function destroy($id): JsonResponse
    {
        try {
            $this->productService->deleteProduct($id);
            return response()->json([
                'success' => true,
                'message' => 'Product deleted successfully',
            ], 200);
        } catch (\Exception $e) {
            return response()->json([
                'success' => false,
                'message' => $e->getMessage(),
            ], 500);
        }
    }
}
```

#### J. Register Service Provider

Edit `backend/app/Providers/AppServiceProvider.php`:

```php
<?php

namespace App\Providers;

use App\Interfaces\ProductRepositoryInterface;
use App\Repositories\ProductRepository;
use Illuminate\Support\ServiceProvider;

class AppServiceProvider extends ServiceProvider
{
    public function register(): void
    {
        $this->app->bind(
            ProductRepositoryInterface::class,
            ProductRepository::class
        );
    }

    public function boot(): void
    {
        //
    }
}
```

#### K. Setup Routes

Edit `backend/routes/api.php`:

```php
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\Api\V1\ProductController;

Route::prefix('v1')->group(function () {
    Route::apiResource('products', ProductController::class);
});
```
Edit `backend\bootstrap\app.php`:

```php
<?php

use Illuminate\Foundation\Application;
use Illuminate\Foundation\Configuration\Exceptions;
use Illuminate\Foundation\Configuration\Middleware;

return Application::configure(basePath: dirname(__DIR__))
    ->withRouting(
        web: __DIR__.'/../routes/web.php',
        api: __DIR__.'/../routes/api.php',
        commands: __DIR__.'/../routes/console.php',
        health: '/up',
    )
    ->withMiddleware(function (Middleware $middleware): void {
        //
    })
    ->withExceptions(function (Exceptions $exceptions): void {
        //
    })->create();

```
#### L. Configure CORS

Edit `backend/config/cors.php` to allow frontend origins:

```php
'allowed_origins' => ['http://localhost:3000', 'http://localhost:4200'],
'allowed_origins_patterns' => [],
'allowed_methods' => ['*'],
'allowed_headers' => ['*'],
'exposed_headers' => [],
'max_age' => 0,
'supports_credentials' => false,
```

#### M. Run Migrations

```bash
cd backend
php artisan migrate
cd ..
```

---

## Step 3: Create Frontend (React 18+)

### 3.1 Create React App

```bash
# Create React app with TypeScript
npx create-react-app frontend-react --template typescript

cd frontend-react
```

### 3.2 Install Dependencies

```bash
# Install Tailwind CSS
npm install -D tailwindcss@3 postcss autoprefixer
npx tailwindcss init -p
npm install zustand
npm install -D @tailwindcss/postcss

# Dependencies are already included:
# - react@^18.2.0
# - typescript@^4.9.5
# - react-scripts@5.0.1

cd ..
```

### 3.3 Configure Tailwind

Edit `frontend-react/tailwind.config.js`:

```js
/** @type {import('tailwindcss').Config} */
export default {
  content: [
    "./index.html",
    "./src/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```
Edit `frontend-react/postcss.config.js`:

```js
module.exports = {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
}


```

Edit `frontend-react/src/index.css`:

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

### 3.4 Create API Service

Create `frontend-react/src/services/api.ts`:

```typescript
const API_URL = 'http://localhost:8000/api/v1';

export interface Product {
  id: number;
  name: string;
  description: string | null;
  price: number;
  quantity: number;
  created_at: string;
  updated_at: string;
}

export interface ApiResponse<T> {
  success: boolean;
  message: string;
  data?: T;
}

export const productService = {
  getAll: async (): Promise<Product[]> => {
    const response = await fetch(`${API_URL}/products`);
    const data: ApiResponse<Product[]> = await response.json();
    if (!data.success) throw new Error(data.message);
    return data.data || [];
  },

  getById: async (id: number): Promise<Product> => {
    const response = await fetch(`${API_URL}/products/${id}`);
    const data: ApiResponse<Product> = await response.json();
    if (!data.success) throw new Error(data.message);
    return data.data!;
  },

  create: async (product: Omit<Product, 'id' | 'created_at' | 'updated_at'>): Promise<Product> => {
    const response = await fetch(`${API_URL}/products`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(product),
    });
    const data: ApiResponse<Product> = await response.json();
    if (!data.success) throw new Error(data.message);
    return data.data!;
  },

  update: async (id: number, product: Partial<Omit<Product, 'id' | 'created_at' | 'updated_at'>>): Promise<Product> => {
    const response = await fetch(`${API_URL}/products/${id}`, {
      method: 'PUT',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(product),
    });
    const data: ApiResponse<Product> = await response.json();
    if (!data.success) throw new Error(data.message);
    return data.data!;
  },

  delete: async (id: number): Promise<void> => {
    const response = await fetch(`${API_URL}/products/${id}`, { method: 'DELETE' });
    const data: ApiResponse<void> = await response.json();
    if (!data.success) throw new Error(data.message);
  },
};
```
### 3.4 Create API store

Create `frontend-react/src/store/productStore.ts`:

```typescript
import { create } from 'zustand';
import { Product, productService } from '../services/api';

export interface ProductState {
  products: Product[];
  selectedProduct: Product | null;
  loading: boolean;
  error: string | null;
  
  // Actions
  fetchProducts: () => Promise<void>;
  selectProduct: (product: Product | null) => void;
  createProduct: (product: Omit<Product, 'id'>) => Promise<void>;
  updateProduct: (id: number, product: Omit<Product, 'id'>) => Promise<void>;
  deleteProduct: (id: number) => Promise<void>;
  clearError: () => void;
}

export const useProductStore = create<ProductState>((set) => ({
  products: [],
  selectedProduct: null,
  loading: false,
  error: null,

  fetchProducts: async () => {
    set({ loading: true, error: null });
    try {
      const data = await productService.getAll();
      set({ products: data, loading: false });
    } catch (err) {
      const errorMessage = err instanceof Error ? err.message : 'Failed to fetch products';
      set({ error: errorMessage, loading: false });
    }
  },

  selectProduct: (product) => {
    set({ selectedProduct: product });
  },

  createProduct: async (product) => {
    set({ loading: true, error: null });
    try {
      const created = await productService.create(product);
      set((state) => ({
        products: [...state.products, created],
        selectedProduct: null,
        loading: false,
      }));
    } catch (err) {
      const errorMessage = err instanceof Error ? err.message : 'Failed to create product';
      set({ error: errorMessage, loading: false });
      throw err;
    }
  },

  updateProduct: async (id, product) => {
    set({ loading: true, error: null });
    try {
      const updated = await productService.update(id, product);
      set((state) => ({
        products: state.products.map((p) => (p.id === id ? updated : p)),
        selectedProduct: null,
        loading: false,
      }));
    } catch (err) {
      const errorMessage = err instanceof Error ? err.message : 'Failed to update product';
      set({ error: errorMessage, loading: false });
      throw err;
    }
  },

  deleteProduct: async (id) => {
    set({ loading: true, error: null });
    try {
      await productService.delete(id);
      set((state) => ({
        products: state.products.filter((p) => p.id !== id),
        selectedProduct: state.selectedProduct?.id === id ? null : state.selectedProduct,
        loading: false,
      }));
    } catch (err) {
      const errorMessage = err instanceof Error ? err.message : 'Failed to delete product';
      set({ error: errorMessage, loading: false });
      throw err;
    }
  },

  clearError: () => {
    set({ error: null });
  },
}));
```
### 3.5 Create Components

Create `frontend-react/src/components/ProductForm.tsx`:

```typescript
import React, { useEffect, useState } from 'react';
import { useProductStore } from '../store/productStore';

interface ProductFormProps {
  productId: number | null;
  onSuccess: () => void;
}

export const ProductForm: React.FC<ProductFormProps> = ({ productId, onSuccess }) => {
  const [formData, setFormData] = useState({
    name: '',
    description: '',
    price: '',
    quantity: '',
  });
  const [localError, setLocalError] = useState('');

  const selectedProduct = useProductStore((state) => state.selectedProduct);
  const loading = useProductStore((state) => state.loading);
  const storeError = useProductStore((state) => state.error);
  const createProduct = useProductStore((state) => state.createProduct);
  const updateProduct = useProductStore((state) => state.updateProduct);
  const selectProduct = useProductStore((state) => state.selectProduct);

  useEffect(() => {
    if (productId && selectedProduct) {
      setFormData({
        name: selectedProduct.name,
        description: selectedProduct.description || '',
        price: selectedProduct.price.toString(),
        quantity: selectedProduct.quantity.toString(),
      });
    } else {
      setFormData({ name: '', description: '', price: '', quantity: '' });
    }
  }, [productId, selectedProduct]);

  const handleChange = (e: React.ChangeEvent<HTMLInputElement | HTMLTextAreaElement>) => {
    const { name, value } = e.target;
    setFormData(prev => ({ ...prev, [name]: value }));
  };

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    setLocalError('');

    try {
      const data = {
        name: formData.name,
        description: formData.description || null,
        price: parseFloat(formData.price),
        quantity: parseInt(formData.quantity),
      } as any;

      if (productId && selectedProduct) {
        await updateProduct(selectedProduct.id, data);
      } else {
        await createProduct(data);
      }

      setFormData({ name: '', description: '', price: '', quantity: '' });
      selectProduct(null);
      onSuccess();
    } catch (err) {
      setLocalError(err instanceof Error ? err.message : 'Failed to save product');
    }
  };

  return (
    <form onSubmit={handleSubmit} className="bg-white p-6 rounded-lg shadow mb-6">
      <h2 className="text-2xl font-bold mb-4">{productId ? 'Edit Product' : 'Create Product'}</h2>
      
      {(storeError || localError) && <div className="mb-4 p-3 bg-red-100 text-red-700 rounded">{storeError || localError}</div>}

      <div className="mb-4">
        <label className="block text-gray-700 font-bold mb-2">Name *</label>
        <input
          type="text"
          name="name"
          value={formData.name}
          onChange={handleChange}
          required
          className="w-full px-3 py-2 border border-gray-300 rounded focus:outline-none focus:border-blue-500"
          placeholder="Product name"
        />
      </div>

      <div className="mb-4">
        <label className="block text-gray-700 font-bold mb-2">Description</label>
        <textarea
          name="description"
          value={formData.description}
          onChange={handleChange}
          className="w-full px-3 py-2 border border-gray-300 rounded focus:outline-none focus:border-blue-500"
          placeholder="Product description"
          rows={4}
        />
      </div>

      <div className="grid grid-cols-2 gap-4 mb-4">
        <div>
          <label className="block text-gray-700 font-bold mb-2">Price *</label>
          <input
            type="number"
            name="price"
            value={formData.price}
            onChange={handleChange}
            required
            step="0.01"
            min="0"
            className="w-full px-3 py-2 border border-gray-300 rounded focus:outline-none focus:border-blue-500"
            placeholder="0.00"
          />
        </div>
        <div>
          <label className="block text-gray-700 font-bold mb-2">Quantity *</label>
          <input
            type="number"
            name="quantity"
            value={formData.quantity}
            onChange={handleChange}
            required
            min="0"
            className="w-full px-3 py-2 border border-gray-300 rounded focus:outline-none focus:border-blue-500"
            placeholder="0"
          />
        </div>
      </div>

      <button
        type="submit"
        disabled={loading}
        className="w-full px-4 py-2 bg-green-500 text-white rounded hover:bg-green-600 disabled:bg-gray-400"
      >
        {loading ? 'Saving...' : productId ? 'Update Product' : 'Create Product'}
      </button>
    </form>
  );
};

```

Create `frontend-react/src/components/ProductList.tsx`:

```typescript
import React, { useEffect } from 'react';
import { useProductStore } from '../store/productStore';

interface ProductListProps {
  onEdit: (id: number) => void;
}

export const ProductList: React.FC<ProductListProps> = ({ onEdit }) => {
  const products = useProductStore((state) => state.products);
  const loading = useProductStore((state) => state.loading);
  const error = useProductStore((state) => state.error);
  const fetchProducts = useProductStore((state) => state.fetchProducts);
  const deleteProduct = useProductStore((state) => state.deleteProduct);

  useEffect(() => {
    fetchProducts();
  }, [fetchProducts]);

  const handleDelete = async (id: number) => {
    if (!window.confirm('Are you sure?')) return;
    try {
      await deleteProduct(id);
    } catch (err) {
      // Error is already handled in store
    }
  };

  if (loading) return <div className="text-center py-4">Loading...</div>;

  return (
    <div className="overflow-x-auto">
      {error && <div className="mb-4 p-4 bg-red-100 text-red-700 rounded">{error}</div>}
      <table className="w-full border-collapse border border-gray-300">
        <thead className="bg-gray-100">
          <tr>
            <th className="border border-gray-300 px-4 py-2 text-left">Name</th>
            <th className="border border-gray-300 px-4 py-2 text-left">Description</th>
            <th className="border border-gray-300 px-4 py-2 text-right">Price</th>
            <th className="border border-gray-300 px-4 py-2 text-right">Quantity</th>
            <th className="border border-gray-300 px-4 py-2 text-center">Actions</th>
          </tr>
        </thead>
        <tbody>
          {products.map(product => (
            <tr key={product.id} className="hover:bg-gray-50">
              <td className="border border-gray-300 px-4 py-2">{product.name}</td>
              <td className="border border-gray-300 px-4 py-2">{product.description || '-'}</td>
              <td className="border border-gray-300 px-4 py-2 text-right">${product.price}</td>
              <td className="border border-gray-300 px-4 py-2 text-right">{product.quantity}</td>
              <td className="border border-gray-300 px-4 py-2 text-center">
                <button
                  onClick={() => onEdit(product.id)}
                  className="mr-2 px-3 py-1 bg-blue-500 text-white rounded hover:bg-blue-600"
                >
                  Edit
                </button>
                <button
                  onClick={() => handleDelete(product.id)}
                  className="px-3 py-1 bg-red-500 text-white rounded hover:bg-red-600"
                >
                  Delete
                </button>
              </td>
            </tr>
          ))}
        </tbody>
      </table>
      {products.length === 0 && !error && <div className="text-center py-4 text-gray-500">No products found</div>}
    </div>
  );
};


```

### 3.6 Create Main App

Edit `frontend-react/src/App.tsx`:

```typescript
import React from 'react';
import { useProductStore } from './store/productStore';
import { ProductForm } from './components/ProductForm';
import { ProductList } from './components/ProductList';
import './App.css';

function App() {
  const [selectedProductId, setSelectedProductId] = React.useState<number | null>(null);
  
  const selectProduct = useProductStore((state) => state.selectProduct);
  const products = useProductStore((state) => state.products);

  const handleFormSuccess = () => {
    setSelectedProductId(null);
  };

  const handleEditProduct = (id: number) => {
    const product = products.find((p) => p.id === id);
    if (product) {
      selectProduct(product);
      setSelectedProductId(id);
    }
  };

  return (
    <div className="min-h-screen bg-gray-100">
      <nav className="bg-blue-600 text-white p-4 shadow">
        <h1 className="text-3xl font-bold">Product Management</h1>
        <p className="text-blue-100">React CRUD Application</p>
      </nav>
      
      <div className="container mx-auto py-8 px-4">
        <ProductForm productId={selectedProductId} onSuccess={handleFormSuccess} />
        <div className="bg-white p-6 rounded-lg shadow">
          <h2 className="text-2xl font-bold mb-4">Products</h2>
          <ProductList onEdit={handleEditProduct} />
        </div>
      </div>
    </div>
  );
}

export default App;

```

---

## Step 4: Create Frontend (Angular 17+)

### 4.1 Create Angular App

```bash
# Create Angular app
npm install -g @angular/cli
ng new frontend-angular --routing --style=css

cd frontend-angular
```

### 4.2 Install Dependencies

```bash
# Install NgRx for state management
npm install @ngrx/store@19 @ngrx/effects@19 @ngrx/entity@19 @ngrx/store-devtools@19

# Install Tailwind CSS
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p


```

### 4.2 Configure Tailwind CSS



Edit `frontend-angular/src/styles.css`:

```css
@import "tailwindcss";
```
Edit `frontend-angular\postcss.config.js`:

```css
module.exports = {
  plugins: {
    '@tailwindcss/postcss': {},
    autoprefixer: {},
  },
}

```

### 4.3 Configure Application


Create `frontend-angular/src/app/app.routes.ts`:

```typescript
import { Routes } from '@angular/router';

export const appRoutes: Routes = [
  {
    path: '',
    redirectTo: '/products',
    pathMatch: 'full'
  },
  {
    path: 'products',
    loadChildren: () => import('./products/products.routes').then(m => m.productsRoutes)
  }
];

```
Create `frontend-angular/src/app/app.config.ts`:

```typescript
import { ApplicationConfig } from '@angular/core';
import { provideHttpClient } from '@angular/common/http';
import { provideRouter } from '@angular/router';
import { provideStore } from '@ngrx/store';
import { provideEffects } from '@ngrx/effects';
import { productReducer } from './store/product.reducer';
import { ProductEffects } from './store/product.effects';
import { appRoutes } from './app.routes';

export const appConfig: ApplicationConfig = {
  providers: [
    provideRouter(appRoutes),
    provideHttpClient(),
    provideStore({ products: productReducer }),
    provideEffects([ProductEffects])
  ]
};



### 4.4 Configure Server (Optional SSR)

Create `frontend-angular/server.ts`:

```typescript
import 'zone.js/node';
import express from 'express';
import { fileURLToPath } from 'node:url';
import { dirname, join, resolve } from 'node:path';
import { renderApplication } from '@angular/platform-server';
import { APP_BASE_HREF } from '@angular/common';
import bootstrap from './src/main.server'; // bootstrap function

export function app(): express.Express {
  const server = express();

  const serverDistFolder = dirname(fileURLToPath(import.meta.url));
  const browserDistFolder = resolve(serverDistFolder, '../browser');

  // Serve static files
  server.get('*.*', express.static(browserDistFolder, { maxAge: '1y' }));

  // Angular SSR route handling
  server.get('*', async (req, res, next) => {
    try {
      const html = await renderApplication(bootstrap, {
        document: join(browserDistFolder, 'index.html'),
        url: req.originalUrl,
        platformProviders: [
          { provide: APP_BASE_HREF, useValue: req.baseUrl },
        ],
      });

      res.send(html);
    } catch (err) {
      next(err);
    }
  });

  return server;
}

function run(): void {
  const port = process.env['PORT'] || 4000;
  const server = app();
  server.listen(port, () => {
    console.log(`✅ Angular SSR server running on http://localhost:${port}`);
  });
}

run();

```
### 4.5 Update Main App Component

Create `frontend-angular/src/app/app.component.ts`:

```typescript
import { Component } from '@angular/core';
import { RouterOutlet, RouterLink } from '@angular/router';
import { CommonModule } from '@angular/common';

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [RouterOutlet, RouterLink, CommonModule],
  template: `
    <div class="min-h-screen bg-gradient-to-br from-slate-900 via-purple-900 to-slate-900">
      <!-- Navigation Bar -->
      <nav class="backdrop-blur-md bg-black/30 border-b border-purple-500/20 sticky top-0 z-50">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <div class="flex justify-between items-center h-16">
            <div class="flex items-center space-x-3">
              <div class="w-10 h-10 bg-gradient-to-br from-purple-400 to-pink-600 rounded-lg flex items-center justify-center">
                <span class="text-white font-bold text-lg">P</span>
              </div>
              <h1 class="text-2xl font-bold bg-gradient-to-r from-purple-400 via-pink-400 to-purple-400 bg-clip-text text-transparent">ProductHub</h1>
            </div>
            <div class="hidden md:flex space-x-8">
              <a routerLink="/products" class="text-gray-300 hover:text-white transition-colors duration-200">Products</a>
            </div>
          </div>
        </div>
      </nav>







      <!-- Router Outlet -->
      <router-outlet></router-outlet>

    </div>
  `,
  styleUrl: './app.component.css'
})
export class AppComponent {
  title = 'ProductHub';
}

```
### 4.6 Create NgRx Store Structure

#### 4.6.1 Product Actions

Create `frontend-angular/src/app/store/product.actions.ts`:

```typescript
import { createAction, props } from '@ngrx/store';
import { Product } from '../services/product.service';

// Load Products
export const loadProducts = createAction(
  '[Product Page] Load Products'
);

export const loadProductsSuccess = createAction(
  '[Product API] Load Products Success',
  props<{ products: Product[] }>()
);

export const loadProductsFailure = createAction(
  '[Product API] Load Products Failure',
  props<{ error: string }>()
);

// Load Single Product
export const loadProduct = createAction(
  '[Product Details Page] Load Product',
  props<{ id: number }>()
);

export const loadProductSuccess = createAction(
  '[Product API] Load Product Success',
  props<{ product: Product }>()
);

export const loadProductFailure = createAction(
  '[Product API] Load Product Failure',
  props<{ error: string }>()
);

// Create Product
export const createProduct = createAction(
  '[Product Create Page] Create Product',
  props<{ product: Omit<Product, 'id' | 'created_at' | 'updated_at'> }>()
);

export const createProductSuccess = createAction(
  '[Product API] Create Product Success',
  props<{ product: Product }>()
);

export const createProductFailure = createAction(
  '[Product API] Create Product Failure',
  props<{ error: string }>()
);

// Update Product
export const updateProduct = createAction(
  '[Product Edit Page] Update Product',
  props<{ id: number; product: Partial<Omit<Product, 'id' | 'created_at' | 'updated_at'>> }>()
);

export const updateProductSuccess = createAction(
  '[Product API] Update Product Success',
  props<{ product: Product }>()
);

export const updateProductFailure = createAction(
  '[Product API] Update Product Failure',
  props<{ error: string }>()
);

// Delete Product
export const deleteProduct = createAction(
  '[Product Delete] Delete Product',
  props<{ id: number }>()
);

export const deleteProductSuccess = createAction(
  '[Product API] Delete Product Success',
  props<{ id: number }>()
);

export const deleteProductFailure = createAction(
  '[Product API] Delete Product Failure',
  props<{ error: string }>()
);

// Clear Selection
export const clearProductSelection = createAction(
  '[Product] Clear Selection'
);



```
#### 4.6.2 Product Effects

Create `frontend-angular/src/app/store/product.effects.ts`:

```typescript
import { Injectable, inject } from '@angular/core';
import { Actions, createEffect, ofType } from '@ngrx/effects';
import { catchError, map, switchMap } from 'rxjs/operators';
import { of } from 'rxjs';
import { ProductService } from '../services/product.service';
import * as ProductActions from './product.actions';

@Injectable()
export class ProductEffects {
  // Standalone injection
  private actions$ = inject(Actions);
  private productService = inject(ProductService);

  // Load all products
  loadProducts$ = createEffect(() =>
    this.actions$.pipe(
      ofType(ProductActions.loadProducts),
      switchMap(() =>
        this.productService.getAll().pipe(
          map((response) =>
            ProductActions.loadProductsSuccess({
              products: response.data || [],
            })
          ),
          catchError((error) =>
            of(
              ProductActions.loadProductsFailure({
                error: error.error?.message || 'Failed to load products',
              })
            )
          )
        )
      )
    )
  );

  // Load single product by id
  loadProduct$ = createEffect(() =>
    this.actions$.pipe(
      ofType(ProductActions.loadProduct),
      switchMap(({ id }) =>
        this.productService.getById(id).pipe(
          map((response) =>
            ProductActions.loadProductSuccess({
              product: response.data!,
            })
          ),
          catchError((error) =>
            of(
              ProductActions.loadProductFailure({
                error: error.error?.message || 'Failed to load product',
              })
            )
          )
        )
      )
    )
  );

  // Create product
  createProduct$ = createEffect(() =>
    this.actions$.pipe(
      ofType(ProductActions.createProduct),
      switchMap(({ product }) =>
        this.productService.create(product).pipe(
          map((response) =>
            ProductActions.createProductSuccess({
              product: response.data!,
            })
          ),
          catchError((error) =>
            of(
              ProductActions.createProductFailure({
                error: error.error?.message || 'Failed to create product',
              })
            )
          )
        )
      )
    )
  );

  // Update product
  updateProduct$ = createEffect(() =>
    this.actions$.pipe(
      ofType(ProductActions.updateProduct),
      switchMap(({ id, product }) =>
        this.productService.update(id, product).pipe(
          map((response) =>
            ProductActions.updateProductSuccess({
              product: response.data!,
            })
          ),
          catchError((error) =>
            of(
              ProductActions.updateProductFailure({
                error: error.error?.message || 'Failed to update product',
              })
            )
          )
        )
      )
    )
  );

  // Delete product
  deleteProduct$ = createEffect(() =>
    this.actions$.pipe(
      ofType(ProductActions.deleteProduct),
      switchMap(({ id }) =>
        this.productService.delete(id).pipe(
          map(() =>
            ProductActions.deleteProductSuccess({
              id,
            })
          ),
          catchError((error) =>
            of(
              ProductActions.deleteProductFailure({
                error: error.error?.message || 'Failed to delete product',
              })
            )
          )
        )
      )
    )
  );
}


```
#### 4.6.3 Product Reducer

Create `frontend-angular/src/app/store/product.reducer.ts`:

```typescript
import { createReducer, on } from '@ngrx/store';
import { Product } from '../services/product.service';
import * as ProductActions from './product.actions';

export interface ProductState {
  products: Product[];
  selectedProduct: Product | null;
  loading: boolean;
  error: string | null;
}

export const initialState: ProductState = {
  products: [],
  selectedProduct: null,
  loading: false,
  error: null,
};

export const productReducer = createReducer(
  initialState,
  
  // Load Products
  on(ProductActions.loadProducts, (state: ProductState) => ({
    ...state,
    loading: true,
    error: null,
  })),
  on(ProductActions.loadProductsSuccess, (state: ProductState, { products }: { products: Product[] }) => ({
    ...state,
    products,
    loading: false,
  })),
  on(ProductActions.loadProductsFailure, (state: ProductState, { error }: { error: string }) => ({
    ...state,
    error,
    loading: false,
  })),
  
  // Load Single Product
  on(ProductActions.loadProduct, (state: ProductState) => ({
    ...state,
    loading: true,
    error: null,
  })),
  on(ProductActions.loadProductSuccess, (state: ProductState, { product }: { product: Product }) => ({
    ...state,
    selectedProduct: product,
    loading: false,
  })),
  on(ProductActions.loadProductFailure, (state: ProductState, { error }: { error: string }) => ({
    ...state,
    error,
    loading: false,
  })),
  
  // Create Product
  on(ProductActions.createProduct, (state: ProductState) => ({
    ...state,
    loading: true,
    error: null,
  })),
  on(ProductActions.createProductSuccess, (state: ProductState, { product }: { product: Product }) => ({
    ...state,
    products: [...state.products, product],
    loading: false,
  })),
  on(ProductActions.createProductFailure, (state: ProductState, { error }: { error: string }) => ({
    ...state,
    error,
    loading: false,
  })),
  
  // Update Product
  on(ProductActions.updateProduct, (state: ProductState) => ({
    ...state,
    loading: true,
    error: null,
  })),
  on(ProductActions.updateProductSuccess, (state: ProductState, { product }: { product: Product }) => ({
    ...state,
    products: state.products.map((p: Product) => (p.id === product.id ? product : p)),
    selectedProduct: product,
    loading: false,
  })),
  on(ProductActions.updateProductFailure, (state: ProductState, { error }: { error: string }) => ({
    ...state,
    error,
    loading: false,
  })),
  
  // Delete Product
  on(ProductActions.deleteProduct, (state: ProductState) => ({
    ...state,
    loading: true,
    error: null,
  })),
  on(ProductActions.deleteProductSuccess, (state: ProductState, { id }: { id: number }) => ({
    ...state,
    products: state.products.filter((p: Product) => p.id !== id),
    selectedProduct: state.selectedProduct?.id === id ? null : state.selectedProduct,
    loading: false,
  })),
  on(ProductActions.deleteProductFailure, (state: ProductState, { error }: { error: string }) => ({
    ...state,
    error,
    loading: false,
  })),
  
  // Clear Selection
  on(ProductActions.clearProductSelection, (state: ProductState) => ({
    ...state,
    selectedProduct: null,
  }))
);



```
#### 4.6.4 Product Selectors

Create `frontend-angular/src/app/store/product.selectors.ts`:

```typescript

import { createFeatureSelector, createSelector } from '@ngrx/store';
import { ProductState } from './product.reducer';

export const selectProductFeature = createFeatureSelector<ProductState>('products');

export const selectAllProducts = createSelector(
  selectProductFeature,
  (state: ProductState) => state.products
);

export const selectSelectedProduct = createSelector(
  selectProductFeature,
  (state: ProductState) => state.selectedProduct
);

export const selectLoadingState = createSelector(
  selectProductFeature,
  (state: ProductState) => state.loading
);

export const selectErrorState = createSelector(
  selectProductFeature,
  (state: ProductState) => state.error
);

export const selectProductById = (id: number) =>
  createSelector(
    selectAllProducts,
    (products) => products.find((p) => p.id === id) || null
  );

export const selectProductCount = createSelector(
  selectAllProducts,
  (products) => products.length
);

export const selectProductsWithLoading = createSelector(
  selectAllProducts,
  selectLoadingState,
  (products, loading) => ({ products, loading })
);


```
### 4.7 Create API Service

```bash
cd frontend-angular
ng generate service services/product
```

Edit `frontend-angular/src/app/services/product.service.ts`:

```typescript
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';

export interface Product {
  id: number;
  name: string;
  description: string | null;
  price: number;
  quantity: number;
  created_at: string;
  updated_at: string;
}

export interface ApiResponse<T> {
  success: boolean;
  message: string;
  data?: T;
}

@Injectable({
  providedIn: 'root'
})
export class ProductService {
  private apiUrl = 'http://localhost:8000/api/v1/products';

  constructor(private http: HttpClient) { }

  getAll(): Observable<ApiResponse<Product[]>> {
    return this.http.get<ApiResponse<Product[]>>(this.apiUrl);
  }

  getById(id: number): Observable<ApiResponse<Product>> {
    return this.http.get<ApiResponse<Product>>(`${this.apiUrl}/${id}`);
  }

  create(product: Omit<Product, 'id' | 'created_at' | 'updated_at'>): Observable<ApiResponse<Product>> {
    return this.http.post<ApiResponse<Product>>(this.apiUrl, product);
  }

  update(id: number, product: Partial<Omit<Product, 'id' | 'created_at' | 'updated_at'>>): Observable<ApiResponse<Product>> {
    return this.http.put<ApiResponse<Product>>(`${this.apiUrl}/${id}`, product);
  }

  delete(id: number): Observable<ApiResponse<void>> {
    return this.http.delete<ApiResponse<void>>(`${this.apiUrl}/${id}`);
  }
}

```
### 4.8 Create Product Routes

Create `frontend-angular/src/app/products/products.routes.ts`:

```typescript
import { Routes } from '@angular/router';
import { ProductListComponent } from '../components/product-list/product-list.component';
import { ProductFormComponent } from '../components/product-form/product-form.component';

export const productsRoutes: Routes = [
  {
    path: '',
    component: ProductListComponent,
  },
  {
    path: 'create',
    component: ProductFormComponent,
  },
  {
    path: 'edit/:id',
    component: ProductFormComponent,
  }
];



```
### 4.9 Create Components

```bash
cd frontend-angular
ng generate component components/product-form
ng generate component components/product-list
```

Edit `frontend-angular/src/app/components/product-form/product-form.component.ts`:

```typescript
import { Component, OnInit, OnDestroy, Optional } from '@angular/core';
import { CommonModule } from '@angular/common';
import { FormsModule, ReactiveFormsModule, FormBuilder, FormGroup, Validators } from '@angular/forms';
import { ActivatedRoute, Router, RouterLink } from '@angular/router';
import { Store } from '@ngrx/store';
import { Observable, Subject, filter, takeUntil } from 'rxjs';
import { Product } from '../../services/product.service';
import * as ProductActions from '../../store/product.actions';
import { selectLoadingState, selectErrorState, selectSelectedProduct } from '../../store/product.selectors';

@Component({
  selector: 'app-product-form',
  standalone: true,
  imports: [CommonModule, FormsModule, ReactiveFormsModule, RouterLink],
  templateUrl: './product-form.component.html',
  styleUrl: './product-form.component.css'
})
export class ProductFormComponent implements OnInit, OnDestroy {
  form: FormGroup;
  loading$: Observable<boolean>;
  error$: Observable<string | null>;
  selectedProduct$: Observable<Product | null>;
  productId: number | null = null;
  private destroy$ = new Subject<void>();

  constructor(
    private fb: FormBuilder,
    private store: Store,
    @Optional() private route: ActivatedRoute | null,
    @Optional() private router: Router | null
  ) {
    this.form = this.fb.group({
      name: ['', [Validators.required, Validators.maxLength(255)]],
      description: [''],
      price: ['', [Validators.required, Validators.min(0)]],
      quantity: ['', [Validators.required, Validators.min(0)]],
    });

    this.loading$ = this.store.select(selectLoadingState);
    this.error$ = this.store.select(selectErrorState);
    this.selectedProduct$ = this.store.select(selectSelectedProduct);
  }

  ngOnInit(): void {
    if (this.route) {
      this.route.params.pipe(takeUntil(this.destroy$)).subscribe(params => {
        if (params['id']) {
          this.productId = +params['id']; // Convert to number
          
          // Clear previous selection and load the product
          this.store.dispatch(ProductActions.clearProductSelection());
          this.store.dispatch(ProductActions.loadProduct({ id: this.productId }));
          
          // Subscribe to the selected product to populate the form
          this.selectedProduct$.pipe(
            filter(product => !!product),
            takeUntil(this.destroy$)
          ).subscribe(product => {
            console.log('Route product:', product);
            if (product && product.id === this.productId) {
              this.form.patchValue({
                name: product.name,
                description: product.description || '',
                price: product.price.toString(),
                quantity: product.quantity.toString(),
              });
            }
          });
        } else {
          // Clear selection for create mode
          this.store.dispatch(ProductActions.clearProductSelection());
          this.productId = null;
          this.form.reset();
        }
      });
    } else {
      this.form.reset();
    }
  }

  ngOnDestroy(): void {
    this.destroy$.next();
    this.destroy$.complete();
  }

  onSubmit(): void {
    if (!this.form.valid) return;

    const data = this.form.value;

    if (this.productId) {
      this.store.dispatch(ProductActions.updateProduct({ id: this.productId, product: data }));
    } else {
      this.store.dispatch(ProductActions.createProduct({ product: data }));
    }

    // Better way to handle navigation after success
    this.loading$.pipe(
      filter(loading => !loading),
      takeUntil(this.destroy$)
    ).subscribe(() => {
      this.error$.pipe(
        takeUntil(this.destroy$)
      ).subscribe(error => {
        if (!error) {
          if (this.router) {
            this.router.navigate(['/products']);
          }
        }
      });
    });
  }
}


```

Edit `frontend-angular/src/app/components/product-form/product-form.component.html`:

```html
<div class="min-h-screen bg-gray-50 py-12">
  <div class="max-w-2xl mx-auto px-4 sm:px-6 lg:px-8">
    <!-- Back Button -->
    <a routerLink="/products" class="inline-flex items-center text-black-400 hover:text-black-300 mb-8 transition-colors duration-200">
      <span class="mr-2">←</span>
      Back to Products
    </a>

    <!-- Form Card -->
    <div class="bg-black backdrop-blur border border-purple-500/20 rounded-lg p-8">
      <h2 class="text-3xl font-bold text-white mb-2">
        {{ (selectedProduct$ | async) ? '✏️ Edit Product' : '✨ Create New Product' }}
      </h2>
      <p class="text-gray-400 mb-8">{{ (selectedProduct$ | async) ? 'Update product details' : 'Add a new product to your catalog' }}</p>

      <!-- Error Message -->
      <div *ngIf="error$ | async as error" class="mb-6 p-4 bg-red-500/10 border border-red-500/30 rounded-lg text-red-400 backdrop-blur">
        <div class="flex items-center">
          <span class="mr-3">⚠️</span>
          <span>{{ error }}</span>
        </div>
      </div>

      <form [formGroup]="form" (ngSubmit)="onSubmit()" class="space-y-6">
        <!-- Product Name -->
        <div>
          <label class="block text-white font-semibold mb-2">Product Name *</label>
          <input
            type="text"
            formControlName="name"
            placeholder="Enter product name"
            class="w-full px-4 py-2 bg-black/50 border border-purple-500/30 rounded-lg text-white placeholder-gray-500 focus:outline-none focus:border-purple-500/50 focus:ring-2 focus:ring-purple-500/20 transition-all duration-200"
          />
          <div *ngIf="form.get('name')?.errors && form.get('name')?.touched" class="mt-2 text-red-400 text-sm flex items-center">
            <span class="mr-2">❌</span>
            Name is required
          </div>
        </div>

        <!-- Description -->
        <div>
          <label class="block text-white font-semibold mb-2">Description</label>
          <textarea
            formControlName="description"
            placeholder="Enter product description (optional)"
            rows="4"
            class="w-full px-4 py-2 bg-black/50 border border-purple-500/30 rounded-lg text-white placeholder-gray-500 focus:outline-none focus:border-purple-500/50 focus:ring-2 focus:ring-purple-500/20 transition-all duration-200 resize-none"
          ></textarea>
        </div>

        <!-- Price and Quantity Grid -->
        <div class="grid md:grid-cols-2 gap-6">
          <!-- Price -->
          <div>
            <label class="block text-white font-semibold mb-2">Price ($) *</label>
            <input
              type="number"
              formControlName="price"
              placeholder="0.00"
              step="0.01"
              min="0"
              class="w-full px-4 py-2 bg-black/50 border border-purple-500/30 rounded-lg text-white placeholder-gray-500 focus:outline-none focus:border-purple-500/50 focus:ring-2 focus:ring-purple-500/20 transition-all duration-200"
            />
            <div *ngIf="form.get('price')?.errors && form.get('price')?.touched" class="mt-2 text-red-400 text-sm flex items-center">
              <span class="mr-2">❌</span>
              Price is required and must be valid
            </div>
          </div>

          <!-- Quantity -->
          <div>
            <label class="block text-white font-semibold mb-2">Quantity *</label>
            <input
              type="number"
              formControlName="quantity"
              placeholder="0"
              min="0"
              class="w-full px-4 py-2 bg-black/50 border border-purple-500/30 rounded-lg text-white placeholder-gray-500 focus:outline-none focus:border-purple-500/50 focus:ring-2 focus:ring-purple-500/20 transition-all duration-200"
            />
            <div *ngIf="form.get('quantity')?.errors && form.get('quantity')?.touched" class="mt-2 text-red-400 text-sm flex items-center">
              <span class="mr-2">❌</span>
              Quantity is required and must be valid
            </div>
          </div>
        </div>

        <!-- Buttons -->
        <div class="flex gap-4 pt-6 border-t border-purple-500/20">
          <button
            type="submit"
            [disabled]="(loading$ | async) || !form.valid"
            class="flex-1 px-6 py-3 bg-gradient-to-r from-purple-500 to-pink-600 text-white rounded-lg font-semibold hover:shadow-lg hover:shadow-purple-500/50 disabled:opacity-50 disabled:cursor-not-allowed transition-all duration-200"
          >
            <span *ngIf="loading$ | async" class="inline-block mr-2">⏳</span>
            {{ (loading$ | async) ? 'Saving...' : ((selectedProduct$ | async) ? 'Update Product' : 'Create Product') }}
          </button>
          <button
            type="button"
            routerLink="/products"
            class="px-6 py-3 border border-purple-500/30 text-purple-400 rounded-lg font-semibold hover:bg-purple-500/10 transition-colors duration-200"
          >
            Cancel
          </button>
        </div>
      </form>
    </div>
  </div>
</div>


```

Edit `frontend-angular/src/app/components/product-list/product-list.component.ts`:

```typescript
import { Component, OnInit, Optional } from '@angular/core';
import { CommonModule } from '@angular/common';
import { RouterModule, Router } from '@angular/router';
import { Store } from '@ngrx/store';
import { Observable } from 'rxjs';
import { Product } from '../../services/product.service';
import * as ProductActions from '../../store/product.actions';
import { selectAllProducts, selectLoadingState, selectErrorState } from '../../store/product.selectors';

@Component({
  selector: 'app-product-list',
  standalone: true,
  imports: [CommonModule, RouterModule],
  templateUrl: './product-list.component.html',
  styleUrl: './product-list.component.css'
})
export class ProductListComponent implements OnInit {
  products$: Observable<Product[]>;
  loading$: Observable<boolean>;
  error$: Observable<string | null>;

  constructor(
    private store: Store,
    @Optional() private router: Router | null
  ) {
    this.products$ = this.store.select(selectAllProducts);
    this.loading$ = this.store.select(selectLoadingState);
    this.error$ = this.store.select(selectErrorState);
  }

  ngOnInit(): void {
    this.store.dispatch(ProductActions.loadProducts());
  }

  onEdit(product: Product): void {
    if (this.router) {
      this.router.navigate(['/products/edit', product.id]);
    }
  }

  onDelete(id: number): void {
    if (!confirm('Are you sure?')) return;
    this.store.dispatch(ProductActions.deleteProduct({ id }));
  }
}

```

Edit `frontend-angular/src/app/components/product-list/product-list.component.html`:

```html
<div class="min-h-screen bg-gray-50 py-12">
  <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
    <!-- Header -->
    <div class="mb-8">
      <h1 class="text-3xl font-bold text-gray-900 mb-2">Product Catalog</h1>
      <p class="text-gray-600">Manage all your products in one place</p>
    </div>

    <!-- Create Button -->
    <div class="mb-8">
      <a
        routerLink="/products/create"
        class="inline-flex items-center px-6 py-3 bg-blue-600 text-white rounded-lg font-semibold hover:bg-blue-700 transition-all duration-200"
      >
        <span class="mr-2">＋</span>
        Create Product
      </a>
    </div>

    <!-- Error Message -->
    <div
      *ngIf="error$ | async as error"
      class="mb-6 p-4 bg-red-100 border border-red-300 text-red-700 rounded-lg"
    >
      ⚠️ {{ error }}
    </div>

    <!-- Loading State -->
    <div *ngIf="loading$ | async" class="flex items-center justify-center py-12">
      <div class="text-center text-gray-500">Loading products...</div>
    </div>

    <!-- Products Table -->
    <div
      *ngIf="(loading$ | async) === false && (products$ | async) as products"
      class="bg-white border border-gray-200 rounded-lg shadow-sm overflow-hidden"
    >
      <div class="overflow-x-auto">
        <table class="w-full">
          <thead>
            <tr class="bg-gray-100 border-b border-gray-200">
              <th class="px-6 py-4 text-left text-sm font-semibold text-gray-700">
                Product Name
              </th>
              <th class="px-6 py-4 text-left text-sm font-semibold text-gray-700">
                Description
              </th>
              <th class="px-6 py-4 text-right text-sm font-semibold text-gray-700">
                Price
              </th>
              <th class="px-6 py-4 text-right text-sm font-semibold text-gray-700">
                Quantity
              </th>
              <th class="px-6 py-4 text-center text-sm font-semibold text-gray-700">
                Actions
              </th>
            </tr>
          </thead>
          <tbody class="divide-y divide-gray-100">
            <tr
              *ngFor="let product of products"
              class="hover:bg-gray-50 transition-colors duration-150"
            >
              <td class="px-6 py-4 text-gray-900 font-medium">
                {{ product.name }}
              </td>
              <td class="px-6 py-4 text-gray-600 text-sm">
                {{ product.description || '—' }}
              </td>
              <td class="px-6 py-4 text-right text-gray-700">
                ${{ product.price || 0 | number: '1.2-2' }}
              </td>
              <td class="px-6 py-4 text-right text-gray-700">
                {{ product.quantity }}
              </td>
              <td class="px-6 py-4 text-center space-x-2">
                <button
                  (click)="onEdit(product)"
                  class="text-blue-600 hover:underline text-sm font-semibold"
                >
                  Edit
                </button>
                <button
                  (click)="onDelete(product.id)"
                  class="text-red-600 hover:underline text-sm font-semibold"
                >
                  Delete
                </button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

    <!-- Empty State -->
    <div
      *ngIf="(loading$ | async) === false && (products$ | async)?.length === 0"
      class="text-center py-12 text-gray-600"
    >
      <div class="text-5xl mb-3">📦</div>
      <h3 class="text-2xl font-bold text-gray-900 mb-2">
        No Products Found
      </h3>
      <p class="mb-6">Start by creating your first product</p>
      <a
        routerLink="/products/create"
        class="inline-flex items-center px-6 py-3 bg-blue-600 text-white rounded-lg font-semibold hover:bg-blue-700 transition-all duration-200"
      >
        <span class="mr-2">＋</span>
        Create Product
      </a>
    </div>
  </div>
</div>


```



### 4.10 Configure TypeScript

Edit `frontend-angular/tsconfig.json`:

```json
{
  "compileOnSave": false,
  "compilerOptions": {
    "outDir": "./dist/out-tsc",
    "strict": true,
    "noImplicitOverride": true,
    "noPropertyAccessFromIndexSignature": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "skipLibCheck": true,
    "esModuleInterop": true,
    "sourceMap": true,
    "declaration": false,
    "experimentalDecorators": true,
    "moduleResolution": "node",
    "importHelpers": true,
    "target": "ES2022",
    "module": "ES2022",
    "useDefineForClassFields": false,
    "lib": [
      "ES2022",
      "dom"
    ]
  },
  "angularCompilerOptions": {
    "enableI18nLegacyMessageIdFormat": false,
    "strictInjectionParameters": true,
    "strictInputAccessModifiers": true,
    "strictTemplates": true
  }
}
```
---

## Step 5: Root Configuration Files

Create these files in the project root:

### .gitignore

```
# Backend
backend/vendor/
backend/node_modules/
backend/.env
backend/.env.local
backend/database/database.sqlite
backend/dist/

# Frontend React
frontend-react/node_modules/
frontend-react/build/
frontend-react/.env.local
frontend-react/.DS_Store

# Frontend Angular
frontend-angular/node_modules/
frontend-angular/dist/
frontend-angular/.angular/
frontend-angular/.env.local

# IDE
.vscode/
.idea/
*.swp
*.swo
*~

# OS
.DS_Store
Thumbs.db
```

### README.md

```markdown
# [Project Name]

Full-stack CRUD application with Laravel backend and React/Angular frontends.

## Quick Start

See [QUICK_START.md](./QUICK_START.md) for rapid setup.

## Setup

See [SETUP.md](./SETUP.md) for detailed setup instructions.

## Architecture

- **Backend**: Laravel 11+ API (SOLID principles, Repository pattern)
- **Frontend React**: React 18+ with TypeScript and Zustand
- **Frontend Angular**: Angular 17+ with standalone components and NgRx

All components use Tailwind CSS and RESTful API communication.
```

### QUICK_START.md

```markdown
# Quick Start

## Terminal 1: Backend
```bash
cd backend
php artisan serve
```

## Terminal 2: React Frontend
```bash
cd frontend-react
npm start
```

## Terminal 3: Angular Frontend
```bash
cd frontend-angular
ng serve
```

Access:
- Backend API: http://localhost:8000
- React App: http://localhost:3000
- Angular App: http://localhost:4200
```

### WARP.md

Copy the WARP.md from the reference project and adjust project-specific details.

---

## Step 6: Commit to Git

```bash
git add .
git commit -m "Initial project setup with Laravel, React, and Angular"
git branch -M main
git remote add origin <your-repository-url>
git push -u origin main
```

---

## Extending the Project

### Adding a New Resource

Follow this pattern for each new entity (e.g., Category, User, etc.):

#### Backend:
1. Create model and migration
2. Create repository interface
3. Create repository implementation
4. Create service
5. Create form requests
6. Create resource
7. Create controller
8. Register routes
9. Add service provider binding

#### Frontend:
1. Create API methods in service
2. Create components
3. Integrate in main component

### Testing

```bash
# Backend
cd backend
php artisan test

# React
cd frontend-react
npm test

# Angular
cd frontend-angular
ng test
```

---

## Deployment Checklist

- [ ] Set `APP_ENV=production` in backend `.env`
- [ ] Set `APP_DEBUG=false` in backend `.env`
- [ ] Run `composer install --no-dev` in backend
- [ ] Run migrations: `php artisan migrate --force`
- [ ] Cache configuration: `php artisan config:cache`
- [ ] Build React: `npm run build` → deploy `build/`
- [ ] Build Angular: `ng build --configuration production` → deploy `dist/frontend-angular`
- [ ] Use PHP-FPM with Nginx/Apache for backend
- [ ] Set correct CORS origins in production

---

## Resources

- [Laravel Docs](https://laravel.com/docs)
- [React Docs](https://react.dev)
- [Angular Docs](https://angular.io)
- [Tailwind CSS](https://tailwindcss.com)

---

**Created**: Using this comprehensive project creation guide  
**Last Updated**: November 2024











