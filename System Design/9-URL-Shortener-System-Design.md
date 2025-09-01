# URL Shortener (bit.ly) System Design

## 1. Core Logic & Architecture

### 1.1 Key Components

- **URL Shortening Service**

  - Long URL to short URL conversion
  - Custom alias support
  - URL validation and sanitization
  - Expiration date management
  - Bulk URL processing

- **Data Storage**
  - **PostgreSQL**: URL mappings, user accounts, analytics
  - **Redis**: Caching popular URLs, rate limiting
  - **Cassandra**: Click analytics, time-series data
  - **Elasticsearch**: URL search and analytics

### 1.2 Technical Stack

- **Backend**: Java (Spring Boot), Go

  - Microservices architecture
  - REST APIs
  - Asynchronous processing
  - Message queues (Apache Kafka)

- **Frontend**: React.js

  - Single Page Application
  - Dashboard for analytics
  - URL management interface

- **Infrastructure**
  - Load balancers (NGINX)
  - CDN for global distribution
  - Auto-scaling groups

## 2. How It Works

### 2.1 URL Shortening Flow

1. **URL Submission**

   - Validate URL format and accessibility
   - Check for malicious content
   - Normalize URL (remove tracking parameters)
   - Check for existing mapping

2. **Short URL Generation**

   - Base62 encoding (a-z, A-Z, 0-9)
   - Counter-based approach
   - Hash-based approach (MD5/SHA)
   - Custom alias validation

3. **Storage & Caching**
   - Store mapping in database
   - Cache popular URLs in Redis
   - Generate QR code
   - Set expiration policies

### 2.2 URL Redirection Flow

1. **Short URL Access**

   - Extract short code from URL
   - Check cache for mapping
   - Query database if not cached
   - Validate URL expiration

2. **Analytics Collection**

   - Log click event asynchronously
   - Capture user agent, IP, referer
   - Geographic location detection
   - Bot detection and filtering

3. **Redirection**
   - HTTP 301 (permanent) or 302 (temporary)
   - Update cache with fresh data
   - Handle error cases gracefully

## 3. Key Features Implementation

### 3.1 URL Encoding Strategies

- **Base62 Encoding**

  ```
  Characters: [a-z, A-Z, 0-9] = 62 characters
  7-character short URL = 62^7 = ~3.5 trillion URLs
  ```

- **Counter-based Generation**

  - Sequential counter for unique IDs
  - Distributed counter with ranges
  - Base62 encode the counter value
  - Predictable but efficient

- **Hash-based Generation**
  - MD5/SHA hash of original URL
  - Take first 7 characters
  - Handle hash collisions
  - Less predictable but collision-prone

### 3.2 Custom Aliases

- **Alias Validation**

  - Check for existing aliases
  - Validate against reserved words
  - Length and character restrictions
  - Profanity filtering

- **Premium Features**
  - Custom domains
  - Branded short URLs
  - Vanity URLs
  - Bulk custom alias creation

### 3.3 Analytics & Tracking

- **Click Analytics**

  - Real-time click counting
  - Geographic distribution
  - Device and browser analysis
  - Referrer tracking

- **Time-series Analytics**
  - Hourly/Daily/Monthly trends
  - Peak traffic identification
  - Click heatmaps
  - Conversion tracking

## 4. Database Design

### 4.1 URL Mapping Schema

```sql
-- URLs table
CREATE TABLE urls (
    id BIGSERIAL PRIMARY KEY,
    short_code VARCHAR(10) UNIQUE NOT NULL,
    original_url TEXT NOT NULL,
    user_id BIGINT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    expires_at TIMESTAMP,
    click_count BIGINT DEFAULT 0,
    is_active BOOLEAN DEFAULT true
);

-- Click analytics table
CREATE TABLE clicks (
    id BIGSERIAL PRIMARY KEY,
    short_code VARCHAR(10) NOT NULL,
    clicked_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    ip_address INET,
    user_agent TEXT,
    referer TEXT,
    country VARCHAR(2),
    city VARCHAR(100)
);
```

### 4.2 Indexing Strategy

- **Primary Indexes**

  - Short code unique index
  - User ID index for user queries
  - Created_at index for time-based queries

- **Composite Indexes**
  - (short_code, is_active) for active URL lookups
  - (user_id, created_at) for user timeline
  - (clicked_at, short_code) for analytics

## 5. Scalability Considerations

### 5.1 Database Scaling

- **Sharding Strategy**

  - Range-based sharding by short_code
  - Hash-based sharding by user_id
  - Geographic sharding for analytics

- **Read Replicas**
  - Multiple read replicas per region
  - Read-write splitting
  - Eventual consistency for analytics

### 5.2 Caching Strategy

- **Multi-level Caching**

  - Browser cache (Cache-Control headers)
  - CDN cache for popular URLs
  - Application cache (Redis)
  - Database query cache

- **Cache Warming**
  - Pre-load popular URLs
  - Predictive caching based on patterns
  - Background cache refresh
  - Cache hierarchy optimization

## 6. Performance Optimization

### 6.1 Latency Optimization

- **Geographic Distribution**

  - Global CDN deployment
  - Regional data centers
  - Edge computing for redirects
  - DNS-based routing

- **Connection Optimization**
  - HTTP/2 support
  - Connection pooling
  - Keep-alive connections
  - Compression (gzip/brotli)

### 6.2 Throughput Optimization

- **Asynchronous Processing**

  - Non-blocking I/O
  - Background analytics processing
  - Message queue for click events
  - Batch processing for bulk operations

- **Resource Optimization**
  - Memory-efficient data structures
  - Connection pool tuning
  - Thread pool optimization
  - Garbage collection tuning

## 7. Security & Safety

### 7.1 URL Validation & Safety

- **Malicious URL Detection**

  - Blacklist checking
  - Machine learning-based detection
  - Real-time threat intelligence
  - Periodic re-scanning

- **Content Filtering**
  - Adult content detection
  - Phishing URL identification
  - Malware scanning
  - Social engineering detection

### 7.2 Abuse Prevention

- **Rate Limiting**

  - Per-user rate limits
  - IP-based rate limiting
  - Adaptive rate limiting
  - CAPTCHA integration

- **Spam Prevention**
  - URL deduplication
  - Suspicious pattern detection
  - Account verification
  - Honeypot URLs

## 8. Analytics & Monitoring

### 8.1 Real-time Analytics

- **Click Tracking**

  - Real-time click streams
  - Geographic click distribution
  - Device and browser analytics
  - Bot traffic filtering

- **Performance Metrics**
  - Response time monitoring
  - Cache hit rates
  - Error rate tracking
  - Throughput metrics

### 8.2 Business Analytics

- **Usage Patterns**

  - Popular domains and content
  - User behavior analysis
  - Peak usage times
  - Conversion funnel analysis

- **Revenue Analytics**
  - Premium feature adoption
  - Customer lifetime value
  - Pricing optimization
  - Market segmentation

## 9. API Design

### 9.1 RESTful API Endpoints

```
POST /api/v1/shorten
{
  "url": "https://example.com/very/long/url",
  "custom_alias": "mylink",
  "expires_at": "2024-12-31T23:59:59Z"
}

GET /api/v1/urls/{short_code}
GET /api/v1/urls/{short_code}/analytics
DELETE /api/v1/urls/{short_code}
```

### 9.2 Rate Limiting & Authentication

- **API Rate Limits**

  - Free tier: 100 requests/hour
  - Premium tier: 10,000 requests/hour
  - Burst allowance for peak usage
  - Graceful degradation

- **Authentication Methods**
  - API key authentication
  - OAuth 2.0 for third-party apps
  - JWT tokens for web clients
  - IP whitelisting for enterprise

## 10. Reliability & Disaster Recovery

### 10.1 High Availability

- **Multi-region Deployment**

  - Active-active configuration
  - Automatic failover
  - Data synchronization
  - Load balancing

- **Circuit Breaker Pattern**
  - Service isolation
  - Graceful degradation
  - Health checks
  - Automatic recovery

### 10.2 Data Backup & Recovery

- **Backup Strategies**

  - Continuous backup
  - Point-in-time recovery
  - Cross-region replication
  - Data versioning

- **Disaster Recovery**
  - RTO: 15 minutes
  - RPO: 5 minutes
  - Automated failover
  - Data consistency checks

## 11. Capacity Planning

### 11.1 Traffic Estimation

- **Read vs Write Ratio**

  - 100:1 read to write ratio
  - Burst traffic during viral content
  - Geographic traffic patterns
  - Seasonal variations

- **Storage Requirements**
  - URL data: ~500 bytes per URL
  - Analytics data: ~200 bytes per click
  - Growth rate: 1M new URLs/day
  - Retention: 5 years for active URLs

### 11.2 Infrastructure Scaling

- **Auto-scaling Policies**

  - CPU-based scaling
  - Request rate scaling
  - Queue depth monitoring
  - Predictive scaling

- **Resource Planning**
  - Database capacity planning
  - Cache memory requirements
  - Network bandwidth needs
  - Storage growth projections

## 12. Competitive Features

### 12.1 Advanced Analytics

- **A/B Testing Support**

  - Traffic splitting
  - Conversion tracking
  - Statistical significance
  - Campaign optimization

- **Attribution Tracking**
  - Multi-touch attribution
  - Cross-device tracking
  - Conversion paths
  - ROI measurement

### 12.2 Enterprise Features

- **White-label Solutions**

  - Custom branding
  - Domain customization
  - API white-labeling
  - Dedicated infrastructure

- **Integration Capabilities**
  - Webhook notifications
  - Third-party integrations
  - Marketing automation
  - CRM integration
