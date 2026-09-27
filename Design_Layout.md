# Strapped — Headless Shopify Clothing E-commerce

## 1. Project Overview

Strapped is a custom clothing e-commerce website where the **frontend is fully controlled by us**, while **Shopify manages the commerce backend**.

The architecture is known as **headless Shopify**.

The customer interacts with our custom frontend. The frontend communicates with Shopify through the **Shopify Storefront API**, while Shopify handles products, inventory, orders, customers, discounts, checkout, payments, and other commerce functionality.

---

## 2. High-Level Architecture

```mermaid
flowchart TD
    U[Customer] --> F[Custom Frontend]

    F --> API[Shopify Storefront API]

    API --> S[Shopify Commerce Backend]

    S --> P[Products & Variants]
    S --> I[Inventory]
    S --> O[Orders]
    S --> C[Customers]
    S --> D[Discounts]
    S --> CO[Checkout & Payments]
    S --> SH[Shipping]
```

---

## 3. Technology Stack

### Frontend

- Next.js
- React
- JavaScript or TypeScript
- CSS / Tailwind CSS
- Responsive design
- Custom UI components

### Backend / Commerce

- Shopify
- Shopify Admin
- Shopify Storefront API
- Shopify Checkout

### Optional Services

- Cloudinary for product/media assets if required
- Analytics platform
- Email service
- Customer support/chat service

---

## 4. Responsibilities

### Custom Frontend

The frontend is responsible for:

- Homepage
- Navigation
- Product collections
- Product listing pages
- Product detail pages
- Product image galleries
- Size selection
- Colour selection
- Product filtering
- Product search
- Shopping cart interface
- Wishlist functionality, if implemented
- Customer-facing account pages
- Responsive mobile/desktop experience
- Animations and visual design
- Branding

### Shopify

Shopify is responsible for:

- Product management
- Product variants
- Prices
- Inventory
- Orders
- Customer records
- Discounts
- Checkout
- Payment processing
- Shipping configuration
- Tax configuration
- Store administration

---

## 5. System Architecture

```mermaid
flowchart LR
    subgraph CLIENT["Customer"]
        B[Web Browser]
    end

    subgraph FRONTEND["Custom Frontend"]
        UI[Next.js / React UI]
        CART[Cart State]
        SEARCH[Search & Filters]
    end

    subgraph SHOPIFY["Shopify"]
        API[Storefront API]
        PRODUCTS[Products]
        INVENTORY[Inventory]
        CHECKOUT[Checkout]
        ORDERS[Orders]
        CUSTOMERS[Customers]
    end

    B --> UI
    UI --> CART
    UI --> SEARCH

    UI --> API
    CART --> API
    SEARCH --> API

    API --> PRODUCTS
    API --> INVENTORY
    API --> CHECKOUT
    API --> CUSTOMERS

    CHECKOUT --> ORDERS
```

---

## 6. Product Flow

Products are created and managed inside Shopify.

The custom frontend does not need to maintain a separate product database.

```mermaid
sequenceDiagram
    participant Admin as Store Admin
    participant Shopify as Shopify
    participant API as Storefront API
    participant Frontend as Custom Frontend
    participant Customer as Customer

    Admin->>Shopify: Create / update product
    Shopify->>Shopify: Store product and inventory

    Customer->>Frontend: Open store
    Frontend->>API: Request products
    API->>Shopify: Retrieve product data
    Shopify-->>API: Product data
    API-->>Frontend: Product data
    Frontend-->>Customer: Display products
```

---

## 7. Product Data Example

A Shopify product may contain:

```text
Product
├── Title
├── Description
├── Images
├── Price
├── Compare-at price
├── Product type
├── Tags
├── Collections
└── Variants
    ├── Size
    ├── Colour
    ├── SKU
    ├── Price
    └── Inventory
```

Example:

```text
Oversized Black Hoodie

Price: R699

Colour:
- Black

Sizes:
- S
- M
- L
- XL

SKU:
HOODIE-BLK-S
HOODIE-BLK-M
HOODIE-BLK-L
HOODIE-BLK-XL
```

---

## 8. Product Page Flow

```mermaid
flowchart TD
    A[Customer opens product page] --> B[Frontend reads product ID]
    B --> C[Request product from Storefront API]
    C --> D[Shopify returns product]
    D --> E[Display product information]
    E --> F[Customer selects size]
    F --> G[Customer selects colour]
    G --> H[Customer selects quantity]
    H --> I[Add selected variant to cart]
```

The frontend should use the **variant ID** when adding an item to the cart.

This is important because different sizes and colours can represent different Shopify variants and inventory quantities.

---

## 9. Shopping Cart Flow

```mermaid
sequenceDiagram
    participant Customer
    participant Frontend
    participant Shopify

    Customer->>Frontend: Select product variant
    Frontend->>Shopify: Add variant to cart
    Shopify-->>Frontend: Updated cart
    Frontend-->>Customer: Display cart

    Customer->>Frontend: Change quantity
    Frontend->>Shopify: Update cart
    Shopify-->>Frontend: Updated cart
    Frontend-->>Customer: Display updated cart
```

The cart should ultimately be backed by Shopify rather than creating an independent order system.

---

## 10. Checkout Flow

```mermaid
flowchart TD
    A[Customer] --> B[Custom Cart]
    B --> C[Shopify Cart]
    C --> D[Create / Retrieve Checkout]
    D --> E[Shopify Checkout]
    E --> F[Payment]
    F --> G[Order Created]
    G --> H[Shopify Admin]
```

The custom frontend can control the shopping experience up to checkout.

Shopify then provides the checkout experience and processes the transaction according to the store's configured payment methods.

---

## 11. Order Flow

```mermaid
flowchart LR
    CUSTOMER[Customer] --> FRONTEND[Custom Frontend]
    FRONTEND --> SHOPIFY[Shopify Checkout]
    SHOPIFY --> PAYMENT[Payment Provider]
    PAYMENT --> SHOPIFY
    SHOPIFY --> ORDER[Shopify Order]
    ORDER --> ADMIN[Shopify Admin]
    ADMIN --> FULFILL[Fulfillment / Shipping]
```

The Strapped store owner can manage orders from Shopify Admin instead of building an independent order-management dashboard.

---

## 12. Search and Filtering

The frontend can provide custom filtering such as:

- Category
- Collection
- Size
- Colour
- Price range
- Availability
- Product type
- Tags

Example:

```mermaid
flowchart TD
    A[Search / Filter UI] --> B[Storefront API Query]
    B --> C[Shopify Product Catalog]
    C --> D[Matching Products]
    D --> E[Frontend Product Grid]
```

For more advanced search requirements, Shopify's supported search/query capabilities should be evaluated before introducing a separate search engine.

---

## 13. Recommended Frontend Structure

```text
clothing-store/
│
├── app/
│   ├── page
│   ├── products/
│   ├── collections/
│   ├── cart/
│   ├── search/
│   ├── account/
│   └── checkout/
│
├── components/
│   ├── Navbar
│   ├── Footer
│   ├── ProductCard
│   ├── ProductGrid
│   ├── ProductGallery
│   ├── ProductOptions
│   ├── CartDrawer
│   ├── SearchBar
│   └── FilterPanel
│
├── lib/
│   └── shopify/
│       ├── client
│       ├── queries
│       ├── mutations
│       └── types
│
├── styles/
│
├── public/
│
└── .env.local
```

---

## 14. Shopify API Layer

Keep Shopify API communication separate from UI components.

```mermaid
flowchart TD
    COMPONENTS[React Components]
    SHOPIFY_CLIENT[Shopify API Client]
    QUERIES[GraphQL Queries]
    MUTATIONS[GraphQL Mutations]
    API[Shopify Storefront API]

    COMPONENTS --> SHOPIFY_CLIENT
    SHOPIFY_CLIENT --> QUERIES
    SHOPIFY_CLIENT --> MUTATIONS
    QUERIES --> API
    MUTATIONS --> API
```

This makes the application easier to maintain and prevents Shopify-specific API logic from being scattered throughout the frontend.

---

## 15. Environment Variables

Sensitive configuration should not be hardcoded into the source code.

Example:

```env
SHOPIFY_STORE_DOMAIN=your-store.myshopify.com
SHOPIFY_STOREFRONT_ACCESS_TOKEN=your_token
```

Environment variables should be handled according to the framework's rules.

Any credential that grants privileged Shopify Admin access must **never be exposed to the browser**.

---

## 16. Security Model

```mermaid
flowchart TD
    BROWSER[Customer Browser]
    PUBLIC[Public Storefront API Access]
    SERVER[Server-side Application Logic]
    SHOPIFY[Shopify]

    BROWSER --> PUBLIC
    BROWSER --> SERVER
    SERVER --> SHOPIFY

    ADMIN[Shopify Admin Credentials] --> SERVER
    SERVER --> SHOPIFY
```

### Important rules

- Never expose Shopify Admin API credentials in frontend JavaScript.
- Do not commit secrets to Git.
- Use environment variables for credentials.
- Use HTTPS in production.
- Validate user-controlled input.
- Do not trust prices or product information sent by the client.
- Let Shopify remain the source of truth for products, inventory, carts, and orders.

---

## 17. Source of Truth

A major design principle is to avoid duplicating Shopify data unnecessarily.

| Data | Source of Truth |
|---|---|
| Product name | Shopify |
| Product description | Shopify |
| Product images | Shopify / configured media service |
| Price | Shopify |
| Size variants | Shopify |
| Colour variants | Shopify |
| Inventory | Shopify |
| Cart | Shopify |
| Orders | Shopify |
| Customer data | Shopify |
| Discounts | Shopify |
| Shipping configuration | Shopify |
| Frontend design | Custom application |

---

## 18. Customer Journey

```mermaid
flowchart LR
    A[Landing Page]
    B[Browse Collection]
    C[Product Page]
    D[Select Variant]
    E[Add to Cart]
    F[Review Cart]
    G[Shopify Checkout]
    H[Payment]
    I[Order Confirmation]

    A --> B
    B --> C
    C --> D
    D --> E
    E --> F
    F --> G
    G --> H
    H --> I
```

---

## 19. Admin Workflow

The Strapped store owner does not need to edit the website whenever a product changes.

```mermaid
flowchart TD
    A[Store Owner] --> B[Shopify Admin]
    B --> C[Create / Edit Product]
    B --> D[Update Inventory]
    B --> E[Manage Orders]
    B --> F[Configure Discounts]
    B --> G[Manage Shipping]

    C --> H[Shopify Catalog]
    D --> H
    E --> I[Order System]
    F --> H
    G --> I

    H --> J[Storefront API]
    J --> K[Custom Frontend]
```

---

## 20. Why Use Headless Shopify?

### Advantages

- Complete control over frontend design
- Shopify handles complex commerce functionality
- No need to build an order system from scratch
- Shopify handles inventory management
- Shopify handles checkout and payment integrations
- Products can be managed without redeploying the frontend
- Easier to create a unique brand experience
- Can integrate custom frontend functionality

### Trade-offs

- More development work than a normal Shopify theme
- Requires knowledge of APIs
- Requires frontend application hosting
- More responsibility for frontend performance
- Some Shopify functionality may require additional implementation
- More moving parts to maintain

---

## 21. Recommended Development Strategy

### Phase 1 — Shopify Setup

1. Create Shopify store.
2. Configure store settings.
3. Add clothing products.
4. Create product variants.
5. Configure inventory.
6. Configure shipping.
7. Configure payment provider.
8. Create collections.
9. Configure basic store policies.

### Phase 2 — Frontend

1. Create Next.js application.
2. Build global layout.
3. Build navigation.
4. Build homepage.
5. Connect Shopify Storefront API.
6. Build collection pages.
7. Build product pages.
8. Implement variant selection.
9. Implement cart.
10. Connect Shopify checkout.

### Phase 3 — Store Features

1. Search.
2. Filtering.
3. Sorting.
4. Wishlist.
5. Customer accounts.
6. Order history.
7. Promotional banners.
8. Discount handling.
9. Product recommendations.

### Phase 4 — Production

1. Configure production environment variables.
2. Deploy frontend.
3. Configure custom domain.
4. Configure Shopify domain/checkout settings.
5. Test mobile responsiveness.
6. Test checkout.
7. Test inventory changes.
8. Test order creation.
9. Test failed payments.
10. Monitor errors and performance.

---

## 22. Final Architecture

```mermaid
flowchart TB
    CUSTOMER[Customer]

    subgraph WEB["Custom E-commerce Frontend"]
        HOME[Homepage]
        COLLECTIONS[Collections]
        PRODUCT[Product Pages]
        CART[Cart]
        ACCOUNT[Customer Account]
    end

    subgraph API_LAYER["Shopify Storefront API"]
        STOREFRONT[Storefront API]
    end

    subgraph SHOPIFY["Shopify"]
        CATALOG[Product Catalog]
        STOCK[Inventory]
        CUSTOMERS[Customers]
        CHECKOUT[Checkout]
        ORDERS[Orders]
        DISCOUNTS[Discounts]
    end

    subgraph ADMIN["Store Management"]
        SHOPIFY_ADMIN[Shopify Admin]
    end

    CUSTOMER --> HOME
    CUSTOMER --> COLLECTIONS
    CUSTOMER --> PRODUCT
    CUSTOMER --> CART
    CUSTOMER --> ACCOUNT

    HOME --> STOREFRONT
    COLLECTIONS --> STOREFRONT
    PRODUCT --> STOREFRONT
    CART --> STOREFRONT
    ACCOUNT --> STOREFRONT

    STOREFRONT --> CATALOG
    STOREFRONT --> STOCK
    STOREFRONT --> CUSTOMERS
    STOREFRONT --> CHECKOUT
    STOREFRONT --> ORDERS
    STOREFRONT --> DISCOUNTS

    SHOPIFY_ADMIN --> CATALOG
    SHOPIFY_ADMIN --> STOCK
    SHOPIFY_ADMIN --> ORDERS
    SHOPIFY_ADMIN --> CUSTOMERS
    SHOPIFY_ADMIN --> DISCOUNTS
```

## 23. Core Principle

The project should follow this separation:

> **We own the experience. Shopify owns the commerce.**

The frontend controls how customers discover and interact with the clothing brand.

Shopify remains responsible for the underlying commerce infrastructure, including products, inventory, carts, checkout, payments, customers, and orders.
