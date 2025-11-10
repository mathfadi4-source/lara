# Project Creation Guide

This guide documents how to create a new full-stack CRUD application following the same architecture and best practices as this project.

---

## Prerequisites

Before starting, ensure you have:

- **Node.js** v20.17.0+ ([download](https://nodejs.org))
- **npm** v10.8.2+ (comes with Node.js)
- **PHP** 8.2+ ([download](https://www.php.net/downloads))
- **Composer** ([download](https://getcomposer.org/download))
- **Git** ([download](https://git-scm.com/downloads))

### Verify Installation

```bash
node --version          # v20.17.0 or higher
npm --version           # v10.8.2 or higher
php --version           # 8.2 or higher
composer --version      # Latest version
git --version           # Any recent version
```

---

## Project Structure Template

Create the following directory structure:

```
project-name/
├── backend/                    # Laravel 12 API
├── frontend-react/             # React 19 Frontend
├── frontend-angular/           # Angular 17 Frontend
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

## Step 2: Create Backend (Laravel 12)

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

## Step 3: Create Frontend (React 19)

### 3.1 Create React App

```bash
# Create React app with TypeScript
npx create-react-app frontend-react --template typescript

cd frontend-react
```

### 3.2 Install Dependencies

```bash
# Install Tailwind CSS
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p

# Dependencies are already included:
# - react@^19.2.0
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

export const productApi = {
  async getAll(): Promise<Product[]> {
    const response = await fetch(`${API_URL}/products`);
    const data: ApiResponse<Product[]> = await response.json();
    if (!data.success) throw new Error(data.message);
    return data.data || [];
  },

  async getById(id: number): Promise<Product> {
    const response = await fetch(`${API_URL}/products/${id}`);
    const data: ApiResponse<Product> = await response.json();
    if (!data.success) throw new Error(data.message);
    return data.data!;
  },

  async create(product: Omit<Product, 'id' | 'created_at' | 'updated_at'>): Promise<Product> {
    const response = await fetch(`${API_URL}/products`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(product),
    });
    const data: ApiResponse<Product> = await response.json();
    if (!data.success) throw new Error(data.message);
    return data.data!;
  },

  async update(id: number, product: Partial<Product>): Promise<Product> {
    const response = await fetch(`${API_URL}/products/${id}`, {
      method: 'PUT',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(product),
    });
    const data: ApiResponse<Product> = await response.json();
    if (!data.success) throw new Error(data.message);
    return data.data!;
  },

  async delete(id: number): Promise<void> {
    const response = await fetch(`${API_URL}/products/${id}`, {
      method: 'DELETE',
    });
    const data: ApiResponse<null> = await response.json();
    if (!data.success) throw new Error(data.message);
  },
};
```

### 3.5 Create Components

Create `frontend-react/src/components/ProductForm.tsx`:

```typescript
import { useState } from 'react';
import { Product, productApi } from '../services/api';

interface ProductFormProps {
  product?: Product;
  onSuccess: () => void;
}

export const ProductForm: React.FC<ProductFormProps> = ({ product, onSuccess }) => {
  const [formData, setFormData] = useState({
    name: product?.name || '',
    description: product?.description || '',
    price: product?.price.toString() || '',
    quantity: product?.quantity.toString() || '',
  });
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState('');

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    setLoading(true);
    setError('');

    try {
      const payload = {
        name: formData.name,
        description: formData.description,
        price: parseFloat(formData.price),
        quantity: parseInt(formData.quantity),
      };

      if (product) {
        await productApi.update(product.id, payload);
      } else {
        await productApi.create(payload);
      }

      setFormData({ name: '', description: '', price: '', quantity: '' });
      onSuccess();
    } catch (err) {
      setError(err instanceof Error ? err.message : 'An error occurred');
    } finally {
      setLoading(false);
    }
  };

  return (
    <form onSubmit={handleSubmit} className="bg-white p-6 rounded-lg shadow">
      {error && <div className="mb-4 p-3 bg-red-100 text-red-700 rounded">{error}</div>}

      <div className="mb-4">
        <label className="block text-sm font-medium text-gray-700 mb-1">Name</label>
        <input
          type="text"
          required
          value={formData.name}
          onChange={(e) => setFormData({ ...formData, name: e.target.value })}
          className="w-full px-3 py-2 border border-gray-300 rounded-md"
        />
      </div>

      <div className="mb-4">
        <label className="block text-sm font-medium text-gray-700 mb-1">Description</label>
        <textarea
          value={formData.description}
          onChange={(e) => setFormData({ ...formData, description: e.target.value })}
          className="w-full px-3 py-2 border border-gray-300 rounded-md"
        />
      </div>

      <div className="grid grid-cols-2 gap-4 mb-4">
        <div>
          <label className="block text-sm font-medium text-gray-700 mb-1">Price</label>
          <input
            type="number"
            step="0.01"
            required
            value={formData.price}
            onChange={(e) => setFormData({ ...formData, price: e.target.value })}
            className="w-full px-3 py-2 border border-gray-300 rounded-md"
          />
        </div>
        <div>
          <label className="block text-sm font-medium text-gray-700 mb-1">Quantity</label>
          <input
            type="number"
            required
            value={formData.quantity}
            onChange={(e) => setFormData({ ...formData, quantity: e.target.value })}
            className="w-full px-3 py-2 border border-gray-300 rounded-md"
          />
        </div>
      </div>

      <button
        type="submit"
        disabled={loading}
        className="w-full bg-blue-600 text-white py-2 rounded-md hover:bg-blue-700 disabled:opacity-50"
      >
        {loading ? 'Saving...' : product ? 'Update Product' : 'Create Product'}
      </button>
    </form>
  );
};
```

Create `frontend-react/src/components/ProductList.tsx`:

```typescript
import { Product, productApi } from '../services/api';

interface ProductListProps {
  products: Product[];
  onEdit: (product: Product) => void;
  onDelete: () => void;
}

export const ProductList: React.FC<ProductListProps> = ({ products, onEdit, onDelete }) => {
  const handleDelete = async (id: number) => {
    if (!window.confirm('Are you sure?')) return;
    try {
      await productApi.delete(id);
      onDelete();
    } catch (err) {
      alert(err instanceof Error ? err.message : 'Delete failed');
    }
  };

  return (
    <div className="overflow-x-auto">
      <table className="w-full border-collapse">
        <thead>
          <tr className="bg-gray-100">
            <th className="border p-2 text-left">Name</th>
            <th className="border p-2 text-left">Description</th>
            <th className="border p-2 text-right">Price</th>
            <th className="border p-2 text-right">Quantity</th>
            <th className="border p-2">Actions</th>
          </tr>
        </thead>
        <tbody>
          {products.map((product) => (
            <tr key={product.id} className="hover:bg-gray-50">
              <td className="border p-2">{product.name}</td>
              <td className="border p-2">{product.description}</td>
              <td className="border p-2 text-right">${product.price}</td>
              <td className="border p-2 text-right">{product.quantity}</td>
              <td className="border p-2 text-center">
                <button
                  onClick={() => onEdit(product)}
                  className="text-blue-600 hover:underline mr-2"
                >
                  Edit
                </button>
                <button
                  onClick={() => handleDelete(product.id)}
                  className="text-red-600 hover:underline"
                >
                  Delete
                </button>
              </td>
            </tr>
          ))}
        </tbody>
      </table>
    </div>
  );
};
```

### 3.6 Create Main App

Edit `frontend-react/src/App.tsx`:

```typescript
import { useState, useEffect } from 'react';
import { Product, productApi } from './services/api';
import { ProductForm } from './components/ProductForm';
import { ProductList } from './components/ProductList';
import './App.css';

function App() {
  const [products, setProducts] = useState<Product[]>([]);
  const [selectedProduct, setSelectedProduct] = useState<Product | undefined>();
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState('');

  const loadProducts = async () => {
    setLoading(true);
    try {
      const data = await productApi.getAll();
      setProducts(data);
      setError('');
    } catch (err) {
      setError(err instanceof Error ? err.message : 'Failed to load products');
    } finally {
      setLoading(false);
    }
  };

  useEffect(() => {
    loadProducts();
  }, []);

  return (
    <div className="min-h-screen bg-gray-100">
      <div className="max-w-6xl mx-auto p-8">
        <h1 className="text-4xl font-bold mb-8">Product CRUD</h1>

        <div className="grid grid-cols-1 md:grid-cols-3 gap-8 mb-8">
          <div className="md:col-span-1">
            <ProductForm
              product={selectedProduct}
              onSuccess={() => {
                setSelectedProduct(undefined);
                loadProducts();
              }}
            />
          </div>

          <div className="md:col-span-2">
            {error && <div className="mb-4 p-3 bg-red-100 text-red-700 rounded">{error}</div>}
            {loading ? (
              <div>Loading...</div>
            ) : (
              <ProductList
                products={products}
                onEdit={setSelectedProduct}
                onDelete={loadProducts}
              />
            )}
          </div>
        </div>
      </div>
    </div>
  );
}

export default App;
```

---

## Step 4: Create Frontend (Angular 17)

### 4.1 Create Angular App

```bash
# Create Angular app
ng new frontend-angular --routing --style=css

cd frontend-angular
```

### 4.2 Install Dependencies

```bash
# Install Tailwind CSS
ng add @tailwindcss/ng@latest
```

### 4.3 Create API Service

```bash
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

  update(id: number, product: Partial<Product>): Observable<ApiResponse<Product>> {
    return this.http.put<ApiResponse<Product>>(`${this.apiUrl}/${id}`, product);
  }

  delete(id: number): Observable<ApiResponse<null>> {
    return this.http.delete<ApiResponse<null>>(`${this.apiUrl}/${id}`);
  }
}
```

### 4.4 Create Components

```bash
ng generate component components/product-form
ng generate component components/product-list
```

Edit `frontend-angular/src/app/components/product-form/product-form.component.ts`:

```typescript
import { Component, EventEmitter, Input, OnInit, Output } from '@angular/core';
import { FormBuilder, FormGroup, ReactiveFormsModule, Validators } from '@angular/forms';
import { Product, ProductService } from '../../services/product.service';
import { CommonModule } from '@angular/common';

@Component({
  selector: 'app-product-form',
  templateUrl: './product-form.component.html',
  styleUrls: ['./product-form.component.css'],
  standalone: true,
  imports: [CommonModule, ReactiveFormsModule]
})
export class ProductFormComponent implements OnInit {
  @Input() product?: Product;
  @Output() success = new EventEmitter<void>();

  form!: FormGroup;
  loading = false;
  error = '';

  constructor(
    private fb: FormBuilder,
    private productService: ProductService
  ) {
    this.form = this.fb.group({
      name: ['', [Validators.required, Validators.maxLength(255)]],
      description: [''],
      price: [0, [Validators.required, Validators.min(0)]],
      quantity: [0, [Validators.required, Validators.min(0)]],
    });
  }

  ngOnInit() {
    if (this.product) {
      this.form.patchValue(this.product);
    }
  }

  onSubmit() {
    if (!this.form.valid) return;

    this.loading = true;
    this.error = '';

    const payload = this.form.value;
    const request = this.product
      ? this.productService.update(this.product.id, payload)
      : this.productService.create(payload);

    request.subscribe({
      next: () => {
        this.form.reset();
        this.success.emit();
      },
      error: (err) => {
        this.error = err.error?.message || 'An error occurred';
        this.loading = false;
      },
      complete: () => {
        this.loading = false;
      }
    });
  }
}
```

Edit `frontend-angular/src/app/components/product-form/product-form.component.html`:

```html
<form [formGroup]="form" (ngSubmit)="onSubmit()" class="bg-white p-6 rounded-lg shadow">
  <div *ngIf="error" class="mb-4 p-3 bg-red-100 text-red-700 rounded">
    {{ error }}
  </div>

  <div class="mb-4">
    <label class="block text-sm font-medium text-gray-700 mb-1">Name</label>
    <input
      type="text"
      formControlName="name"
      class="w-full px-3 py-2 border border-gray-300 rounded-md"
    />
  </div>

  <div class="mb-4">
    <label class="block text-sm font-medium text-gray-700 mb-1">Description</label>
    <textarea
      formControlName="description"
      class="w-full px-3 py-2 border border-gray-300 rounded-md"
    ></textarea>
  </div>

  <div class="grid grid-cols-2 gap-4 mb-4">
    <div>
      <label class="block text-sm font-medium text-gray-700 mb-1">Price</label>
      <input
        type="number"
        step="0.01"
        formControlName="price"
        class="w-full px-3 py-2 border border-gray-300 rounded-md"
      />
    </div>
    <div>
      <label class="block text-sm font-medium text-gray-700 mb-1">Quantity</label>
      <input
        type="number"
        formControlName="quantity"
        class="w-full px-3 py-2 border border-gray-300 rounded-md"
      />
    </div>
  </div>

  <button
    type="submit"
    [disabled]="loading"
    class="w-full bg-blue-600 text-white py-2 rounded-md hover:bg-blue-700 disabled:opacity-50"
  >
    {{ loading ? 'Saving...' : (product ? 'Update Product' : 'Create Product') }}
  </button>
</form>
```

Edit `frontend-angular/src/app/components/product-list/product-list.component.ts`:

```typescript
import { Component, EventEmitter, Input, Output } from '@angular/core';
import { CommonModule } from '@angular/common';
import { Product, ProductService } from '../../services/product.service';

@Component({
  selector: 'app-product-list',
  templateUrl: './product-list.component.html',
  styleUrls: ['./product-list.component.css'],
  standalone: true,
  imports: [CommonModule]
})
export class ProductListComponent {
  @Input() products: Product[] = [];
  @Output() edit = new EventEmitter<Product>();
  @Output() refresh = new EventEmitter<void>();

  constructor(private productService: ProductService) {}

  onEdit(product: Product) {
    this.edit.emit(product);
  }

  onDelete(id: number) {
    if (!confirm('Are you sure?')) return;

    this.productService.delete(id).subscribe({
      next: () => this.refresh.emit(),
      error: (err) => alert(err.error?.message || 'Delete failed')
    });
  }
}
```

Edit `frontend-angular/src/app/components/product-list/product-list.component.html`:

```html
<div class="overflow-x-auto">
  <table class="w-full border-collapse">
    <thead>
      <tr class="bg-gray-100">
        <th class="border p-2 text-left">Name</th>
        <th class="border p-2 text-left">Description</th>
        <th class="border p-2 text-right">Price</th>
        <th class="border p-2 text-right">Quantity</th>
        <th class="border p-2">Actions</th>
      </tr>
    </thead>
    <tbody>
      <tr *ngFor="let product of products" class="hover:bg-gray-50">
        <td class="border p-2">{{ product.name }}</td>
        <td class="border p-2">{{ product.description }}</td>
        <td class="border p-2 text-right">${{ product.price }}</td>
        <td class="border p-2 text-right">{{ product.quantity }}</td>
        <td class="border p-2 text-center">
          <button
            (click)="onEdit(product)"
            class="text-blue-600 hover:underline mr-2"
          >
            Edit
          </button>
          <button
            (click)="onDelete(product.id)"
            class="text-red-600 hover:underline"
          >
            Delete
          </button>
        </td>
      </tr>
    </tbody>
  </table>
</div>
```

### 4.5 Update App Component

Edit `frontend-angular/src/app/app.component.ts`:

```typescript
import { Component, OnInit } from '@angular/core';
import { HttpClientModule } from '@angular/common/http';
import { CommonModule } from '@angular/common';
import { Product, ProductService } from './services/product.service';
import { ProductFormComponent } from './components/product-form/product-form.component';
import { ProductListComponent } from './components/product-list/product-list.component';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css'],
  standalone: true,
  imports: [CommonModule, HttpClientModule, ProductFormComponent, ProductListComponent]
})
export class AppComponent implements OnInit {
  title = 'Product CRUD';
  products: Product[] = [];
  selectedProduct?: Product;
  loading = true;
  error = '';

  constructor(private productService: ProductService) {}

  ngOnInit() {
    this.loadProducts();
  }

  loadProducts() {
    this.loading = true;
    this.productService.getAll().subscribe({
      next: (response) => {
        this.products = response.data || [];
        this.error = '';
      },
      error: (err) => {
        this.error = err.error?.message || 'Failed to load products';
      },
      complete: () => {
        this.loading = false;
      }
    });
  }

  onFormSuccess() {
    this.selectedProduct = undefined;
    this.loadProducts();
  }
}
```

Edit `frontend-angular/src/app/app.component.html`:

```html
<div class="min-h-screen bg-gray-100">
  <div class="max-w-6xl mx-auto p-8">
    <h1 class="text-4xl font-bold mb-8">{{ title }}</h1>

    <div class="grid grid-cols-1 md:grid-cols-3 gap-8 mb-8">
      <div class="md:col-span-1">
        <app-product-form
          [product]="selectedProduct"
          (success)="onFormSuccess()"
        ></app-product-form>
      </div>

      <div class="md:col-span-2">
        <div *ngIf="error" class="mb-4 p-3 bg-red-100 text-red-700 rounded">
          {{ error }}
        </div>
        <div *ngIf="loading">Loading...</div>
        <app-product-list
          *ngIf="!loading"
          [products]="products"
          (edit)="selectedProduct = $event"
          (refresh)="loadProducts()"
        ></app-product-list>
      </div>
    </div>
  </div>
</div>
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

- **Backend**: Laravel 12 API (SOLID principles, Repository pattern)
- **Frontend React**: React 19 with TypeScript
- **Frontend Angular**: Angular 17 with standalone components

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
**Last Updated**: November 2025
