# Twitter System Design

## 1. System Architecture Overview

### 1.1 High-Level Architecture

```
[Mobile/Web Clients] → [CDN] → [Load Balancer] → [API Gateway] → [Microservices Layer]
                                                                      ↓
[Tweet Service] ← [Timeline Service] ← [User Service] ← [Notification Service] ← [Media Service]
        ↓              ↓                  ↓                    ↓                     ↓
[Tweet Database] → [Timeline Cache] → [User Database] → [Message Queue] → [Media Storage]
        ↓              ↓                  ↓                    ↓                     ↓
[Search Service] ← [Analytics Service] ← [Recommendation Engine] ← [Real-time Stream Processing]
```

**Why This Architecture?**

- **Fan-out Architecture**: Optimizes for read-heavy workload (500M+ daily tweet reads)
- **Microservices**: Independent scaling of different functionalities (tweets, timelines, users)
- **Event-Driven**: Real-time features through message queues and stream processing
- **Cache-Heavy**: Multiple cache layers for sub-100ms response times

### 1.2 Working Logic Flow

1. **Tweet Creation** → **Content Processing** → **Timeline Fan-out** → **Real-time Delivery** → **Search Indexing**
2. **Background Processes**: Trend analysis, recommendation training, analytics aggregation, content moderation

### 1.3 Core Components Detailed

- **Tweet Management System**

  - **Purpose**: Handle tweet lifecycle from creation to deletion
  - **Component Details**:
    - Tweet composition with character limits and media attachment
    - Real-time content validation and spam detection
    - Tweet threading and conversation management
    - Engagement tracking (likes, retweets, replies, views)
  - **Why Essential**: Core platform functionality; must handle 6,000+ tweets per second globally

- **Timeline Generation System**

  - **Purpose**: Create personalized and chronological feeds for users
  - **Responsibilities**:
    - Home timeline with algorithmic ranking
    - User timeline with chronological ordering
    - Fan-out strategies for different user types
    - Real-time timeline updates and notifications
  - **Why Complex**: Must balance personalization with performance across 300M+ active users

- **Real-time Streaming Infrastructure**
  - **Purpose**: Enable live updates and trending topics
  - **Implementation**: Apache Kafka with custom stream processing
  - **Key Features**:
    - Live tweet streaming to followers
    - Trending topic calculation in real-time
    - Real-time engagement metrics
    - Live event tracking and notifications
  - **Why Critical**: Twitter's value proposition is real-time information discovery

### 1.2 Technical Stack

- **Backend**: Java (Spring Boot), Scala

  - Microservices architecture
  - REST APIs and GraphQL
  - Message queues (Apache Kafka)
  - Real-time processing (Apache Storm)

- **Frontend**: React.js

  - Single Page Application
  - Progressive Web App
  - Real-time updates via WebSocket

- **Mobile**: React Native

  - Cross-platform development
  - Push notifications
  - Offline capabilities

- **Data Storage Architecture**
  - **Cassandra (Tweet Storage & Timelines)**
    - **Tables**: tweets, user_timeline, home_timeline, tweet_engagement
    - **Why Cassandra**: Write-heavy workload, global distribution, time-series data optimization
  - **Redis (Real-time & Caching)**
    - **Data**: User sessions, trending topics, rate limiting, real-time counters
    - **Why Redis**: Sub-millisecond response, atomic operations, pub/sub for real-time features
  - **PostgreSQL (User Management)**
    - **Tables**: users, followers, following, user_preferences, blocked_users
    - **Why PostgreSQL**: ACID compliance for relationships, complex queries for social graph
  - **Elasticsearch (Search & Discovery)**
    - **Indices**: tweet_search, user_search, trending_analysis
    - **Why Elasticsearch**: Full-text search, real-time analytics, faceted search
  - **S3/CDN (Media Storage)**
    - **Content**: Images, videos, GIFs, profile pictures, header images
    - **Why CDN**: Global media delivery, bandwidth optimization, cost-effective storage

## 2. Access Control & Security (RBAC)

### 2.1 Role-Based Access Control Implementation

```
User Access Levels:
├── Public User (Read-Only)
├── Registered User (Tweet & Interact)
├── Verified User (Enhanced Features)
├── Content Creator (Analytics Access)
├── Business Account (Advertising Features)
├── Content Moderator (Content Review)
├── Support Agent (User Assistance)
├── Trust & Safety (Policy Enforcement)
├── Data Analyst (Analytics Access)
├── System Admin (Platform Management)
└── Security Admin (Full System Access)
```

### 2.2 Permission Matrix

| Role           | View Tweets | Tweet         | Follow | Analytics      | Moderation | Admin Panel   | API Access |
| -------------- | ----------- | ------------- | ------ | -------------- | ---------- | ------------- | ---------- |
| Public         | ✅          | ❌            | ❌     | ❌             | ❌         | ❌            | Limited    |
| Registered     | ✅          | ✅            | ✅     | Basic          | ❌         | ❌            | Standard   |
| Verified       | ✅          | ✅ (Priority) | ✅     | Enhanced       | ❌         | ❌            | Enhanced   |
| Creator        | ✅          | ✅            | ✅     | Advanced       | ❌         | ❌            | Enhanced   |
| Business       | ✅          | ✅            | ✅     | Full           | ❌         | Limited       | Premium    |
| Moderator      | ✅          | ✅            | ✅     | Moderation     | ✅         | Moderation    | ❌         |
| Support        | ✅          | Limited       | ❌     | User Support   | Limited    | Support Tools | ❌         |
| Trust & Safety | ✅          | ✅            | ✅     | Policy Data    | Full       | Policy Tools  | ❌         |
| Data Analyst   | ✅          | ✅            | ✅     | Full Analytics | ❌         | Analytics     | Research   |
| System Admin   | ✅          | ✅            | ✅     | System Level   | ✅         | Full          | Full       |
| Security Admin | ✅          | ✅            | ✅     | Security Data  | ✅         | Full          | Full       |

## 2. How It Works

### 2.1 Tweet Publishing Flow

1. **Tweet Creation**

   - Content validation (character limit, spam detection)
   - Media upload and processing
   - Hashtag and mention extraction
   - URL shortening

2. **Tweet Distribution**

   - Fan-out to followers' timelines
   - Push to real-time streams
   - Index for search
   - Trigger notifications

3. **Timeline Updates**
   - Home timeline insertion
   - User timeline update
   - Cache invalidation
   - Real-time push to clients

### 2.2 Timeline Generation Strategies

- **Push Model (Fan-out on Write)**

  - Pre-compute timelines when tweet is published
  - Fast read performance
  - High write costs for celebrities
  - Used for regular users

- **Pull Model (Fan-out on Read)**

  - Compute timeline when user requests
  - Slower read performance
  - Lower storage costs
  - Used for celebrity accounts

- **Hybrid Model**
  - Combine both approaches
  - Smart routing based on user type
  - Optimize for both performance and cost

## 3. Key Features Implementation

### 3.1 Real-time Features

- **Live Timeline Updates**

  - WebSocket connections
  - Server-sent events
  - Real-time tweet insertion
  - Live engagement updates

- **Trending Topics**
  - Real-time hashtag tracking
  - Sliding window algorithms
  - Geographic and demographic trends
  - Spam and bot detection

### 3.2 Search & Discovery

- **Tweet Search**

  - Full-text search with Elasticsearch
  - Real-time indexing
  - Advanced filtering (date, location, media)
  - Relevance scoring

- **User Discovery**
  - People you may know
  - Interest-based suggestions
  - Network analysis algorithms
  - Activity-based recommendations

### 3.3 Content Moderation

- **Automated Detection**

  - Spam tweet identification
  - Hate speech detection
  - Bot account detection
  - Fake news flagging

- **Manual Review**
  - Human moderator workflow
  - Community reporting
  - Appeal processes
  - Policy enforcement

## 4. Scalability Architecture

### 4.1 Database Scaling

- **Tweet Storage Sharding**

  - Time-based partitioning
  - User-based sharding
  - Geographic distribution
  - Hot data optimization

- **Read Replicas**
  - Geographic distribution
  - Read traffic distribution
  - Eventual consistency
  - Cache-aside pattern

### 4.2 Caching Strategy

- **Multi-level Caching**

  - Browser cache (client-side)
  - CDN cache (edge servers)
  - Application cache (Redis)
  - Database cache (query cache)

- **Cache Invalidation**
  - Time-based expiration
  - Event-driven invalidation
  - Cache warming strategies
  - Consistent hashing

## 5. Performance Optimization

### 5.1 Timeline Optimization

- **Pre-computed Timelines**

  - Background timeline generation
  - Cache popular user timelines
  - Lazy loading for less active users
  - Timeline compression

- **Smart Pagination**
  - Cursor-based pagination
  - Efficient data retrieval
  - Smooth scrolling experience
  - Memory optimization

### 5.2 Media Optimization

- **Image Processing**

  - Multiple resolution generation
  - Format optimization (WebP, AVIF)
  - Lazy loading
  - Progressive image loading

- **Video Optimization**
  - Adaptive bitrate streaming
  - Video compression
  - Thumbnail generation
  - CDN distribution

## 6. Security & Privacy

### 6.1 Authentication & Authorization

- **OAuth 2.0 Implementation**

  - Secure token management
  - Rate limiting per user
  - API key management
  - Third-party integrations

- **Account Security**
  - Two-factor authentication
  - Suspicious activity detection
  - Device management
  - Password security

### 6.2 Content Security

- **Data Protection**

  - Encryption at rest and in transit
  - PII data handling
  - GDPR compliance
  - Data retention policies

- **Privacy Controls**
  - Tweet visibility settings
  - Follower management
  - Blocked accounts
  - Muted content

## 7. Analytics & Monitoring

### 7.1 Real-time Analytics

- **Engagement Metrics**

  - Tweet impressions and reach
  - Like, retweet, reply rates
  - User interaction patterns
  - Trending topic analytics

- **System Metrics**
  - API response times
  - Database performance
  - Cache hit rates
  - Error rates and patterns

### 7.2 Business Intelligence

- **User Behavior Analysis**

  - User journey mapping
  - Feature usage patterns
  - Retention analytics
  - Cohort analysis

- **Content Analytics**
  - Viral content patterns
  - Topic popularity trends
  - Sentiment analysis
  - Influencer identification

## 8. Reliability & Disaster Recovery

### 8.1 High Availability Design

- **Multi-region Deployment**

  - Active-active configuration
  - Automatic failover
  - Data synchronization
  - Load balancing

- **Circuit Breaker Pattern**
  - Service isolation
  - Graceful degradation
  - Automatic recovery
  - Health checks

### 8.2 Data Backup & Recovery

- **Backup Strategies**

  - Continuous data backup
  - Point-in-time recovery
  - Cross-region replication
  - Incremental backups

- **Disaster Recovery**
  - RTO/RPO definitions
  - Automated recovery procedures
  - Data consistency validation
  - Recovery testing

## 9. Capacity Planning

### 9.1 Traffic Patterns

- **Daily Activity Cycles**

  - Peak hours identification
  - Geographic distribution
  - Seasonal variations
  - Event-driven spikes

- **Growth Projections**
  - User acquisition rates
  - Tweet volume growth
  - Storage requirements
  - Infrastructure scaling

### 9.2 Resource Optimization

- **Auto-scaling Policies**

  - CPU and memory thresholds
  - Request rate monitoring
  - Predictive scaling
  - Cost optimization

- **Capacity Monitoring**
  - Resource utilization tracking
  - Performance bottleneck identification
  - Capacity planning alerts
  - Infrastructure right-sizing

## 10. API Design & Rate Limiting

### 10.1 API Architecture

- **RESTful Design**

  - Resource-based URLs
  - HTTP method conventions
  - Status code standards
  - Pagination support

- **GraphQL Implementation**
  - Flexible data fetching
  - Reduced over-fetching
  - Real-time subscriptions
  - Schema evolution

### 10.2 Rate Limiting & Abuse Prevention

- **Rate Limiting Strategies**

  - Token bucket algorithm
  - Sliding window counters
  - User-based limits
  - API endpoint specific limits

- **Abuse Detection**
  - Suspicious activity patterns
  - Bot detection algorithms
  - IP-based blocking
  - Behavioral analysis

## 11. Machine Learning Integration

### 11.1 Recommendation Systems

- **Timeline Ranking**

  - Relevance scoring algorithms
  - User interest modeling
  - Engagement prediction
  - Real-time personalization

- **Content Discovery**
  - Similar users recommendation
  - Topic-based suggestions
  - Trending content identification
  - Personalized trends

### 11.2 Content Analysis

- **Sentiment Analysis**

  - Tweet sentiment classification
  - Emotion detection
  - Brand sentiment tracking
  - Public opinion analysis

- **Content Classification**
  - Topic categorization
  - Language detection
  - Spam identification
  - Adult content filtering
