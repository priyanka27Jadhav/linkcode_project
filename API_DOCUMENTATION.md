# REST API Documentation

This document describes all REST API endpoints for the **MERN Mini E-Commerce Demo Project**.

- **Base URL**: `http://localhost:5000/api`
- **Content Type**: `application/json`
- **Authentication**: Sent in request headers as `Authorization: Bearer <JWT_TOKEN>`

---

## 📑 Endpoints Summary Table

| Category | Method | Endpoint | Access Level | Description |
| :--- | :--- | :--- | :--- | :--- |
| **Auth** | `POST` | `/api/auth/register` | Public | Register new customer account |
| **Auth** | `POST` | `/api/auth/login` | Public | Authenticate user/admin & return JWT |
| **Categories** | `GET` | `/api/categories` | Public | Get all categories |
| **Categories** | `POST` | `/api/categories` | Admin Only | Create new category |
| **Categories** | `PUT` | `/api/categories/:id` | Admin Only | Update existing category |
| **Categories** | `DELETE` | `/api/categories/:id` | Admin Only | Remove category |
| **Products** | `GET` | `/api/products` | Public | Get products with search & category filter |
| **Products** | `GET` | `/api/products/:id` | Public | Get single product by ID |
| **Products** | `POST` | `/api/products` | Admin Only | Create new product |
| **Products** | `PUT` | `/api/products/:id` | Admin Only | Update existing product |
| **Products** | `DELETE` | `/api/products/:id` | Admin Only | Delete product |
| **Orders** | `POST` | `/api/orders` | Customer | Place new COD order & decrement stock |
| **Orders** | `GET` | `/api/orders/my-orders` | Customer | Get order history of logged-in customer |
| **Orders** | `GET` | `/api/admin/orders` | Admin Only | Get all orders across the platform |
| **Orders** | `PATCH`| `/api/admin/orders/:id/status`| Admin Only | Update status of an order |

---

## 1. Authentication Endpoints

### 1.1. Register Customer
- **Endpoint**: `POST /api/auth/register`
- **Access**: Public

#### Request Body
```json
{
  "name": "Jane Doe",
  "email": "jane@example.com",
  "password": "password123",
  "confirmPassword": "password123"
}
```

#### Validation Rules
- `name`: Required, non-empty.
- `email`: Required, valid email format, unique.
- `password`: Required, minimum 6 characters.
- `confirmPassword`: Must match `password`.

#### Response `201 Created`
```json
{
  "_id": "651a1f9e8a7b6c5d4e3f2a10",
  "name": "Jane Doe",
  "email": "jane@example.com",
  "role": "customer",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

#### Errors
- `400 Bad Request`: `{ "message": "Passwords do not match" }`
- `400 Bad Request`: `{ "message": "User with this email already exists" }`

---

### 1.2. Login (Customer & Admin)
- **Endpoint**: `POST /api/auth/login`
- **Access**: Public

#### Request Body
```json
{
  "email": "jane@example.com",
  "password": "password123"
}
```

#### Response `200 OK`
```json
{
  "_id": "651a1f9e8a7b6c5d4e3f2a10",
  "name": "Jane Doe",
  "email": "jane@example.com",
  "role": "customer",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

#### Errors
- `401 Unauthorized`: `{ "message": "Invalid email or password" }`

---

## 2. Category Endpoints

### 2.1. Get All Categories
- **Endpoint**: `GET /api/categories`
- **Access**: Public

#### Response `200 OK`
```json
[
  {
    "_id": "651a201b8a7b6c5d4e3f2a20",
    "name": "Electronics",
    "description": "Smartphones, laptops, and gadgets",
    "createdAt": "2026-10-02T08:00:00.000Z"
  },
  {
    "_id": "651a202c8a7b6c5d4e3f2a21",
    "name": "Fashion",
    "description": "Clothing, apparel, and accessories",
    "createdAt": "2026-10-02T08:05:00.000Z"
  }
]
```

---

### 2.2. Create Category
- **Endpoint**: `POST /api/categories`
- **Access**: Admin Only (`Authorization: Bearer <ADMIN_TOKEN>`)

#### Request Body
```json
{
  "name": "Shoes",
  "description": "Footwear and sneakers"
}
```

#### Response `201 Created`
```json
{
  "_id": "651a203d8a7b6c5d4e3f2a22",
  "name": "Shoes",
  "description": "Footwear and sneakers",
  "createdAt": "2026-10-02T08:10:00.000Z"
}
```

#### Errors
- `400 Bad Request`: `{ "message": "Category name is required" }`
- `400 Bad Request`: `{ "message": "Category already exists" }`
- `401 Unauthorized`: `{ "message": "Not authorized, token missing or invalid" }`
- `403 Forbidden`: `{ "message": "Not authorized as an admin" }`

---

### 2.3. Update Category
- **Endpoint**: `PUT /api/categories/:id`
- **Access**: Admin Only (`Authorization: Bearer <ADMIN_TOKEN>`)

#### Request Body
```json
{
  "name": "Footwear & Shoes",
  "description": "All kinds of footwear"
}
```

#### Response `200 OK`
```json
{
  "_id": "651a203d8a7b6c5d4e3f2a22",
  "name": "Footwear & Shoes",
  "description": "All kinds of footwear",
  "updatedAt": "2026-10-02T08:15:00.000Z"
}
```

---

### 2.4. Delete Category
- **Endpoint**: `DELETE /api/categories/:id`
- **Access**: Admin Only (`Authorization: Bearer <ADMIN_TOKEN>`)

#### Response `200 OK`
```json
{
  "message": "Category removed successfully"
}
```

#### Errors
- `404 Not Found`: `{ "message": "Category not found" }`

---

## 3. Product Endpoints

### 3.1. Get All Products (Filter & Search)
- **Endpoint**: `GET /api/products`
- **Access**: Public
- **Query Parameters**:
  - `category` *(optional)*: Category ID or name to filter
  - `search` *(optional)*: Search query string matched against name/description

#### Example Request
```http
GET /api/products?category=Electronics&search=phone HTTP/1.1
```

#### Response `200 OK`
```json
[
  {
    "_id": "651a20aa8a7b6c5d4e3f2a30",
    "name": "Smartphone Pro Max",
    "description": "Latest 5G smartphone with 128GB storage",
    "price": 799.99,
    "image": "https://images.unsplash.com/photo-1511707171634-5f897ff02aa9",
    "category": {
      "_id": "651a201b8a7b6c5d4e3f2a20",
      "name": "Electronics"
    },
    "stock": 25,
    "createdAt": "2026-10-02T08:20:00.000Z"
  }
]
```

---

### 3.2. Get Single Product
- **Endpoint**: `GET /api/products/:id`
- **Access**: Public

#### Response `200 OK`
```json
{
  "_id": "651a20aa8a7b6c5d4e3f2a30",
  "name": "Smartphone Pro Max",
  "description": "Latest 5G smartphone with 128GB storage",
  "price": 799.99,
  "image": "https://images.unsplash.com/photo-1511707171634-5f897ff02aa9",
  "category": {
    "_id": "651a201b8a7b6c5d4e3f2a20",
    "name": "Electronics"
  },
  "stock": 25,
  "createdAt": "2026-10-02T08:20:00.000Z"
}
```

---

### 3.3. Create Product
- **Endpoint**: `POST /api/products`
- **Access**: Admin Only (`Authorization: Bearer <ADMIN_TOKEN>`)

#### Request Body
```json
{
  "name": "Wireless Noise Cancelling Headphones",
  "description": "High fidelity audio with 30-hour battery life",
  "price": 149.99,
  "image": "https://images.unsplash.com/photo-1505740420928-5e560c06d30e",
  "category": "651a201b8a7b6c5d4e3f2a20",
  "stock": 40
}
```

#### Validation Rules
- `name`: Required string.
- `description`: Required string.
- `price`: Number >= 0.
- `image`: Non-empty URL string.
- `category`: Valid MongoDB ObjectId referencing Category.
- `stock`: Integer >= 0.

#### Response `201 Created`
```json
{
  "_id": "651a20bb8a7b6c5d4e3f2a31",
  "name": "Wireless Noise Cancelling Headphones",
  "description": "High fidelity audio with 30-hour battery life",
  "price": 149.99,
  "image": "https://images.unsplash.com/photo-1505740420928-5e560c06d30e",
  "category": "651a201b8a7b6c5d4e3f2a20",
  "stock": 40,
  "createdAt": "2026-10-02T08:25:00.000Z"
}
```

---

### 3.4. Update Product
- **Endpoint**: `PUT /api/products/:id`
- **Access**: Admin Only (`Authorization: Bearer <ADMIN_TOKEN>`)

#### Request Body
```json
{
  "price": 129.99,
  "stock": 35
}
```

#### Response `200 OK`
```json
{
  "_id": "651a20bb8a7b6c5d4e3f2a31",
  "name": "Wireless Noise Cancelling Headphones",
  "description": "High fidelity audio with 30-hour battery life",
  "price": 129.99,
  "image": "https://images.unsplash.com/photo-1505740420928-5e560c06d30e",
  "category": "651a201b8a7b6c5d4e3f2a20",
  "stock": 35,
  "updatedAt": "2026-10-02T08:30:00.000Z"
}
```

---

### 3.5. Delete Product
- **Endpoint**: `DELETE /api/products/:id`
- **Access**: Admin Only (`Authorization: Bearer <ADMIN_TOKEN>`)

#### Response `200 OK`
```json
{
  "message": "Product removed successfully"
}
```

---

## 4. Order Endpoints

### 4.1. Place Order (Cash on Delivery)
- **Endpoint**: `POST /api/orders`
- **Access**: Customer Authenticated (`Authorization: Bearer <CUSTOMER_TOKEN>`)

#### Request Body
```json
{
  "products": [
    {
      "product": "651a20aa8a7b6c5d4e3f2a30",
      "quantity": 2
    }
  ],
  "shippingAddress": {
    "name": "Jane Doe",
    "phone": "+1 555-0199",
    "address": "456 Elm Street, Apt 3B",
    "city": "Metropolis",
    "pincode": "10001"
  }
}
```

> **Security Note**: Notice that the client sends **only** `product` (ID) and `quantity`. The backend fetches the authoritative price from MongoDB, multiplies it, validates stock, reduces product stock atomically, and records the order.

#### Response `201 Created`
```json
{
  "_id": "651a211e8a7b6c5d4e3f2a40",
  "user": "651a1f9e8a7b6c5d4e3f2a10",
  "products": [
    {
      "product": "651a20aa8a7b6c5d4e3f2a30",
      "name": "Smartphone Pro Max",
      "price": 799.99,
      "quantity": 2,
      "image": "https://images.unsplash.com/photo-1511707171634-5f897ff02aa9"
    }
  ],
  "totalAmount": 1599.98,
  "shippingAddress": {
    "name": "Jane Doe",
    "phone": "+1 555-0199",
    "address": "456 Elm Street, Apt 3B",
    "city": "Metropolis",
    "pincode": "10001"
  },
  "paymentMethod": "Cash on Delivery",
  "status": "Pending",
  "createdAt": "2026-10-02T08:35:00.000Z"
}
```

#### Errors
- `400 Bad Request`: `{ "message": "No order items provided" }`
- `400 Bad Request`: `{ "message": "Insufficient stock for product: Smartphone Pro Max" }`
- `404 Not Found`: `{ "message": "Product not found: 651a20aa..." }`

---

### 4.2. Get Logged-in Customer Orders ("My Orders")
- **Endpoint**: `GET /api/orders/my-orders`
- **Access**: Customer Authenticated (`Authorization: Bearer <CUSTOMER_TOKEN>`)

#### Response `200 OK`
```json
[
  {
    "_id": "651a211e8a7b6c5d4e3f2a40",
    "products": [
      {
        "product": "651a20aa8a7b6c5d4e3f2a30",
        "name": "Smartphone Pro Max",
        "price": 799.99,
        "quantity": 2,
        "image": "https://images.unsplash.com/photo-1511707171634-5f897ff02aa9"
      }
    ],
    "totalAmount": 1599.98,
    "shippingAddress": {
      "name": "Jane Doe",
      "phone": "+1 555-0199",
      "address": "456 Elm Street, Apt 3B",
      "city": "Metropolis",
      "pincode": "10001"
    },
    "paymentMethod": "Cash on Delivery",
    "status": "Pending",
    "createdAt": "2026-10-02T08:35:00.000Z"
  }
]
```

---

### 4.3. Get All Orders (Admin)
- **Endpoint**: `GET /api/admin/orders`
- **Access**: Admin Only (`Authorization: Bearer <ADMIN_TOKEN>`)

#### Response `200 OK`
```json
[
  {
    "_id": "651a211e8a7b6c5d4e3f2a40",
    "user": {
      "_id": "651a1f9e8a7b6c5d4e3f2a10",
      "name": "Jane Doe",
      "email": "jane@example.com"
    },
    "products": [
      {
        "name": "Smartphone Pro Max",
        "price": 799.99,
        "quantity": 2
      }
    ],
    "totalAmount": 1599.98,
    "shippingAddress": {
      "name": "Jane Doe",
      "phone": "+1 555-0199",
      "city": "Metropolis",
      "pincode": "10001"
    },
    "status": "Pending",
    "createdAt": "2026-10-02T08:35:00.000Z"
  }
]
```

---

### 4.4. Update Order Status (Admin)
- **Endpoint**: `PATCH /api/admin/orders/:id/status`
- **Access**: Admin Only (`Authorization: Bearer <ADMIN_TOKEN>`)

#### Request Body
```json
{
  "status": "Shipped"
}
```

#### Allowed Status Values
`Pending` | `Confirmed` | `Shipped` | `Delivered` | `Cancelled`

#### Response `200 OK`
```json
{
  "_id": "651a211e8a7b6c5d4e3f2a40",
  "status": "Shipped",
  "updatedAt": "2026-10-02T09:00:00.000Z",
  "message": "Order status updated to Shipped"
}
```

#### Errors
- `400 Bad Request`: `{ "message": "Invalid status value" }`
- `404 Not Found`: `{ "message": "Order not found" }`
