# Zomato System Design

## 1. System Architecture & Overview

### 1.1 High-Level Architecture

```
[Client Apps] → [API Gateway] → [Order Management] → [Restaurant Systems] → [Databases]
     ↓              ↓              ↓                      ↓                    ↓
[User/Delivery] → [Load Balancer] → [Kitchen Display] → [Partner APIs] → [PostgreSQL/MongoDB]
```

### 1.2 Working Logic Flow

1. **Restaurant Discovery**: Location detection → Search/filter → Restaurant listing → Menu browsing
2. **Order Placement**: Item selection → Customization → Cart management → Payment processing
3. **Order Fulfillment**: Restaurant notification → Preparation tracking → Delivery assignment
4. **Delivery Tracking**: Real-time updates → Navigation → Delivery completion → Rating/feedback
5. **Post-Order**: Payment settlement → Analytics logging → Recommendation updates

## 2. Core Components & Descriptions

### 2.1 Order Management System (Why this tier exists)

**Purpose**: Orchestrates the complete food ordering and delivery lifecycle

- **Restaurant Listing**: Comprehensive catalog with search and discovery features
- **Order Processing**: End-to-end order management from placement to completion
- **Delivery Tracking**: Real-time monitoring of delivery progress with ETA updates
- **Payment Handling**: Secure payment processing with multiple payment options

### 2.2 Data Storage Architecture & Database Explanations

#### Primary Databases:

- **PostgreSQL (Relational)**

  - **Purpose**: User profiles, orders, financial transactions, structured business data
  - **Why chosen**: ACID compliance for financial data, complex joins for order relationships
  - **Tables**:
    ```sql
    users: user_id, phone, email, addresses, preferences, registration_date
    restaurants: restaurant_id, name, location, cuisine_type, rating, owner_info
    orders: order_id, user_id, restaurant_id, items, total_amount, status, timestamps
    payments: payment_id, order_id, amount, method, status, gateway_response
    delivery_partners: partner_id, name, phone, vehicle_info, location, status
    ```

- **MongoDB (NoSQL)**

  - **Purpose**: Restaurant data, menus, flexible catalog management
  - **Why chosen**: Flexible schema for varying menu structures, fast reads for catalog browsing
  - **Collections**:
    ```javascript
    restaurant_menus: {
      restaurant_id, categories, items, prices, availability, customizations;
    }
    user_preferences: {
      user_id, favorite_cuisines, dietary_restrictions, order_history_summary;
    }
    delivery_zones: {
      zone_id, boundaries, restaurants, delivery_fee, estimated_time;
    }
    promotional_campaigns: {
      campaign_id, target_criteria, offers, validity, usage_tracking;
    }
    ```

- **Redis (In-Memory)**

  - **Purpose**: Caching, session management, real-time order tracking
  - **Why chosen**: Ultra-fast access for real-time updates and frequent data access
  - **Use cases**: User sessions, restaurant availability cache, active order tracking, search cache

- **Elasticsearch**

  - **Purpose**: Restaurant search, menu search, analytics, recommendation engine
  - **Why chosen**: Full-text search capabilities, geospatial queries, real-time indexing
  - **Indices**: restaurant_search, menu_search, order_analytics, user_behavior

- **TimeseriesDB (InfluxDB)**
  - **Purpose**: Delivery tracking, performance metrics, real-time analytics
  - **Why chosen**: Optimized for location tracking and time-based performance data
  - **Measurements**: delivery_tracking, order_metrics, system_performance

### 2.3 Technical Stack & Component Justification

- **Backend**: Java (Spring Boot)

  - **Why chosen**: Enterprise scalability, robust ecosystem, excellent for complex business logic
  - Microservices architecture
  - REST APIs
  - Event-driven system for order processing
  - Message queues for async processing

- **Frontend**: Angular

  - **Why chosen**: Component-based architecture, excellent for complex UIs, real-time updates
  - PWA architecture for offline capabilities
  - Real-time tracking with WebSocket integration
  - Responsive design for mobile-first approach

- **Mobile**: Flutter
  - **Why chosen**: Cross-platform development, native performance, consistent user experience
  - Cross-platform development for customers and delivery partners
  - Location services integration
  - Push notifications for order updates

## 3. Role-Based Access Control (RBAC) System

### 3.1 User Roles & Hierarchy

```
Super Admin → Operations Manager → Restaurant Manager → Support Agent → Delivery Partner → Customer
     ↓              ↓                    ↓                  ↓                ↓                ↓
All Access → Regional Ops → Restaurant Control → Customer Care → Delivery Ops → Personal Orders
```

### 3.2 Role Definitions & Permissions

#### 3.2.1 Super Admin

- **Scope**: Platform-wide administrative control
- **Permissions**:
  - System configuration, user management, financial oversight
  - Restaurant onboarding, delivery partner management, pricing control
  - Security settings, compliance monitoring, strategic analytics

#### 3.2.2 Operations Manager

- **Scope**: Regional operations and performance optimization
- **Permissions**:
  - Regional restaurant management, delivery optimization, performance monitoring
  - Pricing strategy, promotion management, quality control
  - Operational analytics, resource allocation, process improvement

#### 3.2.3 Restaurant Manager/Owner

- **Scope**: Individual restaurant operations and management
- **Permissions**:
  - Menu management, order processing, inventory control
  - Restaurant analytics, customer feedback, promotional tools
  - Staff management, payment tracking, business insights

#### 3.2.4 Customer Support Agent

- **Scope**: Customer and partner assistance
- **Permissions**:
  - View customer orders, refund processing, dispute resolution
  - Restaurant support, delivery issue resolution, feedback management
  - Limited access to user data for support purposes only

#### 3.2.5 Delivery Partner

- **Scope**: Delivery operations and earning management
- **Permissions**:
  - Accept/decline orders, navigation access, delivery tracking
  - Earnings tracking, performance metrics, support communication
  - Profile management, schedule management

#### 3.2.6 Customer

- **Scope**: Personal food ordering and account management
- **Permissions**:
  - Browse restaurants, place orders, track deliveries
  - Payment management, order history, rating/reviews
  - Account settings, address management, preferences

### 3.3 Permission Matrix

| Action                | Super Admin | Operations | Restaurant | Support | Delivery | Customer |
| --------------------- | ----------- | ---------- | ---------- | ------- | -------- | -------- |
| System Config         | ✅          | ❌         | ❌         | ❌      | ❌       | ❌       |
| Restaurant Onboarding | ✅          | ✅         | ❌         | ❌      | ❌       | ❌       |
| Menu Management       | ✅          | ✅         | ✅         | ❌      | ❌       | ❌       |
| Order Support         | ✅          | ✅         | ✅         | ✅      | ❌       | ✅\*     |
| Delivery Management   | ✅          | ✅         | ❌         | ✅      | ✅       | ❌       |
| Analytics Access      | ✅          | ✅         | ✅\*       | ❌      | ✅\*     | ❌       |
| Profile Management    | ✅          | ✅         | ✅         | ✅      | ✅       | ✅       |

\*Limited to own data/business

## 4. How It Works

### 4.1 Order Processing Flow

1. **Restaurant Discovery**

   - **Purpose**: Help users find relevant restaurants based on preferences
   - Location detection → Cuisine filtering → Restaurant ranking → Menu presentation

2. **Menu Browsing**

   - **Purpose**: Showcase restaurant offerings with detailed information
   - Menu categorization → Item details → Customization options → Pricing display

3. **Order Placement**

   - **Purpose**: Secure order capture with accurate details
   - Item selection → Customization → Cart management → Address confirmation → Payment

4. **Payment Processing**

   - **Purpose**: Secure and reliable transaction handling
   - Payment method selection → Transaction processing → Confirmation → Receipt generation

5. **Delivery Tracking**
   - **Purpose**: Real-time visibility into order progress
   - Order confirmation → Preparation tracking → Delivery assignment → Live tracking → Completion

### 4.2 Key Features

- **Restaurant Platform**

  - **Purpose**: Comprehensive restaurant management and customer interface
  - Menu management with real-time availability updates
  - Order handling with kitchen display integration
  - Analytics dashboard for business insights
  - Inventory control and stock management

- **Delivery System**
  - **Purpose**: Efficient delivery operations with optimal resource utilization
  - Route optimization considering traffic and delivery efficiency
  - Delivery partner matching based on location and availability
  - Real-time tracking with accurate ETA calculations
  - Performance monitoring and quality assurance

## 5. Execution Flow

### 5.1 Order Pipeline

1. **Order Placement Tier**

   - **Tier Purpose**: Capture customer orders accurately and efficiently
   - Restaurant selection → Menu browsing → Item customization → Cart finalization
   - Address confirmation → Payment processing → Order validation

2. **Restaurant Processing Tier**

   - **Tier Purpose**: Manage order fulfillment at restaurant level
   - Order notification → Menu item availability check → Preparation time estimation
   - Kitchen display integration → Cooking progress tracking → Ready notification

3. **Delivery Assignment Tier**

   - **Tier Purpose**: Optimize delivery partner allocation and routing
   - Available partner identification → Distance calculation → Assignment algorithm
   - Route optimization → Pickup coordination → Delivery initiation

4. **Order Fulfillment Tier**
   - **Tier Purpose**: Complete the delivery process with quality assurance
   - Real-time tracking → Customer communication → Delivery completion
   - Payment settlement → Rating collection → Analytics logging

### 5.2 Background Processes

- **Delivery Partner Allocation**

  - **Purpose**: Optimize delivery efficiency and reduce wait times
  - Demand prediction → Supply positioning → Dynamic allocation

- **Restaurant Recommendations**

  - **Purpose**: Personalize user experience and increase engagement
  - User behavior analysis → Machine learning algorithms → Recommendation updates

- **Analytics Processing**

  - **Purpose**: Generate business insights and performance metrics
  - Order pattern analysis → Performance metrics → Business intelligence

- **Rating System**
  - **Purpose**: Maintain quality standards and user satisfaction
  - Rating collection → Analysis → Quality scoring → Improvement feedback

## 6. User & Business Journeys

### 6.1 User Side

1. **Customer Journey**

   - **Discovery Tier**: App installation → Location setup → Restaurant browsing → Menu exploration
   - **Ordering Tier**: Item selection → Customization → Cart management → Payment processing
   - **Experience Tier**: Order tracking → Delivery monitoring → Feedback → Rating
   - **Retention Tier**: Order history → Favorites → Loyalty programs → Recommendations

2. **Restaurant Journey**

   - **Onboarding Tier**: Partner registration → Menu setup → Payment integration → Training
   - **Operations Tier**: Order management → Kitchen integration → Inventory tracking → Customer interaction
   - **Analytics Tier**: Performance monitoring → Revenue tracking → Customer insights → Business optimization
   - **Growth Tier**: Menu optimization → Promotional campaigns → Quality improvement → Expansion planning

3. **Delivery Partner Journey**
   - **Onboarding Tier**: Registration → Document verification → Training → App setup
   - **Operations Tier**: Login → Zone selection → Order acceptance → Navigation → Delivery completion
   - **Management Tier**: Earnings tracking → Performance metrics → Schedule management → Support access
   - **Development Tier**: Skill improvement → Incentive achievement → Fleet expansion → Advanced training

### 6.2 Business Side

1. **Restaurant Management**

   - **Partnership Tier**: Partner acquisition → Onboarding process → Contract management → Relationship building
   - **Quality Control Tier**: Restaurant verification → Menu compliance → Service monitoring → Performance evaluation
   - **Support Tier**: Technical assistance → Business consultation → Issue resolution → Process improvement
   - **Analytics Tier**: Performance tracking → Market analysis → Revenue optimization → Strategic planning

2. **Delivery Operations**
   - **Fleet Management Tier**: Partner recruitment → Training programs → Performance monitoring → Resource optimization
   - **Optimization Tier**: Route planning → Demand forecasting → Supply allocation → Efficiency improvement
   - **Quality Assurance Tier**: Service monitoring → Customer feedback → Issue resolution → Process refinement
   - **Technology Tier**: App updates → Feature development → Integration management → Innovation implementation

## 7. Limitations

### 7.1 Technical Limitations

- Peak load handling
- Real-time tracking accuracy
- Restaurant integration
- Delivery constraints

### 7.2 Business Limitations

- Geographic coverage
- Delivery partner availability
- Restaurant partnerships
- Market competition

## 8. Scope & Scalability

### 8.1 Current Scope

- **Food Delivery**: On-demand restaurant food delivery service
- **Restaurant Discovery**: Comprehensive restaurant search and discovery platform
- **Table Reservations**: Restaurant booking and reservation management
- **Reviews & Ratings**: User-generated content and rating system
- **Multiple Cuisines**: Diverse food options and restaurant categories

### 8.2 Future Scope

- **Grocery Delivery**: Fresh produce and grocery delivery service
- **Cloud Kitchens**: Virtual restaurant and delivery-only kitchen support
- **Subscription Services**: Monthly meal plans and premium memberships
- **International Expansion**: Global market penetration with localization
- **AI-Powered Features**: Personalized recommendations and predictive ordering
- **Sustainability**: Eco-friendly packaging and carbon-neutral delivery

## 9. Competitive Advantages

### 9.1 Technical Differentiators

- **Order Processing Speed**: Fast order confirmation and processing
- **Delivery Optimization**: Advanced algorithms for efficient delivery routing
- **Restaurant Integration**: Seamless POS and kitchen display integration
- **User Experience**: Intuitive interface with personalized recommendations
- **Platform Reliability**: High availability with robust infrastructure

### 9.2 Business Differentiators

- **Restaurant Network**: Extensive partner ecosystem across diverse cuisines
- **Delivery Fleet**: Large delivery partner network with optimized logistics
- **Local Market Presence**: Strong brand recognition and market penetration
- **Customer Service**: Dedicated support for customers, restaurants, and delivery partners
- **Innovation**: Continuous feature development and technology advancement

## 10. Additional Important Areas

### 10.1 Security

- **Payment Security**: PCI DSS compliance, encrypted payment processing
- **Data Protection**: User privacy, GDPR compliance, secure data handling
- **Fraud Prevention**: Anti-fraud algorithms, suspicious order detection
- **Access Control**: Role-based permissions, secure API access
- **Transaction Security**: Secure payment gateways, fraud monitoring

### 10.2 Quality Control

- **Restaurant Hygiene**: Health certification verification, quality audits
- **Delivery Standards**: Service quality monitoring, performance tracking
- **Food Quality**: Customer feedback analysis, restaurant performance metrics
- **Service Monitoring**: Real-time service quality assessment and improvement
- **Compliance**: Food safety regulations, local health department requirements

### 10.3 Performance

- **Order Processing**: High-throughput order handling with minimal latency
- **Real-Time Tracking**: Accurate delivery tracking with live updates
- **App Responsiveness**: Fast loading times and smooth user experience
- **Search Functionality**: Quick restaurant and menu item search capabilities
- **Scalability**: Auto-scaling infrastructure for peak demand periods

### 10.4 Customer Support

- **24/7 Support**: Round-the-clock customer assistance and issue resolution
- **Issue Resolution**: Structured process for order and delivery problems
- **Order Tracking**: Real-time status updates and customer communication
- **Refund Handling**: Quick and fair refund processing for order issues
- **Multi-Channel Support**: Phone, chat, email, and in-app support options

### 10.5 Analytics

- **Order Patterns**: Analysis of ordering trends and customer behavior
- **Customer Behavior**: User journey analysis and engagement tracking
- **Restaurant Performance**: Revenue tracking, customer satisfaction, operational efficiency
- **Delivery Metrics**: Delivery time analysis, partner performance, route optimization
- **Business Intelligence**: Market insights, competitive analysis, growth opportunities
