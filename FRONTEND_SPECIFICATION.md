# Frontend UI/UX Specification

This document defines the user interface architecture, design system, component hierarchy, state management, and page specifications for the React + Vite frontend of the **MERN Mini E-Commerce Demo Project**.

---

## 1. Design System & Styling Guidelines

### 1.1. Core Styling Principles
- **Framework**: Tailwind CSS (Utility-First).
- **Aesthetic**: Modern, clean, minimal, professional, and accessible. No excessive clutter, neon effects, or heavy gradients.
- **Responsiveness**: Mobile-first architecture supporting Mobile (<640px), Tablet (640px - 1024px), and Desktop (>1024px).

### 1.2. Color Palette

| Token | Tailwind Class | Hex / Role | Usage |
| :--- | :--- | :--- | :--- |
| **Primary Brand** | `bg-indigo-600` / `hover:bg-indigo-700` | `#4F46E5` | Buttons, active navigation pills, primary links, focus rings |
| **Neutral Dark** | `text-slate-900` / `bg-slate-900` | `#0F172A` | Primary typography, headers, admin sidebar |
| **Neutral Body** | `text-slate-600` / `text-slate-500` | `#475569` | Body copy, secondary labels, metadata |
| **Surface Light** | `bg-slate-50` / `bg-white` | `#F8FAFC` / `#FFFFFF`| Page backgrounds, card containers, inputs |
| **Borders** | `border-slate-200` | `#E2E8F0` | Card borders, table dividers, input borders |
| **Success** | `bg-emerald-500` / `text-emerald-700` | `#10B981` | In-stock tags, 'Delivered' status, success toasts |
| **Warning** | `bg-amber-500` / `text-amber-700` | `#F59E0B` | Low stock alert, 'Pending' status |
| **Danger** | `bg-rose-500` / `text-rose-700` | `#F43F5E` | Out of stock badge, 'Cancelled' status, delete buttons |
| **Info** | `bg-blue-500` / `text-blue-700` | `#3B82F6` | 'Confirmed' / 'Shipped' status, informational toasts |

---

## 2. Component Hierarchy

```text
<App>
 ├── <AuthProvider>
 │    └── <CartProvider>
 │         ├── <Navbar /> (Public navigation, Search, Cart count badge, User dropdown)
 │         ├── <Toast /> (Global notification banner)
 │         ├── <Routes>
 │         │    ├── Public Routes:
 │         │    │    ├── <Home />
 │         │    │    ├── <Products />
 │         │    │    ├── <ProductDetails />
 │         │    │    ├── <Cart />
 │         │    │    ├── <Login />
 │         │    │    └── <Register />
 │         │    │
 │         │    ├── Customer Protected Routes:
 │         │    │    ├── <Checkout />
 │         │    │    └── <MyOrders />
 │         │    │
 │         │    └── Admin Protected Routes:
 │         │         └── <AdminLayout>
 │         │              ├── <AdminSidebar />
 │         │              ├── <AdminDashboard />
 │         │              ├── <AdminCategories />
 │         │              ├── <AdminProducts />
 │         │              └── <AdminOrders />
 │         └── <Footer />
```

---

## 3. Global State Stores

### 3.1. `AuthContext`
Provides global session handling, token storage, and role-based helpers.

- **State**:
  - `user`: `{ _id, name, email, role }` or `null`
  - `token`: String or `null` (persisted in `localStorage.getItem('token')`)
  - `loading`: Boolean (initial authentication verification)
  - `isAuthenticated`: Derived boolean
  - `isAdmin`: Derived boolean (`user?.role === 'admin'`)
- **Actions**:
  - `login(email, password)`: Sends credentials to `/api/auth/login`, stores token in `localStorage`, updates state.
  - `register(name, email, password, confirmPassword)`: Registers user and logs them in.
  - `logout()`: Clears token from `localStorage`, resets `user` and `token` state, navigates to `/login`.

### 3.2. `CartContext`
Provides persistent shopping cart capabilities synced with `localStorage`.

- **State**:
  - `cart`: Array of items `[{ product: ProductObject, quantity: Number }]`
  - `totalItems`: Total quantity of units in cart
  - `totalPrice`: Total sum calculated from `item.product.price * item.quantity`
- **Actions**:
  - `addToCart(product, quantity = 1)`:
    - Checks if item is already in cart.
    - Ensures `existingQty + quantity <= product.stock`. If exceeded, shows a Warning Toast: *"Cannot add more than available stock"*.
  - `updateQuantity(productId, newQuantity)`:
    - Restricts `1 <= newQuantity <= product.stock`.
  - `removeFromCart(productId)`:
    - Removes line item from cart array.
  - `clearCart()`:
    - Resets cart to `[]` and removes `cart` key from `localStorage`.

---

## 4. Page Breakdown & Detailed Specifications

### 4.1. Public Pages

#### `Home.jsx`
- **Hero Section**: Clean banner featuring call-to-action button *"Shop Now"* linking to `/products`.
- **Category Quick-Links**: Horizontal cards or chips showcasing top categories (Electronics, Fashion, Shoes).
- **Featured / Latest Products**: Responsive 4-column grid rendering latest products fetched from `/api/products?limit=8`.
- **Value Proposition Bar**: Simple 3-column reassurance strip (Free Shipping on orders, Cash on Delivery available, 24/7 Customer Support).

#### `Products.jsx`
- **Top Controls**:
  - Search Input with debounce for typing queries (`?search=...`).
  - Horizontal Category Filter pills: `[All, Electronics, Fashion, Shoes]`. Active pill highlighted in `bg-indigo-600 text-white`.
- **Product Grid**:
  - Mobile: 1 column (`grid-cols-1`)
  - Tablet: 2 columns (`sm:grid-cols-2 lg:grid-cols-3`)
  - Desktop: 4 columns (`xl:grid-cols-4`)
- **Product Card (`ProductCard.jsx`)**:
  - Clean card container with `border border-slate-200 rounded-lg shadow-sm hover:shadow-md transition`.
  - Product Image thumbnail (`h-48 object-cover w-full rounded-t-lg`).
  - Category pill badge.
  - Product Name (truncated to 2 lines).
  - Price formatted in `$XX.XX` or local currency.
  - Stock badge:
    - `stock > 0`: Green text (`In Stock: X`)
    - `stock === 0`: Red badge (`Out of Stock`)
  - "Add to Cart" button: Disabled when `stock === 0`.

#### `ProductDetails.jsx`
- **Two-Column Layout**:
  - Left: Large product image with rounded borders.
  - Right:
    - Category tag.
    - Product title (`text-2xl font-bold text-slate-900`).
    - Price (`text-3xl font-extrabold text-indigo-600`).
    - Stock status indicator.
    - Full product description.
    - Quantity selector (`-` and `+` buttons) bound between `1` and `product.stock`.
    - Primary "Add to Cart" button with cart icon.

#### `Cart.jsx`
- **Cart Line Items Table / List**:
  - Product thumbnail, name, unit price.
  - Quantity controls (`-`, quantity input, `+`). Increments disabled when reaching maximum available stock.
  - Item subtotal (`price * quantity`).
  - Remove button with trash icon.
- **Cart Summary Card (Right sidebar)**:
  - Subtotal calculation.
  - Shipping fee: *"Free (COD)"*.
  - Order Total.
  - "Proceed to Checkout" button (navigates to `/checkout`).
- **Empty State**: If `cart.length === 0`, render `<EmptyState />` with icon, *"Your cart is empty"*, and *"Continue Shopping"* button.

#### `Checkout.jsx` (Protected: Customer Only)
- **Two-Column Layout**:
  - Left: **Shipping Address Form**:
    - Full Name (`input`)
    - Phone Number (`input`)
    - Street Address (`textarea`)
    - City (`input`)
    - Pincode (`input`)
  - Right: **Order Review Card**:
    - Compact list of items, quantities, and prices.
    - Total Amount.
    - Payment Method Badge: **Cash on Delivery (COD)** (pre-selected & locked).
    - "Place Order" button.
- **Submission Action**:
  - Form validation: all fields required.
  - Submits to `POST /api/orders`.
  - On success: clears cart, displays Success Toast, navigates to `/my-orders`.

#### `MyOrders.jsx` (Protected: Customer Only)
- Displays a chronological list of customer's previous orders.
- Each order card contains:
  - Order ID & placement timestamp.
  - Status badge:
    - `Pending`: Yellow badge
    - `Confirmed`: Blue badge
    - `Shipped`: Indigo badge
    - `Delivered`: Emerald green badge
    - `Cancelled`: Red badge
  - Shipping address recap.
  - Items list with thumbnail, product title, quantity, and unit price.
  - Total paid amount.

#### `Login.jsx` & `Register.jsx`
- Centered card on clean slate background.
- Client-side validation:
  - Email format validation.
  - Password minimum 6 characters.
  - Password match verification on registration.
- Quick link to switch between Login and Register.
- Redirects authenticated users away from auth pages to home or dashboard.

---

### 4.2. Admin Dashboard & Management Pages

#### `AdminLayout.jsx`
- Responsive Admin Sidebar:
  - Links: **Dashboard**, **Categories**, **Products**, **Orders**, and **View Storefront**.
  - Logout button at the bottom.
- Main content area with breadcrumbs, action header, and responsive table container.

#### `AdminDashboard.jsx`
- Quick statistic cards:
  - Total Products count
  - Total Categories count
  - Total Orders count
  - Total Revenue generated (sum of non-cancelled orders)
- Recent Orders quick preview table.

#### `AdminCategories.jsx`
- "Add Category" button opening a clean modal dialog.
- Responsive table with columns:
  - Category Name
  - Description
  - Actions (Edit button, Delete button)
- Delete Confirmation Dialog (`ConfirmationModal.jsx`): Prevents accidental deletion.

#### `AdminProducts.jsx`
- "Add Product" button opening modal.
- Responsive table with columns:
  - Image Thumbnail
  - Product Name
  - Category Name
  - Price
  - Current Stock
  - Actions (Edit button, Delete button)
- Modal form includes Name, Description, Price, Image URL, Category selector (fetched from categories API), and Stock.

#### `AdminOrders.jsx`
- Responsive table displaying all customer orders across the platform:
  - Order ID (truncated hash)
  - Customer Name & Email
  - Date & Time
  - Items summary (e.g., "Smartphone x 2, Headphones x 1")
  - Shipping City & Pincode
  - Total Amount
  - Status Dropdown Selector:
    - Select option changes invoke `PATCH /api/admin/orders/:id/status`.
    - Live toast feedback on status update.

---

## 5. UI Feedback & Interaction States

1. **Toast System (`Toast.jsx`)**:
   - Auto-dismissing banners (3 seconds) in top-right corner.
   - Types: `success` (green), `error` (red), `info` (blue), `warning` (amber).
2. **Loading States (`Loader.jsx`)**:
   - Centered SVG spinners during API calls and page transitions.
   - Button loading state: Button text replaced with spinner and disabled during form submit.
3. **Empty States (`EmptyState.jsx`)**:
   - Clean illustration/icon with explanatory title and action button when lists are empty (e.g. "No products found", "Your cart is empty", "No orders yet").
4. **Delete Confirmation Dialog (`ConfirmationModal.jsx`)**:
   - Prompts *"Are you sure you want to delete this product/category? This action cannot be undone."* with Cancel and Confirm buttons.
