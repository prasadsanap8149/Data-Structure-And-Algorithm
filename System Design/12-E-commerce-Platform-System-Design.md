# E-commerce Platform System Design (Amazon/Flipkart)

## 1. System Architecture Overview

### 1.1 High-Level Architecture

```
[Web/Mobile Clients] → [CDN] → [Load Balancer] → [API Gateway] → [Microservices Layer]
                                                                          ↓
[User Service] ← [Product Service] ← [Cart Service] ← [Order Service] ← [Payment Service]
        ↓              ↓                ↓               ↓                 ↓
[User Database] → [Product Catalog] → [Session Store] → [Order DB] → [Payment Gateway]
        ↓              ↓                ↓               ↓                 ↓
[Recommendation Engine] ← [Search Service] ← [Inventory Service] ← [Notification Service]
```

**Why This Architecture?**

- **Microservices**: Independent scaling of different business functions (catalog, orders, payments)
- **API Gateway**: Single entry point with authentication, rate limiting, and request routing
- **CDN**: Global content delivery for product images and static assets
- **Event-Driven**: Asynchronous communication between services for better performance and reliability

### 1.2 Working Logic Flow

1. **Product Discovery** → **Cart Management** → **Checkout Process** → **Order Fulfillment** → **Delivery Tracking**
2. **Background Processes**: Inventory updates, recommendation training, analytics processing, fraud detection

### 1.3 Core Components Detailed

- **Product Management System**

  - **Purpose**: Centralized product catalog and inventory management
  - **Component Details**:
    - Product catalog with rich metadata and media
    - Real-time inventory tracking across warehouses
    - Dynamic pricing engine with competitor analysis
    - Product categorization and attribute management
  - **Why Essential**: Core business functionality; must handle millions of products with high availability

- **Order Management System**

  - **Purpose**: Handle complete order lifecycle from cart to delivery
  - **Responsibilities**:
    - Shopping cart persistence and synchronization
    - Order validation and fraud detection
    - Payment processing integration
    - Inventory reservation and fulfillment
    - Order tracking and customer communication
  - **Why Complex**: Must maintain ACID properties for financial transactions while handling high throughput

- **Recommendation Engine**
  - **Purpose**: Personalized product discovery and cross-selling
  - **Implementation**: Hybrid ML approach with real-time serving
  - **Key Features**:
    - Collaborative filtering for user behavior patterns
    - Content-based filtering for product attributes
    - Real-time contextual recommendations
    - A/B testing framework for optimization
  - **Why Critical**: Drives 20-35% of revenue through personalized discovery

### 1.2 Technical Stack

- **Backend**: Java (Spring Boot), Python (Django)

  - Microservices architecture
  - REST APIs and GraphQL
  - Message queues (Apache Kafka, RabbitMQ)
  - Event-driven architecture

- **Frontend**: React.js, Vue.js

  - Server-side rendering (Next.js)
  - Progressive Web App (PWA)
  - Mobile-responsive design
  - Real-time updates

- **Mobile**: React Native, Flutter
  - Cross-platform development
  - Push notifications
  - Offline capabilities
  - Native payment integration

## 2. How It Works

### 2.1 Product Discovery Flow

1. **Search & Browse**

   - Keyword-based search with Elasticsearch
   - Category-based navigation
   - Filter and faceted search
   - Auto-complete and suggestions

2. **Product Recommendation**

   - Collaborative filtering
   - Content-based recommendations
   - Recently viewed items
   - Trending and popular products

3. **Product Display**
   - Product detail pages
   - Image galleries and zoom
   - Customer reviews and ratings
   - Related products suggestions

### 2.2 Shopping & Checkout Flow

1. **Add to Cart**

   - Session-based cart (guest users)
   - Persistent cart (logged-in users)
   - Cart synchronization across devices
   - Inventory availability check

2. **Checkout Process**

   - Address management
   - Shipping options and costs
   - Payment method selection
   - Order summary and confirmation

3. **Payment Processing**
   - Multiple payment gateways
   - Payment method tokenization
   - Fraud detection and prevention
   - PCI DSS compliance

### 2.3 Order Fulfillment Flow

1. **Order Processing**

   - Order validation and verification
   - Inventory reservation
   - Payment authorization
   - Order confirmation notification

2. **Fulfillment**

   - Warehouse management system
   - Pick, pack, and ship process
   - Shipping carrier integration
   - Tracking number generation

3. **Delivery & Returns**
   - Real-time shipment tracking
   - Delivery confirmation
   - Return and refund processing
   - Customer support integration

## 3. Key Features Implementation

### 3.1 Product Catalog Management

- **Product Information Management**

  ```javascript
  // Product Schema
  {
    "productId": "PROD_12345",
    "title": "Apple iPhone 15 Pro",
    "brand": "Apple",
    "category": "Electronics > Smartphones",
    "attributes": {
      "color": "Space Black",
      "storage": "256GB",
      "model": "A2848"
    },
    "price": {
      "listPrice": 999.99,
      "salePrice": 899.99,
      "currency": "USD"
    },
    "inventory": {
      "quantity": 150,
      "reserved": 25,
      "available": 125
    },
    "images": [
      "https://cdn.example.com/iphone15pro-1.jpg",
      "https://cdn.example.com/iphone15pro-2.jpg"
    ],
    "specifications": {
      "screen": "6.1-inch Super Retina XDR",
      "processor": "A17 Pro chip",
      "camera": "48MP Main camera"
    },
    "seo": {
      "url": "/apple-iphone-15-pro-256gb-space-black",
      "metaTitle": "Buy Apple iPhone 15 Pro 256GB Space Black",
      "metaDescription": "Latest iPhone with A17 Pro chip..."
    }
  }
  ```

- **Category Management**
  - Hierarchical category structure
  - Category-specific attributes
  - SEO-friendly URLs
  - Category performance analytics

### 3.2 Search & Discovery Engine

- **Elasticsearch Implementation**

  - Full-text search with relevance scoring
  - Faceted search and filters
  - Auto-complete and spell correction
  - Search analytics and optimization

- **Search Ranking Factors**
  - Product popularity and sales velocity
  - Customer ratings and reviews
  - Price competitiveness
  - Inventory availability
  - Seasonal and trending factors

### 3.3 Shopping Cart System

- **Cart Management**

  ```javascript
  // Shopping Cart Schema
  {
    "cartId": "CART_67890",
    "userId": "USER_12345",
    "sessionId": "SESSION_ABC123",
    "items": [{
      "productId": "PROD_12345",
      "variantId": "VAR_67890",
      "quantity": 2,
      "price": 899.99,
      "addedAt": "2024-01-01T12:00:00Z"
    }],
    "subtotal": 1799.98,
    "tax": 143.99,
    "shipping": 0.00,
    "total": 1943.97,
    "currency": "USD",
    "updatedAt": "2024-01-01T12:30:00Z"
  }
  ```

- **Cart Features**
  - Persistent cart across sessions
  - Cart sharing and wishlist
  - Save for later functionality
  - Bulk operations (add/remove)

### 3.4 Recommendation Engine

- **Collaborative Filtering**

  - User-based recommendations
  - Item-based recommendations
  - Matrix factorization techniques
  - Deep learning embeddings

- **Content-Based Filtering**

  - Product attribute similarity
  - Category-based recommendations
  - Brand affinity modeling
  - Price range preferences

- **Real-time Personalization**
  - Session-based recommendations
  - Browsing behavior analysis
  - Purchase history integration
  - A/B testing for recommendations

## 4. Scalability Architecture

### 4.1 Database Scaling

- **Product Catalog Scaling**

  - Read replicas for product data
  - Elasticsearch for search workloads
  - CDN for product images
  - Category-based sharding

- **Order Data Scaling**
  - Time-based partitioning for orders
  - User-based sharding for carts
  - Separate read/write databases
  - Event sourcing for order history

### 4.2 Microservices Architecture

- **Service Decomposition**

  - User service (authentication, profiles)
  - Product service (catalog, inventory)
  - Cart service (shopping cart management)
  - Order service (order processing)
  - Payment service (payment processing)
  - Notification service (emails, SMS, push)
  - Recommendation service (ML-based recommendations)

- **Service Communication**
  - Synchronous: REST APIs, GraphQL
  - Asynchronous: Message queues, event streaming
  - Circuit breaker pattern for resilience
  - Service mesh for communication management

## 5. Performance Optimization

### 5.1 Caching Strategy

- **Multi-level Caching**

  - Browser cache (static assets)
  - CDN cache (images, CSS, JS)
  - Application cache (Redis)
  - Database query cache

- **Cache Patterns**
  - Cache-aside for product data
  - Write-through for user sessions
  - Write-behind for analytics data
  - Cache warming for popular products

### 5.2 Database Optimization

- **Query Optimization**

  - Proper indexing strategies
  - Query performance monitoring
  - Connection pooling
  - Read/write splitting

- **Data Archiving**
  - Historical order archiving
  - Inactive product cleanup
  - User activity log rotation
  - Compliance data retention

## 6. Payment System Integration

### 6.1 Payment Gateway Integration

- **Multiple Payment Providers**

  - Credit/debit cards (Stripe, PayPal)
  - Digital wallets (Apple Pay, Google Pay)
  - Bank transfers (ACH, wire transfers)
  - Buy now, pay later (Klarna, Affirm)
  - Cryptocurrency (Bitcoin, Ethereum)

- **Payment Security**
  - PCI DSS compliance
  - Payment tokenization
  - 3D Secure authentication
  - Fraud detection algorithms

### 6.2 Order Processing Pipeline

```
Order Placed → Payment Validation → Inventory Check →
Order Confirmation → Fulfillment → Shipping → Delivery
```

- **Event-Driven Processing**
  - Order status events
  - Inventory updates
  - Payment confirmations
  - Shipping notifications

## 7. Inventory Management

### 7.1 Real-time Inventory Tracking

- **Inventory Operations**

  - Stock level monitoring
  - Automatic reorder points
  - Supplier integration
  - Demand forecasting

- **Inventory Reservation**
  - Cart-based reservation
  - Time-based release
  - Overselling prevention
  - Backorder management

### 7.2 Warehouse Management

- **Multi-warehouse Support**

  - Geographic distribution
  - Inventory allocation
  - Cross-docking operations
  - Returns processing

- **Fulfillment Optimization**
  - Pick path optimization
  - Batch processing
  - Quality control
  - Shipping cost optimization

## 8. Security & Compliance

### 8.1 Data Security

- **User Data Protection**

  - Personal information encryption
  - GDPR compliance
  - Data access controls
  - Audit logging

- **Payment Security**
  - PCI DSS compliance
  - Payment card tokenization
  - Secure payment processing
  - Fraud monitoring

### 8.2 Application Security

- **Web Application Security**

  - Input validation and sanitization
  - XSS and CSRF protection
  - SQL injection prevention
  - Rate limiting and DDoS protection

- **API Security**
  - OAuth 2.0 authentication
  - API rate limiting
  - Request signing
  - IP whitelisting

## 9. Analytics & Business Intelligence

### 9.1 Real-time Analytics

- **User Behavior Tracking**

  - Page views and sessions
  - Product view analytics
  - Cart abandonment rates
  - Conversion funnel analysis

- **Business Metrics**
  - Sales revenue and trends
  - Inventory turnover rates
  - Customer acquisition costs
  - Average order value

### 9.2 Machine Learning Applications

- **Demand Forecasting**

  - Seasonal demand patterns
  - Trend analysis
  - Inventory optimization
  - Price optimization

- **Customer Analytics**
  - Customer lifetime value
  - Churn prediction
  - Segmentation analysis
  - Personalization engines

## 10. Mobile Commerce Optimization

### 10.1 Mobile App Features

- **Mobile-Specific Features**

  - Touch-optimized interface
  - Camera-based product search
  - Location-based services
  - Push notifications

- **Offline Capabilities**
  - Cached product data
  - Offline cart management
  - Sync on reconnection
  - Progressive web app features

### 10.2 Performance Optimization

- **Mobile Performance**
  - Image optimization
  - Lazy loading
  - Compressed assets
  - Fast checkout process

## 11. Global Expansion Considerations

### 11.1 Multi-region Architecture

- **Geographic Distribution**

  - Regional data centers
  - Content localization
  - Currency support
  - Local payment methods

- **Compliance Requirements**
  - Regional data privacy laws
  - Tax calculation and reporting
  - Import/export regulations
  - Local business licensing

### 11.2 Localization Features

- **Multi-language Support**
  - Content translation
  - RTL language support
  - Cultural adaptations
  - Local customer service

## 12. Disaster Recovery & Business Continuity

### 12.1 High Availability Design

- **Redundancy & Failover**

  - Multi-AZ deployment
  - Database replication
  - Load balancer redundancy
  - CDN failover

- **Backup & Recovery**
  - Automated backups
  - Point-in-time recovery
  - Cross-region replication
  - Disaster recovery testing

### 12.2 Monitoring & Alerting

- **System Monitoring**

  - Application performance monitoring
  - Infrastructure health checks
  - Error tracking and logging
  - Business metric monitoring

- **Alert Management**
  - Threshold-based alerts
  - Anomaly detection
  - Escalation procedures
  - Incident response automation

## 13. Capacity Planning & Cost Optimization

### 13.1 Traffic Patterns

- **Seasonal Traffic**

  - Black Friday/Cyber Monday
  - Holiday shopping seasons
  - Flash sales and promotions
  - Geographic time zone patterns

- **Growth Projections**
  - User acquisition rates
  - Transaction volume growth
  - Product catalog expansion
  - Geographic expansion plans

### 13.2 Cost Optimization

- **Infrastructure Costs**

  - Auto-scaling policies
  - Reserved instance utilization
  - Spot instance usage
  - Resource right-sizing

- **Operational Efficiency**
  - Automated deployments
  - Self-healing systems
  - Predictive maintenance
  - Performance optimization

# Data Storage, RBAC, and Database Design

## 1. Data Storage Architecture

- **PostgreSQL (Transactional Data)**
  - **Tables**: users, orders, transactions, inventory, payments
  - **Why PostgreSQL**: ACID compliance for financial data, complex queries, strong consistency
- **MongoDB (Catalog & Content)**
  - **Collections**: products, categories, reviews, seller_profiles
  - **Why MongoDB**: Flexible schema for diverse product attributes, horizontal scaling
- **Redis (Session & Cache)**
  - **Data**: User sessions, shopping carts, recently viewed items, hot product cache
  - **Why Redis**: Sub-millisecond response times, session persistence, atomic cart operations
- **Elasticsearch (Search & Analytics)**
  - **Indices**: product_search, user_behavior, sales_analytics
  - **Why Elasticsearch**: Full-text search, faceted search, real-time analytics
- **Cassandra (Activity Logs)**
  - **Tables**: user_activity, audit_logs, recommendation_events
  - **Why Cassandra**: High write throughput for tracking events, time-series data
- **S3/CDN (Media & Assets)**
  - **Content**: Product images, videos, documents, static website assets
  - **Why CDN**: Global content delivery, reduced server load, improved page load times

## 2. Access Control & Security (RBAC)

### 2.1 Role-Based Access Control Implementation

```
User Roles Hierarchy:
├── Guest User (Browse Only)
├── Registered Customer (Purchase & Account)
├── Premium Customer (Enhanced Features)
├── Seller (Product Management)
├── Vendor Manager (Multiple Stores)
├── Customer Support (Order Assistance)
├── Content Moderator (Review Management)
├── Finance Admin (Payment Operations)
├── System Admin (Platform Management)
└── Super Admin (Full System Access)
```

### 2.2 Permission Matrix

| Role           | Browse | Purchase      | Sell   | Manage Orders  | Financial Data | Admin Panel   | System Config |
| -------------- | ------ | ------------- | ------ | -------------- | -------------- | ------------- | ------------- |
| Guest          | ✅     | Limited       | ❌     | ❌             | ❌             | ❌            | ❌            |
| Customer       | ✅     | ✅            | ❌     | Own Orders     | Own Data       | ❌            | ❌            |
| Premium        | ✅     | ✅ (Priority) | ❌     | Enhanced       | Own Data       | ❌            | ❌            |
| Seller         | ✅     | ✅            | ✅     | Own Products   | Sales Data     | Limited       | ❌            |
| Vendor Manager | ✅     | ✅            | ✅     | Multi-Store    | Aggregated     | Moderate      | ❌            |
| Support        | ✅     | Assist        | ❌     | All Orders     | Limited        | Support Tools | ❌            |
| Content Mod    | ✅     | ✅            | Review | Review Related | ❌             | Moderation    | ❌            |
| Finance Admin  | ✅     | ✅            | ❌     | Financial      | Full Financial | Finance Panel | ❌            |
| System Admin   | ✅     | ✅            | ✅     | All Orders     | System Level   | Full          | Limited       |
| Super Admin    | ✅     | ✅            | ✅     | All Orders     | Full Access    | Full          | Full          |

### 2.3 Authentication & Authorization Flow

1. **Multi-Factor Authentication**

   - **Email/Password Primary**:

     - Strong password requirements (8+ chars, mixed case, numbers, symbols)
     - Account lockout after 5 failed attempts
     - Password reset via secure email tokens
     - **Why Email-based**: Universal accessibility, account recovery capabilities

   - **Social Login Integration**:

     - OAuth 2.0 with Google, Facebook, Apple
     - Account linking for existing users
     - Streamlined registration process
     - **Why Social Login**: Reduces registration friction, improves conversion rates

   - **Two-Factor Authentication (2FA)**:
     - SMS-based OTP for high-value transactions
     - TOTP app support (Google Authenticator, Authy)
     - Backup codes for account recovery
     - **Why 2FA**: Essential for financial transactions, prevents account takeover

2. **Session Management**

   - **JWT Token Strategy**:
     - Short-lived access tokens (30 minutes)
     - Refresh tokens with device binding
     - Automatic token rotation on sensitive operations
     - **Why JWT**: Stateless, scalable, contains user permissions

3. **API Security**
   - **Rate Limiting by Role**:
     - Guest users: 100 requests/hour
     - Customers: 1000 requests/hour
     - Sellers: 5000 requests/hour
     - **Why Tiered Limits**: Prevents abuse while enabling legitimate business usage

### 2.4 Data Security & Privacy

- **PCI DSS Compliance**:

  - Payment card data never stored directly
  - All payment processing through certified providers
  - Network segmentation for card data environment
  - **Implementation**: Tokenization of sensitive payment data

- **Personal Data Protection**:
  - GDPR/CCPA compliance for user data
  - Data minimization and purpose limitation
  - User consent management for marketing
  - **Implementation**: Privacy preference center with granular controls

## 3. Database Design & Table-Level Explanation

### 3.1 PostgreSQL (Primary Transactional Database)

**Why PostgreSQL?**

- ACID compliance for financial transactions
- Advanced indexing for complex queries
- JSON support for flexible product attributes
- Strong consistency for inventory management

#### Core Tables:

**Users Table**

```sql
CREATE TABLE users (
    user_id BIGSERIAL PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    username VARCHAR(100) UNIQUE,
    password_hash VARCHAR(255) NOT NULL,
    first_name VARCHAR(100) NOT NULL,
    last_name VARCHAR(100) NOT NULL,
    phone_number VARCHAR(20),
    date_of_birth DATE,
    account_type VARCHAR(20) DEFAULT 'customer', -- customer, seller, admin
    account_status VARCHAR(20) DEFAULT 'active', -- active, suspended, banned
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    email_verified BOOLEAN DEFAULT false,
    phone_verified BOOLEAN DEFAULT false,
    preferences JSONB DEFAULT '{}',
    INDEX idx_email (email),
    INDEX idx_account_type (account_type),
    INDEX idx_account_status (account_status),
    INDEX idx_created_at (created_at)
);
```

**Purpose**: Central user identity and profile management
**Why These Fields**: Email for authentication, account_type for RBAC, status for account management

**Orders Table**

```sql
CREATE TABLE orders (
    order_id BIGSERIAL PRIMARY KEY,
    user_id BIGINT REFERENCES users(user_id),
    order_number VARCHAR(50) UNIQUE NOT NULL, -- Human-readable order number
    order_status VARCHAR(30) NOT NULL, -- pending, confirmed, shipped, delivered, cancelled
    payment_status VARCHAR(30) NOT NULL, -- pending, paid, failed, refunded
    subtotal DECIMAL(12,2) NOT NULL,
    tax_amount DECIMAL(12,2) NOT NULL,
    shipping_cost DECIMAL(12,2) NOT NULL,
    discount_amount DECIMAL(12,2) DEFAULT 0,
    total_amount DECIMAL(12,2) NOT NULL,
    currency VARCHAR(3) DEFAULT 'USD',
    shipping_address JSONB NOT NULL,
    billing_address JSONB NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    expected_delivery_date DATE,
    INDEX idx_user_id (user_id),
    INDEX idx_order_status (order_status),
    INDEX idx_payment_status (payment_status),
    INDEX idx_created_at (created_at),
    INDEX idx_order_number (order_number)
);
```

**Purpose**: Order lifecycle management and financial tracking
**Why These Fields**: Separate status tracking, address flexibility, financial audit trail

**Order_Items Table**

```sql
CREATE TABLE order_items (
    order_item_id BIGSERIAL PRIMARY KEY,
    order_id BIGINT REFERENCES orders(order_id),
    product_id VARCHAR(50) NOT NULL, -- References MongoDB
    seller_id BIGINT REFERENCES users(user_id),
    quantity INTEGER NOT NULL,
    unit_price DECIMAL(10,2) NOT NULL,
    total_price DECIMAL(12,2) NOT NULL,
    product_snapshot JSONB NOT NULL, -- Product details at time of order
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_order_id (order_id),
    INDEX idx_product_id (product_id),
    INDEX idx_seller_id (seller_id)
);
```

**Purpose**: Detailed order composition with historical product data
**Why Product Snapshot**: Preserves exact product details at purchase time

### 3.2 MongoDB (Product Catalog & Content)

**Why MongoDB?**

- Flexible schema for diverse product attributes
- Horizontal scaling for large catalogs
- Rich query capabilities for product search
- Document structure matches product complexity

#### Core Collections:

**Products Collection**

```javascript
{
  "_id": ObjectId("product_unique_id"),
  "sku": "PROD-12345-VAR-01",
  "title": "Apple iPhone 15 Pro Max",
  "description": "Latest iPhone with advanced camera system",
  "brand": "Apple",
  "category": {
    "primary": "Electronics",
    "secondary": "Smartphones",
    "tertiary": "Apple iPhones"
  },
  "attributes": {
    "color": "Space Black",
    "storage": "256GB",
    "screen_size": "6.7 inches",
    "operating_system": "iOS 17"
  },
  "pricing": {
    "list_price": 1199.99,
    "sale_price": 1099.99,
    "currency": "USD",
    "discount_percentage": 8.33
  },
  "inventory": {
    "total_quantity": 500,
    "available_quantity": 250,
    "reserved_quantity": 25,
    "warehouse_locations": ["US-EAST-1", "US-WEST-1"]
  },
  "media": {
    "primary_image": "https://cdn.example.com/iphone15pro-main.jpg",
    "gallery": [
      "https://cdn.example.com/iphone15pro-1.jpg",
      "https://cdn.example.com/iphone15pro-2.jpg"
    ],
    "videos": ["https://cdn.example.com/iphone15pro-demo.mp4"]
  },
  "seo": {
    "url_slug": "apple-iphone-15-pro-max-256gb-space-black",
    "meta_title": "Buy Apple iPhone 15 Pro Max 256GB - Best Price",
    "meta_description": "Get the latest iPhone 15 Pro Max with advanced camera..."
  },
  "seller_info": {
    "seller_id": "SELLER_123",
    "seller_name": "Apple Store Official",
    "seller_rating": 4.9
  },
  "reviews_summary": {
    "average_rating": 4.7,
    "total_reviews": 1250,
    "rating_distribution": {
      "5": 850,
      "4": 275,
      "3": 85,
      "2": 25,
      "1": 15
    }
  },
  "created_at": ISODate("2024-01-01T00:00:00Z"),
  "updated_at": ISODate("2024-01-01T00:00:00Z"),
  "is_active": true
}
```

**Purpose**: Comprehensive product information with rich metadata
**Why This Structure**: Supports complex queries, flexible attributes, SEO optimization

### 3.3 Redis (Session Management & Caching)

**Why Redis?**

- Sub-millisecond response times for user sessions
- Atomic operations for shopping cart management
- Built-in expiration for session lifecycle
- Pub/Sub for real-time notifications

#### Data Structures:

**Shopping Cart**

```redis
Key Pattern: cart:{user_id}
Data Type: Hash
Fields: {
  "item_PROD123": "{\"quantity\": 2, \"price\": 29.99, \"added_at\": \"timestamp\"}",
  "item_PROD456": "{\"quantity\": 1, \"price\": 59.99, \"added_at\": \"timestamp\"}",
  "last_modified": "timestamp",
  "cart_total": "119.97"
}
TTL: 7 days (extends on activity)
```

**Purpose**: Persistent shopping cart across sessions
**Why Hash**: Efficient item-level operations, atomic updates

**User Session**

```redis
Key Pattern: session:{session_id}
Data Type: Hash
Fields: {
  "user_id": "12345",
  "email": "user@example.com",
  "role": "customer",
  "last_activity": "timestamp",
  "device_info": "serialized_device_data",
  "csrf_token": "random_token"
}
TTL: 24 hours (refreshed on activity)
```

**Purpose**: Maintain user authentication state
**Why Hash with TTL**: Automatic session cleanup, efficient partial updates

**Product Cache**

```redis
Key Pattern: product:{product_id}
Data Type: String (JSON)
Value: Serialized product data
TTL: 1 hour (cache-aside pattern)
```

**Purpose**: Cache frequently accessed product data
**Why JSON String**: Simple serialization, works with existing application logic
