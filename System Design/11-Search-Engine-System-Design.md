# Search Engine System Design (Google-like)

## 1. System Architecture Overview

### 1.1 High-Level Architecture

```
[Web Crawlers] → [Content Processing] → [Index Building] → [Index Distribution]
        ↓                ↓                    ↓                  ↓
[URL Discovery] → [Text Extraction] → [Inverted Index] → [Query Processing]
        ↓                ↓                    ↓                  ↓
[Link Analysis] → [Quality Assessment] → [Ranking Signals] → [Result Generation]
        ↓                ↓                    ↓                  ↓
[PageRank Computation] → [Spam Detection] → [Personalization] → [User Interface]
```

**Why This Architecture?**

- **Distributed Crawling**: Handles billions of web pages across global infrastructure
- **Pipeline Processing**: Separates concerns for better scalability and maintainability
- **Index Distribution**: Enables fast query serving across geographic regions
- **Real-time Updates**: Incorporates fresh content while maintaining historical relevance

### 1.2 Working Logic Flow

1. **Web Crawling** → **Content Processing** → **Index Building** → **Query Serving** → **Result Ranking**
2. **Background Processes**: Link analysis, quality assessment, spam detection, index optimization

### 1.3 Core Components Detailed

- **Web Crawling Infrastructure**

  - **Purpose**: Systematically discover and download web content
  - **Component Details**:
    - Distributed crawler fleet with 100,000+ crawling agents
    - URL frontier management with priority queuing
    - Politeness policies respecting robots.txt and rate limits
    - Content change detection and incremental crawling
  - **Why Essential**: Foundation of search; must continuously discover new content while respecting web standards

- **Content Processing Pipeline**

  - **Purpose**: Extract and normalize textual content from web pages
  - **Responsibilities**:
    - HTML parsing and text extraction
    - Language detection and character encoding
    - Content quality assessment and spam detection
    - Duplicate content identification and deduplication
  - **Why Complex**: Web content is heterogeneous; must handle various formats, languages, and quality levels

- **Inverted Index System**
  - **Purpose**: Enable fast text search across billions of documents
  - **Implementation**: Distributed hash tables with term-based sharding
  - **Key Features**:
    - Term frequency and document frequency calculation
    - Positional information for phrase queries
    - Index compression for storage efficiency
    - Real-time index updates for fresh content
  - **Why Critical**: Core data structure enabling sub-second search across massive corpus

### 1.2 Technical Stack

- **Backend**: Java, C++, Python

  - Distributed systems (Apache Hadoop, Spark)
  - MapReduce for large-scale processing
  - Message queues (Apache Kafka)
  - Microservices architecture

- **Frontend**: React.js, Angular

  - Search interface
  - Autocomplete functionality
  - Search analytics dashboard

- **Infrastructure**
  - Container orchestration (Kubernetes)
  - Load balancers (Google Cloud Load Balancer)
  - CDN for global distribution

## 2. How It Works

### 2.1 Web Crawling Process

1. **URL Discovery**

   - Seed URLs from known websites
   - URL extraction from crawled pages
   - Sitemap parsing
   - Social media link discovery

2. **Crawl Scheduling**

   - Priority-based crawling
   - Freshness-based recrawling
   - Politeness policies (robots.txt)
   - Rate limiting per domain

3. **Content Processing**
   - HTML parsing and text extraction
   - Language detection
   - Content quality assessment
   - Spam and duplicate detection

### 2.2 Indexing Pipeline

1. **Document Processing**

   - Text preprocessing and normalization
   - Tokenization and stemming
   - Stop word removal
   - N-gram generation

2. **Index Building**

   - Inverted index construction
   - Term frequency calculation
   - Document frequency analysis
   - Index compression and optimization

3. **Index Distribution**
   - Shard-based index distribution
   - Replication for availability
   - Load balancing across shards

### 2.3 Query Processing & Ranking

1. **Query Understanding**

   - Query parsing and tokenization
   - Intent classification
   - Entity recognition
   - Query expansion

2. **Document Retrieval**

   - Index lookup and intersection
   - Boolean query processing
   - Phrase query handling
   - Wildcard and fuzzy matching

3. **Ranking & Scoring**
   - PageRank algorithm
   - TF-IDF scoring
   - Machine learning ranking models
   - Freshness and relevance signals

## 3. Key Features Implementation

### 3.1 PageRank Algorithm

- **Link Analysis**

  - Web graph construction
  - Link weight calculation
  - Authority and hub scores
  - Spam link detection

- **PageRank Computation**
  - Iterative algorithm implementation
  - Distributed computation
  - Convergence optimization
  - Update strategies

### 3.2 Search Relevance

- **TF-IDF Scoring**

  - Term frequency calculation
  - Inverse document frequency
  - Document length normalization
  - Field-specific weighting

- **Machine Learning Ranking**
  - Learning-to-rank algorithms
  - Feature engineering
  - Click-through rate modeling
  - Personalization signals

### 3.3 Query Optimization

- **Query Suggestions**

  - Autocomplete functionality
  - Popular query tracking
  - Personalized suggestions
  - Spell correction

- **Search Filters**
  - Time-based filtering
  - Content type filtering
  - Language and region filters
  - Advanced search operators

## 4. Scalability Architecture

### 4.1 Distributed Crawling

- **Crawler Coordination**

  - Distributed crawler fleet
  - URL frontier management
  - Duplicate URL detection
  - Crawl status coordination

- **Content Processing Pipeline**
  - Parallel document processing
  - Distributed index building
  - Stream processing for real-time updates
  - Batch processing for bulk operations

### 4.2 Index Sharding

- **Horizontal Sharding**

  - Term-based sharding
  - Document-based sharding
  - Hybrid sharding strategies
  - Consistent hashing

- **Index Replication**
  - Master-slave replication
  - Multi-region distribution
  - Eventual consistency
  - Failover mechanisms

## 5. Performance Optimization

### 5.1 Query Performance

- **Caching Strategy**

  - Query result caching
  - Autocomplete cache
  - Popular query preprocessing
  - CDN for static content

- **Index Optimization**
  - Index compression techniques
  - Skip lists for faster intersection
  - Bloom filters for negative lookups
  - Parallel query processing

### 5.2 Crawling Efficiency

- **Smart Crawling**

  - Content change detection
  - Incremental crawling
  - Priority-based scheduling
  - Resource-aware crawling

- **Content Processing**
  - Parallel parsing pipeline
  - Efficient text processing
  - Memory-optimized algorithms
  - GPU acceleration for ML tasks

## 6. Data Storage Design

### 6.1 Inverted Index Structure

```
Term: "python"
├── Document 1: {tf: 5, positions: [1, 15, 23, 45, 67]}
├── Document 2: {tf: 3, positions: [8, 34, 56]}
├── Document 3: {tf: 7, positions: [2, 12, 28, 41, 53, 64, 78]}
└── ...
```

### 6.2 Document Storage

```javascript
// Document Metadata
{
  "docId": "doc_12345",
  "url": "https://example.com/page",
  "title": "Python Programming Guide",
  "lastModified": "2024-01-01T12:00:00Z",
  "contentLength": 2048,
  "language": "en",
  "pageRank": 0.85,
  "inboundLinks": 150,
  "contentHash": "sha256_hash"
}
```

- **Data Storage & Processing Architecture**
  - **Distributed File System (HDFS)**
    - **Content**: Raw web pages, crawl logs, link graphs
    - **Why HDFS**: Handles petabytes of data, fault tolerance, batch processing integration
  - **Cassandra/HBase (Index Storage)**
    - **Tables**: inverted_index, document_metadata, link_graph, pagerank_scores
    - **Why NoSQL**: Linear scalability, high write throughput, distributed architecture
  - **PostgreSQL (Metadata)**
    - **Tables**: crawl_urls, crawl_status, crawler_config, quality_signals
    - **Why PostgreSQL**: ACID compliance for operational data, complex queries for analytics
  - **Redis (Real-time Cache)**
    - **Data**: Query results, autocomplete suggestions, trending queries, user sessions
    - **Why Redis**: Sub-millisecond query response, real-time features, session management
  - **Elasticsearch (Search Analytics)**
    - **Indices**: search_logs, user_behavior, performance_metrics
    - **Why Elasticsearch**: Real-time analytics, complex aggregations, search optimization

## 2. Access Control & Security (RBAC)

### 2.1 Role-Based Access Control Implementation

```
User Access Levels:
├── Public User (Basic Search)
├── Premium User (Advanced Features)
├── Developer (API Access)
├── Content Publisher (Search Console)
├── SEO Specialist (Analytics Access)
├── Quality Rater (Relevance Evaluation)
├── Operations Engineer (System Monitoring)
├── Search Engineer (Algorithm Access)
├── System Admin (Infrastructure Control)
└── Security Admin (Full System Access)
```

### 2.2 Permission Matrix

| Role            | Search      | API Access     | Analytics       | Content Tools | Quality Rating | System Config | Infrastructure |
| --------------- | ----------- | -------------- | --------------- | ------------- | -------------- | ------------- | -------------- |
| Public User     | ✅          | Limited        | ❌              | ❌            | ❌             | ❌            | ❌             |
| Premium User    | ✅ Enhanced | Moderate       | Basic           | ❌            | ❌             | ❌            | ❌             |
| Developer       | ✅          | Full API       | Usage Stats     | ❌            | ❌             | ❌            | ❌             |
| Publisher       | ✅          | Search Console | Site Analytics  | ✅            | ❌             | ❌            | ❌             |
| SEO Specialist  | ✅          | Analytics API  | Full Analytics  | ✅            | ❌             | ❌            | ❌             |
| Quality Rater   | ✅          | ❌             | Quality Metrics | ❌            | ✅             | ❌            | ❌             |
| Operations      | ✅          | ✅             | System Metrics  | Limited       | ❌             | Monitoring    | ❌             |
| Search Engineer | ✅          | ✅             | Full Analytics  | ✅            | ✅             | Algorithm     | Limited        |
| System Admin    | ✅          | ✅             | Full            | ✅            | ✅             | Full          | Limited        |
| Security Admin  | ✅          | ✅             | Full            | ✅            | ✅             | Full          | Full           |

### 2.3 Authentication & Authorization Flow

1. **API Authentication**

   - **API Key Management**:

     - Tiered API keys based on usage quotas
     - Rate limiting per key and per IP
     - Geographic access restrictions
     - **Why API Keys**: Simple integration, quota management, abuse prevention

   - **OAuth 2.0 for Premium Features**:
     - User consent for personalized search
     - Scope-based permissions for different features
     - Refresh token rotation for security
     - **Why OAuth**: User control over data access, secure third-party integration

2. **Internal System Security**

   - **Service-to-Service Authentication**:
     - Mutual TLS for service communication
     - Service mesh for secure internal networking
     - Certificate rotation and management
     - **Why mTLS**: Prevents internal service impersonation, encrypted communication

3. **Data Access Controls**
   - **Query Filtering**:
     - Geographic content restrictions
     - Safe search filtering based on user settings
     - Personalization opt-out compliance
     - **Implementation**: Query middleware with user preference validation

### 2.4 Privacy & Data Protection

- **User Privacy**:

  - Search query anonymization after 18 months
  - IP address truncation for logs
  - User control over search history
  - **Implementation**: Privacy-preserving analytics with differential privacy

- **Content Protection**:
  - DMCA takedown request processing
  - Copyright violation detection
  - Adult content filtering
  - **Implementation**: Automated content classification with human review

## 3. Database Design & Table-Level Explanation

### 3.1 PostgreSQL (Crawl Management & Metadata)

**Why PostgreSQL?**

- ACID compliance for crawl coordination
- Complex queries for crawl analytics
- JSON support for flexible metadata
- Strong consistency for operational data

#### Core Tables:

**URLs Table**

```sql
CREATE TABLE crawl_urls (
    url_id BIGSERIAL PRIMARY KEY,
    url VARCHAR(2048) UNIQUE NOT NULL,
    domain VARCHAR(255) NOT NULL,
    url_hash VARCHAR(64) UNIQUE NOT NULL, -- SHA256 of URL
    priority INTEGER DEFAULT 5, -- 1 (highest) to 10 (lowest)
    crawl_frequency INTERVAL DEFAULT '7 days',
    last_crawled TIMESTAMP,
    next_crawl_time TIMESTAMP,
    crawl_status VARCHAR(20) DEFAULT 'pending', -- pending, crawling, completed, failed
    http_status INTEGER,
    content_length BIGINT,
    content_type VARCHAR(100),
    language VARCHAR(10),
    last_modified TIMESTAMP,
    etag VARCHAR(255),
    robots_allowed BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_domain (domain),
    INDEX idx_crawl_status (crawl_status),
    INDEX idx_next_crawl_time (next_crawl_time),
    INDEX idx_url_hash (url_hash),
    INDEX idx_priority_next_crawl (priority, next_crawl_time)
);
```

**Purpose**: Central registry of all discoverable URLs with crawl scheduling
**Why These Fields**: Priority for crawl ordering, frequency for freshness, status for monitoring

**Page_Quality Table**

```sql
CREATE TABLE page_quality (
    url_id BIGINT REFERENCES crawl_urls(url_id),
    pagerank_score DECIMAL(10,8),
    domain_authority DECIMAL(8,4),
    content_quality_score DECIMAL(8,4),
    spam_score DECIMAL(8,4),
    mobile_friendly BOOLEAN,
    page_speed_score INTEGER, -- 0-100
    https_enabled BOOLEAN,
    inbound_links_count INTEGER,
    outbound_links_count INTEGER,
    word_count INTEGER,
    image_count INTEGER,
    last_updated TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (url_id),
    INDEX idx_pagerank_score (pagerank_score DESC),
    INDEX idx_quality_score (content_quality_score DESC),
    INDEX idx_spam_score (spam_score ASC)
);
```

**Purpose**: Store quality signals for ranking algorithm
**Why These Fields**: Multiple ranking factors, separate quality dimensions

### 3.2 Cassandra/HBase (Inverted Index & Link Graph)

**Why Cassandra?**

- Linear scalability for massive index
- High write throughput for real-time updates
- Tunable consistency for different use cases
- Geographic distribution for global search

#### Core Column Families:

**Inverted Index**

```cql
CREATE TABLE inverted_index (
    term TEXT,
    shard_id INT,
    document_id TEXT,
    term_frequency INT,
    document_frequency INT,
    position_list LIST<INT>, -- word positions in document
    field_data MAP<TEXT, INT>, -- title:3, body:15, anchor:1
    quality_score DECIMAL,
    PRIMARY KEY ((term, shard_id), quality_score, document_id)
) WITH CLUSTERING ORDER BY (quality_score DESC, document_id ASC);
```

**Purpose**: Core search index enabling fast text retrieval
**Why This Design**: Partitioned by term for scalable queries, ordered by quality for relevance

**Link Graph**

```cql
CREATE TABLE link_graph (
    source_domain TEXT,
    source_url_hash TEXT,
    target_domain TEXT,
    target_url_hash TEXT,
    anchor_text TEXT,
    link_type TEXT, -- internal, external, nofollow
    discovered_at TIMESTAMP,
    link_quality_score DECIMAL,
    PRIMARY KEY ((source_domain), target_domain, target_url_hash)
);
```

**Purpose**: Web link relationships for PageRank computation
**Why This Design**: Enables efficient link analysis, supports graph algorithms

### 3.3 Redis (Query Cache & Real-time Features)

**Why Redis?**

- Sub-millisecond query response times
- Real-time autocomplete suggestions
- Session management for personalization
- Atomic operations for counters

#### Data Structures:

**Query Results Cache**

```redis
Key Pattern: query:{query_hash}:{region}:{personalization_hash}
Data Type: String (JSON)
Value: {
  "results": [
    {
      "url": "https://example.com",
      "title": "Example Page",
      "snippet": "Relevant content snippet...",
      "score": 0.95
    }
  ],
  "total_results": 1500000,
  "execution_time_ms": 85,
  "cached_at": "timestamp"
}
TTL: 15 minutes
```

**Purpose**: Cache search results for repeated queries
**Why JSON with TTL**: Balance between performance and freshness

**Autocomplete Suggestions**

```redis
Key Pattern: autocomplete:{prefix}:{language}
Data Type: Sorted Set
Score: Query frequency, Member: Suggested query
Max Size: 10 suggestions per prefix
```

**Purpose**: Provide real-time search suggestions
**Why Sorted Set**: Automatic ranking by popularity, efficient prefix queries

**Search Analytics**

```redis
Key Pattern: analytics:query_count:{date}:{hour}
Data Type: Hash
Fields: {
  "query1": "count1",
  "query2": "count2",
  ...
}
TTL: 30 days
```

**Purpose**: Real-time query volume tracking
**Why Hash with TTL**: Efficient aggregation, automatic data retention

## 4. System Working Logic & Data Flow

### 4.1 Web Crawling Process (Detailed)

```
[Seed URLs] → [URL Discovery] → [Politeness Check] → [Content Fetch] → [Content Processing]
     ↓             ↓                ↓                    ↓                  ↓
[Frontier Queue] → [Link Extraction] → [Robots.txt] → [HTTP Headers] → [Text Extraction]
     ↓             ↓                ↓                    ↓                  ↓
[Priority Scoring] → [Duplicate Detection] → [Rate Limiting] → [Content Quality] → [Index Update]
```

**Step-by-Step Process:**

1. **URL Frontier Management**

   - Maintains priority queue of URLs to crawl
   - Politeness delays per domain (1-10 seconds)
   - Geographic distribution of crawl load
   - **Why Priority Queue**: Ensures important content is crawled first

2. **Content Fetching**

   - Parallel HTTP requests with connection pooling
   - Conditional requests using ETags and Last-Modified
   - Content-Type validation and size limits
   - **Why Conditional**: Reduces bandwidth and processing for unchanged content

3. **Content Processing Pipeline**
   - HTML parsing and text extraction
   - Language detection using n-gram analysis
   - Content quality assessment using ML models
   - **Why Pipeline**: Separates concerns, enables parallel processing

### 4.2 Index Building & Distribution

```
[Text Processing] → [Tokenization] → [Term Extraction] → [Index Sharding] → [Index Distribution]
        ↓               ↓                ↓                   ↓                    ↓
[Language Detection] → [Stemming] → [TF-IDF Calculation] → [Compression] → [Global Deployment]
```

**Index Building Process:**

1. **Text Preprocessing**

   - Tokenization with language-specific rules
   - Stop word removal and stemming
   - N-gram generation for phrase queries
   - **Why Preprocessing**: Improves search accuracy and reduces index size

2. **Term-Document Mapping**

   - Calculate term frequency within documents
   - Compute inverse document frequency across corpus
   - Generate positional information for phrase matching
   - **Why TF-IDF**: Balances term importance with document relevance

3. **Index Sharding Strategy**
   - Horizontal partitioning by term hash
   - Replication across geographic regions
   - Load balancing based on query patterns
   - **Why Sharding**: Enables parallel query processing, improves response times
