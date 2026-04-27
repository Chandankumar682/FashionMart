# 🛍️ TrendMart — Full Stack E-Commerce Documentation

> **Single Vendor E-Commerce Platform** | Java 17 + Spring Boot 3.2 + React 18 + Tailwind CSS + MySQL + JWT

---

## 📋 Table of Contents

1. [Project Overview](#chapter-1--project-overview)
2. [System Architecture](#chapter-2--system-architecture)
3. [Database Design](#chapter-3--database-design)
4. [Backend — Spring Boot](#chapter-4--backend--spring-boot)
5. [API Documentation](#chapter-5--api-documentation)
6. [Frontend — React + Tailwind](#chapter-6--frontend--react--tailwind)
7. [Key Business Logic](#chapter-7--key-business-logic)
8. [Setup & Installation Guide](#chapter-8--setup--installation-guide)
9. [Testing](#chapter-9--testing)
10. [Screenshots](#chapter-10--screenshots)
11. [Challenges & Daily Dev Log](#chapter-11--challenges--daily-dev-log)
12. [Future Scope](#chapter-12--future-scope)
13. [References](#chapter-13--references)

---

## Chapter 1 — Project Overview

### What is TrendMart?

TrendMart is a **single vendor e-commerce web application** built as a full-stack project using Java Spring Boot on the backend and React.js on the frontend. It replicates the core functionality of platforms like Amazon or Flipkart but for a single seller/admin.

### What is Single Vendor E-Commerce?

In a single vendor model, **one admin/seller** manages all products, orders, and inventory. Customers can browse, add to cart, place orders, and pay. Unlike multi-vendor platforms (like Amazon Marketplace), all products belong to one store owner.

### Tech Stack

| Layer | Technology |
|-------|-----------|
| Backend Language | Java 17 |
| Backend Framework | Spring Boot 3.2.4 |
| Security | Spring Security + JWT (jjwt 0.11.5) |
| Database ORM | Spring Data JPA + Hibernate |
| Database | MySQL 8 |
| Email | Spring Mail (Gmail SMTP) |
| Frontend Framework | React 18 |
| Styling | Tailwind CSS |
| State Management | Redux Toolkit |
| HTTP Client | Axios |
| Build Tool (Frontend) | Vite |
| Build Tool (Backend) | Maven |

### Tools Used

| Tool | Purpose |
|------|---------|
| IntelliJ IDEA | Backend development |
| VS Code | Frontend development |
| Postman | API testing |
| MySQL Workbench | Database management |
| Git & GitHub | Version control |

### Project Goals & Features

**User Features:**
- Register, Login, OTP Email Verification
- Browse products by category
- Search & filter products
- Product detail with variants (size, color, etc.)
- Add to Cart / Wishlist
- Place orders with coupon discount
- Multiple saved addresses
- Order tracking & history
- Write product reviews with ratings
- Password reset via OTP

**Admin Features:**
- Admin dashboard with stats
- Manage categories (add/edit/delete)
- Manage products, variants, images
- Manage orders & update status
- Manage coupons
- Manage banners
- View inventory logs

---

## Chapter 2 — System Architecture

### Overall Architecture

```
┌─────────────────┐         ┌──────────────────────┐         ┌──────────────┐
│   React 18      │  HTTP   │   Spring Boot 3.2     │  JDBC   │   MySQL 8    │
│   (Vite)        │◄───────►│   REST API (:8080)    │◄───────►│   TrendMart  │
│   Tailwind CSS  │  JSON   │   Spring Security     │         │   Database   │
│   Redux Toolkit │         │   JWT Auth            │         │   26 Tables  │
└─────────────────┘         └──────────────────────┘         └──────────────┘
        │                            │
        │                            │ Gmail SMTP
        │                    ┌───────▼───────┐
        │                    │  Email Server │
        └────────────────────│  OTP / Notify │
                             └───────────────┘
```

### Request Flow

```
User Action (React)
    │
    ▼
Axios HTTP Request
    │ (with JWT Bearer Token in Header)
    ▼
Spring Security Filter Chain
    │
    ├── JwtAuthFilter (validates token)
    │
    ▼
Controller Layer (@RestController)
    │
    ▼
Service Layer (@Service — business logic)
    │
    ▼
Repository Layer (JpaRepository — DB queries)
    │
    ▼
MySQL Database
    │
    ▼
Response DTO → JSON → Back to React
```

### JWT Auth Flow

```
[1] User sends email + password
          │
          ▼
[2] Spring Security authenticates user
          │
          ▼
[3] JwtService generates:
    ├── Access Token  (expires: 15 min)
    └── Refresh Token (expires: 7 days, stored in DB)
          │
          ▼
[4] Frontend stores tokens (localStorage/memory)
          │
          ▼
[5] Every request: Authorization: Bearer <access_token>
          │
          ▼
[6] If Access Token expires → POST /api/auth/refresh
    with Refresh Token → get new Access Token
```

### Folder Structure

**Backend:**
```
trendmart-backend/
├── src/main/java/com/trendmart/
│   ├── TrendmartBackendApplication.java
│   ├── config/
│   │   ├── SecurityConfig.java
│   │   └── ApplicationConfig.java
│   ├── security/
│   │   ├── JwtService.java
│   │   ├── JwtAuthFilter.java
│   │   └── CustomUserDetailsService.java
│   ├── modules/
│   │   ├── auth/
│   │   │   ├── controller/AuthController.java
│   │   │   ├── service/AuthService.java
│   │   │   └── dto/
│   │   ├── user/
│   │   ├── category/
│   │   ├── product/
│   │   ├── cart/
│   │   ├── wishlist/
│   │   ├── order/
│   │   ├── payment/
│   │   ├── coupon/
│   │   ├── review/
│   │   ├── banner/
│   │   └── admin/
│   ├── exception/
│   │   ├── GlobalExceptionHandler.java
│   │   └── CustomExceptions.java
│   └── common/
│       ├── ApiResponse.java
│       └── PagedResponse.java
├── src/main/resources/
│   └── application.properties
└── pom.xml
```

**Frontend:**
```
trendmart-frontend/
├── src/
│   ├── main.jsx
│   ├── App.jsx
│   ├── api/
│   │   └── axiosInstance.js
│   ├── store/
│   │   ├── store.js
│   │   └── slices/
│   │       ├── authSlice.js
│   │       ├── cartSlice.js
│   │       └── wishlistSlice.js
│   ├── pages/
│   │   ├── Home.jsx
│   │   ├── Login.jsx
│   │   ├── Register.jsx
│   │   ├── OtpVerify.jsx
│   │   ├── ProductList.jsx
│   │   ├── ProductDetail.jsx
│   │   ├── Cart.jsx
│   │   ├── Checkout.jsx
│   │   ├── OrderSuccess.jsx
│   │   ├── MyOrders.jsx
│   │   ├── MyProfile.jsx
│   │   ├── MyAddresses.jsx
│   │   ├── Wishlist.jsx
│   │   └── admin/
│   │       ├── AdminDashboard.jsx
│   │       ├── AdminProducts.jsx
│   │       └── AdminOrders.jsx
│   ├── components/
│   │   ├── Navbar.jsx
│   │   ├── Footer.jsx
│   │   ├── ProductCard.jsx
│   │   ├── Banner.jsx
│   │   └── PrivateRoute.jsx
│   └── routes/
│       └── AppRoutes.jsx
├── public/
├── index.html
├── package.json
├── vite.config.js
└── tailwind.config.js
```

---

## Chapter 3 — Database Design

### ER Diagram (Text Representation)

```
user_roles ──< users >──< refresh_tokens
                 │
                 ├──< email_verifications
                 ├──< password_reset_otp
                 ├──< user_addresses
                 ├──< cart >── product_variants
                 ├──< wishlist >── products
                 ├──< orders >──< order_items >── product_variants
                 │              └──< order_shipping_addresses
                 │              └──< payments
                 └──< product_reviews >── products
                                    └──< review_images

categories (self-ref) ──< products >──< product_images
                                    ├──< product_variants >──< product_variant_attribute_values
                                    │                              └── product_attribute_values
                                    │                                        └── product_attributes
                                    ├──< product_rating_summary
                                    └──< inventory_logs

coupons ──< orders
banners ──< banner_images
```

### All 26 Tables

| # | Table Name | Purpose |
|---|-----------|---------|
| 1 | `user_roles` | ROLE_USER / ROLE_ADMIN |
| 2 | `users` | All user accounts |
| 3 | `refresh_tokens` | JWT refresh tokens (per user) |
| 4 | `email_verifications` | OTP for email verify on register |
| 5 | `password_reset_otp` | OTP for forgot password |
| 6 | `user_addresses` | User saved addresses (multiple) |
| 7 | `categories` | Categories with parent-child (self-ref) |
| 8 | `products` | Product master table |
| 9 | `product_images` | Multiple images per product |
| 10 | `product_attributes` | Attribute types (Size, Color) |
| 11 | `product_attribute_values` | Values (S, M, L / Red, Blue) |
| 12 | `product_variants` | Each SKU with price + stock |
| 13 | `product_variant_attribute_values` | Variant ↔ Attribute mapping |
| 14 | `cart` | User's cart items |
| 15 | `wishlist` | User's wishlist |
| 16 | `coupons` | Discount coupon codes |
| 17 | `orders` | Order master |
| 18 | `order_items` | Items inside each order |
| 19 | `order_shipping_addresses` | Snapshot of address at order time |
| 20 | `payments` | Payment record per order |
| 21 | `product_reviews` | Customer reviews + rating |
| 22 | `review_images` | Images attached to reviews |
| 23 | `product_rating_summary` | Denormalized avg rating (fast reads) |
| 24 | `banners` | Banner groups (HOME_TOP etc.) |
| 25 | `banner_images` | Individual banner slides |
| 26 | `inventory_logs` | Stock change history |

### Key Relationships

- **One-to-Many:** `users` → `orders`, `products` → `product_variants`, `orders` → `order_items`
- **Many-to-Many:** `product_variants` ↔ `product_attribute_values` (via `product_variant_attribute_values`)
- **Self-Referencing:** `categories.parent_id` → `categories.id`
- **Snapshot Pattern:** `order_shipping_addresses` stores address copy at order time (so future address changes don't affect old orders)

### Seed Data Summary

| Data | Count |
|------|-------|
| Roles | 2 (ROLE_USER, ROLE_ADMIN) |
| Parent Categories | 8 (Fashion, Electronics, Beauty, Home, Sports, Toys, Books, Grocery) |
| Child Categories | 8 (Men Shoes, Mobiles, Laptops, etc.) |
| Products | 10 (Nike, iPhone, Dell, etc.) |
| Product Variants | 12 (different SKUs) |
| Rating Summary Init | 10 rows (all 0) |

---

## Chapter 4 — Backend (Spring Boot)

### pom.xml — Key Dependencies

```xml
<!-- Spring Boot Parent: 3.2.4 | Java: 17 -->

spring-boot-starter-web          <!-- REST APIs -->
spring-boot-starter-security     <!-- Auth + Filters -->
spring-boot-starter-data-jpa     <!-- ORM/Hibernate -->
spring-boot-starter-validation   <!-- @Valid annotations -->
spring-boot-starter-mail         <!-- Email/OTP -->
mysql-connector-j                <!-- MySQL driver -->
jjwt-api / jjwt-impl / jjwt-jackson  <!-- JWT 0.11.5 -->
lombok                           <!-- Boilerplate reduction -->
```

### application.properties

```properties
# Server
server.port=8080

# Database
spring.datasource.url=jdbc:mysql://localhost:3306/TrendMart?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true
spring.datasource.username=root
spring.datasource.password=yourpassword

# JPA
spring.jpa.hibernate.ddl-auto=none
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQLDialect

# JWT
app.jwt.secret=trendmart_super_secret_key_minimum_256_bits_long_key_here
app.jwt.access-token-expiry-ms=900000       # 15 minutes
app.jwt.refresh-token-expiry-ms=604800000   # 7 days

# Mail (Gmail SMTP)
spring.mail.host=smtp.gmail.com
spring.mail.port=587
spring.mail.username=youremail@gmail.com
spring.mail.password=your_app_password
spring.mail.properties.mail.smtp.auth=true
spring.mail.properties.mail.smtp.starttls.enable=true

# OTP
app.otp.expiry-minutes=10
```

### Module Breakdown

#### ✅ Module 1 — Auth
**Endpoints:** Register, Login, OTP Verify, Refresh Token, Password Reset (Request + Confirm)
**Key Logic:**
- On register → save user (INACTIVE) → send OTP email
- On OTP verify → mark user ACTIVE
- On login → validate credentials → return access + refresh token
- On refresh → validate DB refresh token → return new access token

#### ✅ Module 2 — User & Address
**Endpoints:** Get Profile, Update Profile, List Addresses, Add/Update/Delete Address, Set Default Address
**Key Logic:**
- User can have multiple addresses
- One address can be marked `is_default = true`

#### ✅ Module 3 — Category
**Endpoints:** List all, Get by slug, Admin CRUD
**Key Logic:**
- Self-referencing table (parent_id = null means top-level)
- Slug must be unique (used in URLs)

#### ✅ Module 4 — Product
**Endpoints:** List, Filter by category, Search, Get by slug, Admin CRUD
**Sub-modules:**
- `product_images` — multiple images, one primary
- `product_variants` — each SKU with price + stock
- `product_attributes` & `product_attribute_values` — Size/Color system
- `product_variant_attribute_values` — maps variant to its attributes

#### ✅ Module 5 — Cart
**Endpoints:** Get cart, Add item, Update quantity, Remove item, Clear cart
**Key Logic:**
- `UNIQUE(user_id, variant_id)` — no duplicate cart entries
- Quantity update replaces existing row

#### ✅ Module 6 — Wishlist
**Endpoints:** Get wishlist, Add product, Remove product
**Key Logic:**
- `UNIQUE(user_id, product_id)` — no duplicates

#### ✅ Module 7 — Order
**Endpoints:** Place order, My orders list, Order detail, Cancel order
**Key Logic:**
- Validate stock → deduct stock → create order → clear cart
- Coupon applied at order time (discount calculated)
- Shipping address snapshot saved separately

#### ✅ Module 8 — Payment
**Endpoints:** Initiate payment, Payment callback/confirm
**Key Logic:**
- COD: payment_status = PAID immediately
- Razorpay/Stripe: PENDING → callback → PAID/FAILED

#### ✅ Module 9 — Coupon
**Endpoints:** Validate coupon (user), Admin CRUD
**Key Logic:**
- Check: active, within date range, min_order_amount met, max_uses not exceeded
- FIXED discount or PERCENTAGE (capped at max_discount_amount)
- On use: `used_count++`

#### ✅ Module 10 — Review
**Endpoints:** Add review, Get product reviews, My reviews, Admin delete
**Key Logic:**
- One review per user per product
- After save/update → recalculate `product_rating_summary`

#### ✅ Module 11 — Banner
**Endpoints:** Get active banners by position, Admin CRUD
**Key Logic:**
- Positions: HOME_TOP, HOME_MIDDLE, OFFER_SECTION
- Active = `is_active=true` AND `start_date <= today <= end_date`

#### ✅ Module 12 — Admin APIs
**Endpoints:** Dashboard stats, User list/status change, Order status update, Inventory restock
**Key Logic:**
- Role-based: only ROLE_ADMIN can access `/api/admin/**`
- Dashboard: count users, orders, revenue, low stock alerts

### Exception Handling

```java
// GlobalExceptionHandler.java (@RestControllerAdvice)

ResourceNotFoundException  → 404
ValidationException        → 400
UnauthorizedException      → 401
BusinessException          → 400 (e.g. coupon expired, out of stock)
MethodArgumentNotValidException → 400 (field validation errors)
Exception (generic)        → 500
```

### Global Response Structure

```json
// Success
{
  "success": true,
  "message": "Order placed successfully",
  "data": { ... }
}

// Error
{
  "success": false,
  "message": "Coupon has expired",
  "data": null
}

// Paginated
{
  "success": true,
  "message": "Products fetched",
  "data": {
    "content": [ ... ],
    "page": 0,
    "size": 10,
    "totalElements": 47,
    "totalPages": 5
  }
}
```

---

## Chapter 5 — API Documentation

### Base URL
```
http://localhost:8080/api
```

### Authentication
All protected endpoints require:
```
Authorization: Bearer <access_token>
```

### Auth Module APIs

| Method | Endpoint | Auth | Description |
|--------|---------|------|-------------|
| POST | `/auth/register` | No | User register + send OTP |
| POST | `/auth/verify-otp` | No | Verify email OTP |
| POST | `/auth/login` | No | Login → get tokens |
| POST | `/auth/refresh` | No | Refresh access token |
| POST | `/auth/forgot-password` | No | Send reset OTP |
| POST | `/auth/reset-password` | No | Reset password with OTP |
| POST | `/auth/logout` | Yes | Invalidate refresh token |

### User & Address APIs

| Method | Endpoint | Auth | Description |
|--------|---------|------|-------------|
| GET | `/user/profile` | Yes | Get logged-in user profile |
| PUT | `/user/profile` | Yes | Update profile |
| GET | `/user/addresses` | Yes | List all addresses |
| POST | `/user/addresses` | Yes | Add new address |
| PUT | `/user/addresses/{id}` | Yes | Update address |
| DELETE | `/user/addresses/{id}` | Yes | Delete address |
| PATCH | `/user/addresses/{id}/default` | Yes | Set as default |

### Category APIs

| Method | Endpoint | Auth | Description |
|--------|---------|------|-------------|
| GET | `/categories` | No | All categories |
| GET | `/categories/{slug}` | No | Category by slug |
| POST | `/admin/categories` | Admin | Create category |
| PUT | `/admin/categories/{id}` | Admin | Update category |
| DELETE | `/admin/categories/{id}` | Admin | Delete category |

### Product APIs

| Method | Endpoint | Auth | Description |
|--------|---------|------|-------------|
| GET | `/products` | No | All products (paginated) |
| GET | `/products/{slug}` | No | Product detail |
| GET | `/products?categoryId=1` | No | Filter by category |
| GET | `/products?search=nike` | No | Search products |
| POST | `/admin/products` | Admin | Create product |
| PUT | `/admin/products/{id}` | Admin | Update product |
| DELETE | `/admin/products/{id}` | Admin | Delete product |

### Cart APIs

| Method | Endpoint | Auth | Description |
|--------|---------|------|-------------|
| GET | `/cart` | Yes | Get user cart |
| POST | `/cart` | Yes | Add item to cart |
| PUT | `/cart/{id}` | Yes | Update quantity |
| DELETE | `/cart/{id}` | Yes | Remove item |
| DELETE | `/cart/clear` | Yes | Clear entire cart |

### Wishlist APIs

| Method | Endpoint | Auth | Description |
|--------|---------|------|-------------|
| GET | `/wishlist` | Yes | Get wishlist |
| POST | `/wishlist/{productId}` | Yes | Add to wishlist |
| DELETE | `/wishlist/{productId}` | Yes | Remove from wishlist |

### Order APIs

| Method | Endpoint | Auth | Description |
|--------|---------|------|-------------|
| POST | `/orders` | Yes | Place order |
| GET | `/orders` | Yes | My orders list |
| GET | `/orders/{id}` | Yes | Order detail |
| PATCH | `/orders/{id}/cancel` | Yes | Cancel order |
| GET | `/admin/orders` | Admin | All orders |
| PATCH | `/admin/orders/{id}/status` | Admin | Update order status |

### Payment APIs

| Method | Endpoint | Auth | Description |
|--------|---------|------|-------------|
| POST | `/payments/initiate` | Yes | Initiate payment |
| POST | `/payments/confirm` | Yes | Confirm payment |

### Coupon APIs

| Method | Endpoint | Auth | Description |
|--------|---------|------|-------------|
| POST | `/coupons/validate` | Yes | Validate coupon code |
| POST | `/admin/coupons` | Admin | Create coupon |
| GET | `/admin/coupons` | Admin | All coupons |
| PUT | `/admin/coupons/{id}` | Admin | Update coupon |
| DELETE | `/admin/coupons/{id}` | Admin | Delete coupon |

### Review APIs

| Method | Endpoint | Auth | Description |
|--------|---------|------|-------------|
| GET | `/products/{id}/reviews` | No | Get product reviews |
| POST | `/reviews` | Yes | Add review |
| PUT | `/reviews/{id}` | Yes | Update review |
| DELETE | `/reviews/{id}` | Yes | Delete own review |
| DELETE | `/admin/reviews/{id}` | Admin | Admin delete review |

### Banner APIs

| Method | Endpoint | Auth | Description |
|--------|---------|------|-------------|
| GET | `/banners?position=HOME_TOP` | No | Get active banners |
| POST | `/admin/banners` | Admin | Create banner |
| PUT | `/admin/banners/{id}` | Admin | Update banner |
| DELETE | `/admin/banners/{id}` | Admin | Delete banner |

### Sample Request/Response

**Register Request:**
```json
POST /api/auth/register
{
  "fullName": "John Doe",
  "email": "john@example.com",
  "password": "securePass123",
  "phoneNumber": "9876543210"
}
```

**Register Response:**
```json
{
  "success": true,
  "message": "Registration successful. Please verify your email.",
  "data": null
}
```

**Login Response:**
```json
{
  "success": true,
  "message": "Login successful",
  "data": {
    "accessToken": "eyJhbGciOiJIUzI1NiJ9...",
    "refreshToken": "eyJhbGciOiJIUzI1NiJ9...",
    "user": {
      "id": 1,
      "fullName": "John Doe",
      "email": "john@example.com",
      "role": "ROLE_USER"
    }
  }
}
```

**Place Order Request:**
```json
POST /api/orders
{
  "addressId": 2,
  "couponCode": "SAVE10",
  "paymentMethod": "COD",
  "items": [
    { "variantId": 1, "quantity": 2 },
    { "variantId": 5, "quantity": 1 }
  ]
}
```

**Error Response:**
```json
{
  "success": false,
  "message": "Insufficient stock for variant SKU: NIKE-7",
  "data": null
}
```

---

## Chapter 6 — Frontend (React + Tailwind)

### Project Setup

```bash
npm create vite@latest trendmart-frontend -- --template react
cd trendmart-frontend
npm install tailwindcss @tailwindcss/vite
npm install @reduxjs/toolkit react-redux
npm install axios
npm install react-router-dom
```

### Pages List

| Page | Route | Auth Required |
|------|-------|--------------|
| Home | `/` | No |
| Login | `/login` | No |
| Register | `/register` | No |
| OTP Verify | `/verify-otp` | No |
| Product List | `/products` | No |
| Product Detail | `/products/:slug` | No |
| Cart | `/cart` | Yes |
| Checkout | `/checkout` | Yes |
| Order Success | `/order-success/:id` | Yes |
| My Orders | `/my-orders` | Yes |
| My Profile | `/my-profile` | Yes |
| My Addresses | `/my-addresses` | Yes |
| Wishlist | `/wishlist` | Yes |
| Admin Dashboard | `/admin/dashboard` | Admin |
| Admin Products | `/admin/products` | Admin |
| Admin Orders | `/admin/orders` | Admin |

### Redux Store Structure

```javascript
// store.js
{
  auth: {
    user: null | { id, fullName, email, role },
    accessToken: null | string,
    isAuthenticated: false | true
  },
  cart: {
    items: [],          // [{ id, variant, quantity, price }]
    totalItems: 0,
    totalAmount: 0
  },
  wishlist: {
    items: []           // [{ id, product }]
  }
}
```

### Axios Setup + JWT Interceptor

```javascript
// api/axiosInstance.js
const axiosInstance = axios.create({
  baseURL: 'http://localhost:8080/api',
});

// Request interceptor — attach token
axiosInstance.interceptors.request.use((config) => {
  const token = store.getState().auth.accessToken;
  if (token) config.headers.Authorization = `Bearer ${token}`;
  return config;
});

// Response interceptor — handle 401 → refresh token
axiosInstance.interceptors.response.use(
  (response) => response,
  async (error) => {
    if (error.response?.status === 401) {
      // call /auth/refresh → update store → retry request
    }
    return Promise.reject(error);
  }
);
```

### Role-Based Routing

```jsx
// PrivateRoute.jsx
<PrivateRoute role="ROLE_ADMIN">
  <AdminDashboard />
</PrivateRoute>

// Logic: if not authenticated → redirect to /login
//        if authenticated but wrong role → redirect to /
```

### Key Components

| Component | Purpose |
|-----------|---------|
| `Navbar` | Top nav with cart count, wishlist, user menu |
| `Banner` | Carousel banner for homepage |
| `ProductCard` | Reusable card shown on listing pages |
| `VariantSelector` | Size/Color selector on product detail |
| `AddressSelector` | Choose address at checkout |
| `CouponInput` | Apply coupon code at checkout |
| `OrderSummary` | Price breakdown component |
| `PrivateRoute` | Route guard for auth/role |
| `RatingStars` | Star rating display component |

---

## Chapter 7 — Key Business Logic

### Order Placement Flow (Step by Step)

```
1. User clicks "Place Order" on Checkout page
2. Frontend sends POST /api/orders with { addressId, couponCode, paymentMethod, items[] }
3. Backend:
   a. Validate all variant IDs exist and are active
   b. Check stock for each variant (quantity requested ≤ stock)
   c. Calculate gross_amount = Σ (price × quantity)
   d. If couponCode provided → validate coupon → calculate discount
   e. Calculate net_amount = gross_amount - discount + shipping
   f. Deduct stock from each product_variant
   g. Log inventory change in inventory_logs
   h. Create order record in orders table
   i. Create order_items records
   j. Copy selected address to order_shipping_addresses
   k. Create payment record (PENDING or PAID for COD)
   l. Increment coupon used_count (if coupon used)
   m. Clear user's cart
4. Return order summary to frontend
5. Frontend redirects to /order-success/:orderId
```

### Stock Deduction Logic

```
For each item in order:
  variant = find variant by ID
  if variant.stock < requested quantity → throw OutOfStockException
  variant.stock = variant.stock - quantity
  save variant
  
  create inventory_log:
    change_type = ORDER
    quantity_changed = -quantity
    reference_id = order.id
```

### Coupon Validation Logic

```
1. Find coupon by code → if not found → error
2. Check status = true → if false → "Coupon inactive"
3. Check today between start_date and end_date → else → "Coupon expired"
4. Check gross_amount >= min_order_amount → else → "Minimum order required"
5. Check max_uses = NULL or used_count < max_uses → else → "Coupon limit reached"
6. Calculate discount:
   if FIXED → discount = discount_value
   if PERCENTAGE → discount = gross_amount × (discount_value/100)
                             capped at max_discount_amount (if set)
7. Return { valid: true, discountAmount, finalAmount }
```

### OTP Verification Flow

```
Register:
  1. Save user (status = INACTIVE)
  2. Generate 6-digit OTP
  3. Save OTP in email_verifications (expires in 10 min)
  4. Send OTP email via Gmail SMTP

Verify OTP:
  1. Find latest OTP for email
  2. Check not expired (expires_at > now)
  3. Check OTP matches
  4. Mark email_verifications.verified = true
  5. Update users.status = ACTIVE
```

### JWT Refresh Token Flow

```
1. Access token expires (15 min)
2. Frontend gets 401 response
3. Frontend sends POST /api/auth/refresh with refresh token
4. Backend:
   a. Find refresh token in DB
   b. Check not expired (7 days)
   c. Get user from token
   d. Generate new access token
   e. Return new access token
5. Frontend updates store with new access token
6. Retry original failed request
```

### Rating Summary Update Logic

```
After every review add/update/delete:
  1. SELECT AVG(rating), COUNT(*) FROM product_reviews WHERE product_id = ?
  2. UPDATE product_rating_summary SET
       average_rating = calculated_avg,
       total_reviews = calculated_count
     WHERE product_id = ?
```

---

## Chapter 8 — Setup & Installation Guide

### Prerequisites

| Software | Version | Download |
|----------|---------|---------|
| Java JDK | 17+ | https://adoptium.net |
| Maven | 3.8+ | Included with IntelliJ |
| Node.js | 18+ | https://nodejs.org |
| MySQL | 8.0+ | https://mysql.com |
| Git | Latest | https://git-scm.com |

### Step 1 — Database Setup

```sql
-- Open MySQL Workbench or terminal
-- Run the full DB SQL script (all 26 tables + seed data)
-- File: trendmart-backend/src/main/resources/db/schema.sql

mysql -u root -p < schema.sql
```

### Step 2 — Backend Setup

```bash
# 1. Clone repository
git clone https://github.com/yourusername/trendmart.git
cd trendmart/trendmart-backend

# 2. Update application.properties
# Set your MySQL password, Gmail credentials

# 3. Run the application
mvn spring-boot:run

# Server starts at: http://localhost:8080
```

### Step 3 — Frontend Setup

```bash
cd trendmart/trendmart-frontend

# Install dependencies
npm install

# Run development server
npm run dev

# App runs at: http://localhost:5173
```

### Step 4 — Environment Variables

**Backend `application.properties`:**
```properties
spring.datasource.password=YOUR_MYSQL_PASSWORD
spring.mail.username=YOUR_GMAIL
spring.mail.password=YOUR_GMAIL_APP_PASSWORD
app.jwt.secret=YOUR_MINIMUM_256_BIT_SECRET_KEY
```

**Frontend `.env`:**
```
VITE_API_BASE_URL=http://localhost:8080/api
```

### Gmail App Password Setup

```
1. Go to Google Account → Security
2. Enable 2-Step Verification
3. Search "App Passwords"
4. Create new → Select "Mail" → Copy 16-digit password
5. Use this in spring.mail.password (not your Gmail password)
```

---

## Chapter 9 — Testing

### Postman Setup

```
1. Open Postman
2. Import collection: TrendMart.postman_collection.json
3. Set environment variable: base_url = http://localhost:8080/api
4. Run Auth → Register → Verify OTP → Login
5. Copy access_token → Set as Bearer token in collection auth
```

### Key APIs to Test

| Test Case | Endpoint | Expected |
|-----------|---------|---------|
| Register new user | POST /auth/register | 200, OTP sent |
| Wrong OTP verify | POST /auth/verify-otp | 400, invalid OTP |
| Login after verify | POST /auth/login | 200, tokens returned |
| Add to cart | POST /cart | 200, item added |
| Place order (valid) | POST /orders | 200, order created |
| Place order (out of stock) | POST /orders | 400, stock error |
| Apply expired coupon | POST /coupons/validate | 400, expired error |
| Access admin without role | GET /admin/orders | 403, forbidden |
| Refresh token | POST /auth/refresh | 200, new access token |

### Edge Cases Tested

- Invalid/expired OTP on verify → proper error
- Out-of-stock order placement → transaction rolled back
- Expired coupon code → validation error with message
- Wrong password login → 401 unauthorized
- Access user routes without token → 401
- Access admin routes as USER role → 403
- Concurrent add to cart (same variant) → upsert, no duplicate

---

## Chapter 10 — Screenshots

> 📁 Add screenshots to `/docs/screenshots/` folder

**UI Screenshots needed:**
- [ ] Home page with banners
- [ ] Product listing with filters
- [ ] Product detail with variant selector
- [ ] Cart page
- [ ] Checkout page
- [ ] Order success page
- [ ] My orders list
- [ ] Admin dashboard
- [ ] Admin product management

**Postman Screenshots needed:**
- [ ] Login API response
- [ ] Place order request + response
- [ ] Coupon validation
- [ ] Admin order status update

**Database Screenshots needed:**
- [ ] All 26 tables in MySQL Workbench
- [ ] Sample order_items data
- [ ] product_rating_summary data

---

## Chapter 11 — Challenges & Daily Dev Log

> 📝 **Instructions:** Update this chapter every day as you work. Write what you built, what problems you faced, and how you solved them.

### How to Add Your Entry

```
Copy this template and fill it each day:

### 📅 [Date] — [What You Built Today]
**Module:** [Module name]
**Status:** [In Progress / Completed / Blocked]

**What I did:**
- 

**Problem I faced:**
- 

**How I solved it:**
- 

**Tomorrow's plan:**
- 
```

---

### 📅 Day 1 — [Fill Date] — Project Setup

**Module:** Project Initialization
**Status:** Completed

**What I did:**
- Created Spring Boot project with required dependencies
- Set up MySQL database with all 26 tables
- Created React + Tailwind frontend project
- Tested DB connection from Spring Boot

**Problem I faced:**
- *(Write here)*

**How I solved it:**
- *(Write here)*

**Tomorrow's plan:**
- Start Auth module (Register + OTP)

---

### 📅 Day 2 — [Fill Date] — Auth Module

**Module:** Auth
**Status:** In Progress

**What I did:**
- *(Write here)*

**Problem I faced:**
- *(Write here)*

**How I solved it:**
- *(Write here)*

**Tomorrow's plan:**
- *(Write here)*

---

> 🗓️ **Keep adding daily entries below this line...**

---

### Known Issues & Fixes Log

| Date | Issue | Root Cause | Fix Applied |
|------|-------|-----------|------------|
| *(add as you go)* | | | |

---

## Chapter 12 — Future Scope

| Feature | Priority | Notes |
|---------|---------|-------|
| Razorpay real integration | High | Replace COD mock with live payment |
| Email notifications | High | Order status emails to customers |
| Multi-vendor support | Medium | Each seller has own product list |
| Mobile app (React Native) | Medium | Reuse backend APIs |
| Advanced search & filters | Medium | Elasticsearch or MySQL full-text |
| Product video upload | Low | CDN integration needed |
| Flash sales / time-limited deals | Medium | Scheduled jobs |
| Admin analytics charts | Low | Chart.js on dashboard |

---

## Chapter 13 — References

| Resource | URL |
|----------|-----|
| Spring Boot Docs | https://docs.spring.io/spring-boot/docs/3.2.4/reference/html/ |
| Spring Security Docs | https://docs.spring.io/spring-security/reference/ |
| JJWT (JWT Library) | https://github.com/jwtk/jjwt |
| React Docs | https://react.dev |
| Redux Toolkit | https://redux-toolkit.js.org |
| Tailwind CSS | https://tailwindcss.com/docs |
| Vite Docs | https://vitejs.dev/guide/ |
| Axios Docs | https://axios-http.com/docs/intro |
| MySQL Docs | https://dev.mysql.com/doc/ |
| JWT.io (Debugger) | https://jwt.io |
| Postman Docs | https://learning.postman.com |
| Baeldung Spring Tutorials | https://www.baeldung.com |

---

## Module Progress Tracker

> Update status as you complete each module.

### Backend Modules

| Module | Entity/DB | Controller | Service | Repository | DTO | Status |
|--------|----------|-----------|---------|-----------|-----|--------|
| Auth | ✅ | ⬜ | ⬜ | ⬜ | ⬜ | 🔄 In Progress |
| User & Address | ✅ | ⬜ | ⬜ | ⬜ | ⬜ | ⏳ Pending |
| Category | ✅ | ⬜ | ⬜ | ⬜ | ⬜ | ⏳ Pending |
| Product | ✅ | ⬜ | ⬜ | ⬜ | ⬜ | ⏳ Pending |
| Cart | ✅ | ⬜ | ⬜ | ⬜ | ⬜ | ⏳ Pending |
| Wishlist | ✅ | ⬜ | ⬜ | ⬜ | ⬜ | ⏳ Pending |
| Order | ✅ | ⬜ | ⬜ | ⬜ | ⬜ | ⏳ Pending |
| Payment | ✅ | ⬜ | ⬜ | ⬜ | ⬜ | ⏳ Pending |
| Coupon | ✅ | ⬜ | ⬜ | ⬜ | ⬜ | ⏳ Pending |
| Review | ✅ | ⬜ | ⬜ | ⬜ | ⬜ | ⏳ Pending |
| Banner | ✅ | ⬜ | ⬜ | ⬜ | ⬜ | ⏳ Pending |
| Admin | ✅ | ⬜ | ⬜ | ⬜ | ⬜ | ⏳ Pending |

### Frontend Pages

| Page | Component | Redux | API Connected | Status |
|------|----------|-------|--------------|--------|
| Home | ⬜ | ⬜ | ⬜ | ⏳ Pending |
| Login | ⬜ | ⬜ | ⬜ | ⏳ Pending |
| Register | ⬜ | ⬜ | ⬜ | ⏳ Pending |
| OTP Verify | ⬜ | ⬜ | ⬜ | ⏳ Pending |
| Product List | ⬜ | ⬜ | ⬜ | ⏳ Pending |
| Product Detail | ⬜ | ⬜ | ⬜ | ⏳ Pending |
| Cart | ⬜ | ⬜ | ⬜ | ⏳ Pending |
| Checkout | ⬜ | ⬜ | ⬜ | ⏳ Pending |
| Order Success | ⬜ | ⬜ | ⬜ | ⏳ Pending |
| My Orders | ⬜ | ⬜ | ⬜ | ⏳ Pending |
| My Profile | ⬜ | ⬜ | ⬜ | ⏳ Pending |
| My Addresses | ⬜ | ⬜ | ⬜ | ⏳ Pending |
| Wishlist | ⬜ | ⬜ | ⬜ | ⏳ Pending |
| Admin Dashboard | ⬜ | ⬜ | ⬜ | ⏳ Pending |
| Admin Products | ⬜ | ⬜ | ⬜ | ⏳ Pending |
| Admin Orders | ⬜ | ⬜ | ⬜ | ⏳ Pending |

**Legend:** ✅ Done | ⬜ Pending | 🔄 In Progress | ⏳ Not Started | ❌ Blocked

---

*Last Updated: [Fill your date here]*
*Author: [Your Name]*
*Project: TrendMart — Full Stack E-Commerce*
