# Mini E-Commerce Demo Project (MERN Stack)

A lightweight, robust, and modern **Mini E-Commerce Demo Application** built with the **MERN Stack** (MongoDB, Express.js, React.js, Node.js). 

This project is tailored specifically for educational, evaluation, and demonstration purposes. It highlights full-stack integration, secure JSON Web Token (JWT) authentication, role-based authorization (Customer vs. Admin), inventory control, shopping cart management, Cash-on-Delivery (COD) checkout, and order status fulfillment.

---

## 📑 Project Documentation Index

This repository contains comprehensive design specifications and implementation blueprints:

1. [**Architecture & Technical Blueprint**](./ARCHITECTURE.md)
   - High-level architecture, directory layout, security flow, state management, and request lifecycle.
2. [**Database Models & Schema Design**](./DATABASE_MODELS.md)
   - MongoDB schemas, Mongoose models, data relationships, field constraints, validation rules, and indexes.
3. [**API Specification & Endpoints**](./API_DOCUMENTATION.md)
   - Complete REST API documentation covering endpoints, methods, payloads, responses, status codes, and error formats.
4. [**Frontend UI/UX Specification**](./FRONTEND_SPECIFICATION.md)
   - Design guidelines, Tailwind CSS theming, component tree, page breakdowns, client routing, and state stores.
5. [**Demo Flow & Testing Guide**](./DEMO_FLOW_AND_TESTING.md)
   - Step-by-step walkthrough of the 10-step end-to-end demo, manual test scripts, and edge-case handling.

---

## 🚀 Tech Stack

| Layer | Technology | Description |
| :--- | :--- | :--- |
| **Frontend** | React 18 + Vite | Blazing-fast frontend build tooling and component architecture |
| **Styling** | Tailwind CSS | Modern, utility-first responsive styling |
| **Icons & UI** | Lucide React | Clean, consistent icons |
| **HTTP Client** | Axios | Promise-based HTTP client with request/response interceptors |
| **Backend** | Node.js + Express.js | Scalable RESTful API server |
| **Database** | MongoDB + Mongoose | Document-oriented NoSQL database with strict schema modeling |
| **Security** | JWT + bcryptjs | Stateless token authentication and password hashing (10 salt rounds) |

---

## 📂 Project Structure

```text
linkcode_project/
├── client/                     # React + Vite Frontend
│   ├── public/                 # Static assets
│   ├── src/
│   │   ├── assets/             # Brand logos and images
│   │   ├── components/         # Reusable UI & layout components
│   │   │   ├── admin/          # Admin dashboard & management components
│   │   │   ├── common/         # Navbar, Footer, Modal, Toast, Loader
│   │   │   └── products/       # ProductCard, ProductGrid, Filters, Search
│   │   ├── context/            # React Context (AuthContext, CartContext)
│   │   ├── pages/              # Customer & Admin views
│   │   │   ├── admin/          # Admin pages (Dashboard, Categories, Products, Orders)
│   │   │   └── ...             # Home, Products, ProductDetails, Cart, Checkout, etc.
│   │   ├── services/           # Axios instance & API service functions
│   │   ├── App.jsx             # Route definitions & guards
│   │   ├── index.css           # Tailwind CSS directives
│   │   └── main.jsx            # React root mount
│   ├── index.html              # HTML entrypoint
│   ├── tailwind.config.js      # Tailwind configuration
│   └── package.json            # Client dependencies
│
├── server/                     # Node.js + Express Backend
│   ├── config/                 # Database connection (db.js)
│   ├── controllers/            # Route business logic handlers
│   │   ├── authController.js
│   │   ├── categoryController.js
│   │   ├── productController.js
│   │   └── orderController.js
│   ├── middleware/             # Auth, role check, and error handlers
│   │   ├── authMiddleware.js
│   │   └── errorMiddleware.js
│   ├── models/                 # Mongoose schemas
│   │   ├── User.js
│   │   ├── Category.js
│   │   ├── Product.js
│   │   └── Order.js
│   ├── routes/                 # Express API routes
│   │   ├── authRoutes.js
│   │   ├── categoryRoutes.js
│   │   ├── productRoutes.js
│   │   └── orderRoutes.js
│   ├── utils/                  # Helper utilities (generateToken.js)
│   ├── .env.example            # Environment variables template
│   ├── package.json            # Server dependencies
│   └── server.js               # Express application entrypoint
│
├── ARCHITECTURE.md             # System & directory design document
├── DATABASE_MODELS.md          # Database schemas & relationships
├── API_DOCUMENTATION.md        # REST API endpoints & request/response contracts
├── FRONTEND_SPECIFICATION.md   # UI/UX design, Tailwind config, & page breakdown
├── DEMO_FLOW_AND_TESTING.md    # Demo script, test cases, and verification checklist
└── README.md                   # Project overview & quick start guide
```

---

## 🎯 Key Functional Modules

### 1. Authentication & Role-Based Access Control
- **Customer Registration & Login**: Validates name, unique email, and minimum 6-character password.
- **Admin Authentication**: Privileged user account with access to administrative routes.
- **JWT Protection**: Tokens issued on login and sent via `Authorization: Bearer <token>` header.
- **Route Guards**: Client-side protected routes (`ProtectedRoute` and `AdminRoute`) and backend middlewares (`protect`, `adminOnly`).

### 2. Admin Management Dashboard
- **Category CRUD**: Add, edit, delete, and view categories (Name & Description).
- **Product CRUD**: Add, edit, delete, and view products with title, description, price, image URL, category reference, and available stock.
- **Order Management**: View all customer orders with customer info, product details, address, and update status (`Pending`, `Confirmed`, `Shipped`, `Delivered`, `Cancelled`).

### 3. Customer Storefront
- **Catalog & Discovery**: Dynamic category filtering, search-as-you-type, and responsive product cards.
- **Product Details**: Complete product specifications, stock status, and add-to-cart controls.
- **Cart Management**: Real-time quantity adjustment, stock limit guardrails, and persistent storage via `localStorage`.
- **Checkout (COD)**: Shipping address capture (Name, Phone, Address, City, Pincode) with **Cash on Delivery**.
- **Server-Side Price & Stock Verification**: Products are validated against live MongoDB prices and stock at checkout; stock is automatically decremented upon order creation.
- **My Orders**: Real-time order tracking for authenticated customers.

---

## ⚙️ Environment Variables

### Server (`server/.env`)
```env
PORT=5000
MONGO_URI=mongodb://localhost:27017/mini-ecommerce
JWT_SECRET=your_super_secret_jwt_key_here
NODE_ENV=development
CLIENT_URL=http://localhost:5173
```

### Client (`client/.env`)
```env
VITE_API_BASE_URL=http://localhost:5000/api
```

---

## 🛠️ Getting Started (Execution Guide)

### Prerequisites
- [Node.js](https://nodejs.org/) (v18.x or v20.x recommended)
- [MongoDB](https://www.mongodb.com/) (Local instance or MongoDB Atlas connection string)
- Git

### 1. Clone Repository
```bash
git clone https://github.com/priyanka27Jadhav/linkcode_project.git
cd linkcode_project
```

### 2. Backend Setup
```bash
cd server
npm install
cp .env.example .env
# Edit .env with your MongoDB URI and JWT Secret
npm run dev
# Server runs on http://localhost:5000
```

### 3. Frontend Setup
```bash
cd ../client
npm install
npm run dev
# Frontend runs on http://localhost:5173
```

---

## 🔑 Default Demo Credentials

| Role | Email | Password | Access Level |
| :--- | :--- | :--- | :--- |
| **Admin** | `admin@demo.com` | `admin123` | Full Admin Dashboard & Storefront |
| **Customer** | `customer@demo.com` | `customer123` | Storefront, Cart, Checkout, My Orders |

---

## 🚫 Scope Boundaries (Explicit Non-Goals)
To keep this project clean, performant, and focused as a **Mini E-Commerce Demo**, the following complexities are deliberately excluded:
- ❌ External payment gateway integrations (Stripe, Razorpay, PayPal) — **Cash on Delivery only**.
- ❌ Multi-vendor / supplier marketplaces.
- ❌ Customer reviews, ratings, and wishlist.
- ❌ Coupons, promo codes, and discount engines.
- ❌ Complex analytics or PDF invoice generation.
