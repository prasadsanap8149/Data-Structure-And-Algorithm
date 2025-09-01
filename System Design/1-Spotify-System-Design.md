# Spotify System Design

## 1. System Architecture Overview

### 1.1 High-Level Architecture

```
[Mobile/Web Clients] → [CDN/Load Balancer] → [API Gateway] → [Microservices]
                                                                      ↓
[Authentication Service] ← [User Service] ← [Music Service] ← [Recommendation Service]
                                ↓                    ↓                      ↓
                        [User Database]    [Music Database]    [Analytics Database]
                                ↓                    ↓                      ↓
                            [Cache Layer] ← [Message Queue] → [Real-time Processing]
```

**Why This Architecture?**

- **Microservices**: Enables independent scaling of different functionalities (user management, music streaming, recommendations)
- **API Gateway**: Single entry point for all client requests, handles authentication, rate limiting, and routing
- **CDN**: Reduces latency by serving audio content from geographically closer servers
- **Cache Layer**: Improves response times for frequently accessed data like popular songs and user preferences

### 1.2 Working Logic Flow

1. **User Authentication** → **Content Discovery** → **Music Streaming** → **Real-time Analytics**
2. **Background Processes**: Recommendation engine training, content preprocessing, analytics aggregation

### 1.3 Core Components Detailed

- **Content Delivery Network (CDN)**

  - **Purpose**: Distribute audio content globally to reduce latency
  - **Component Details**:
    - Edge servers in 50+ locations worldwide
    - Audio chunk caching (segments of 10-15 seconds)
    - Adaptive bitrate streaming support
    - Automatic failover between edge servers
  - **Why Essential**: Music streaming requires low latency; users expect instant playback

- **API Gateway**

  - **Purpose**: Single entry point for all client requests
  - **Responsibilities**:
    - Request routing to appropriate microservices
    - Authentication and authorization validation
    - Rate limiting and throttling
    - Request/response transformation
    - API versioning management
  - **Why Needed**: Centralizes cross-cutting concerns and simplifies client integration

- **Data Storage Architecture**
  - **PostgreSQL (Primary Database)**
    - **Tables**: users, playlists, subscriptions, user_preferences
    - **Why PostgreSQL**: ACID compliance for financial transactions, complex queries for user management
  - **Cassandra (Music Metadata & Analytics)**
    - **Tables**: songs, albums, artists, play_history, user_interactions
    - **Why Cassandra**: High write throughput for analytics, linear scalability, geographic distribution
  - **Redis (Caching & Real-time)**
    - **Data**: Session tokens, recently played songs, real-time user presence
    - **Why Redis**: Sub-millisecond response times, pub/sub for real-time features
  - **S3/Blob Storage (Audio Files)**
    - **Content**: Raw audio files, album artwork, podcast episodes
    - **Why Object Storage**: Cost-effective for large files, global replication, CDN integration

### 1.4 Technical Stack & Justification

- **Backend**: Java (Spring Boot)

  - **Microservices Architecture**:
    - User Service, Music Service, Recommendation Service, Payment Service
    - Each service owns its data and can scale independently
    - **Why Java**: Enterprise-grade performance, extensive ecosystem, strong concurrent programming
  - **RESTful APIs**:
    - Stateless communication between services
    - Standard HTTP methods for CRUD operations
    - **Why REST**: Simple, cacheable, stateless, widely supported
  - **WebSocket for Real-time Features**:
    - Live friend activity updates
    - Real-time collaboration on playlists
    - **Why WebSocket**: Bidirectional communication, low latency for real-time features

- **Frontend**: Angular

  - **SPA (Single Page Application)**:
    - Client-side routing and state management
    - Reduced server load and improved user experience
    - **Why SPA**: Faster navigation, better user experience, offline capabilities
  - **Web Audio API**:
    - Native browser audio processing
    - Audio visualization and effects
    - **Why Web Audio API**: Hardware-accelerated audio processing, better performance
  - **Progressive Web App (PWA)**:
    - Offline music playback
    - App-like experience on mobile browsers
    - **Why PWA**: Cross-platform compatibility, app store independence

- **Mobile**: Flutter
  - **Cross-platform Development**:
    - Single codebase for iOS and Android
    - Native performance with platform-specific optimizations
    - **Why Flutter**: Faster development, consistent UI, near-native performance
  - **Native Audio Integration**:
    - Platform-specific audio APIs
    - Background audio playback
  - **Offline Capabilities**:
    - Local storage for downloaded music
    - Sync when connection is restored

## 2. Access Control & Security (RBAC)

### 2.1 Role-Based Access Control Implementation

```
User Roles Hierarchy:
├── Guest User (Limited Access)
├── Free User (Basic Features)
├── Premium User (Full Features)
├── Artist (Content Creation + Analytics)
├── Label Manager (Multiple Artists)
├── Content Moderator (Content Review)
├── System Admin (Full System Access)
└── Super Admin (Infrastructure Access)
```

### 2.2 Permission Matrix

| Role              | Stream Music | Download | Upload | Analytics  | Admin Panel | System Config |
| ----------------- | ------------ | -------- | ------ | ---------- | ----------- | ------------- |
| Guest             | Limited      | ❌       | ❌     | ❌         | ❌          | ❌            |
| Free User         | ✅ (Ads)     | ❌       | ❌     | Basic      | ❌          | ❌            |
| Premium           | ✅           | ✅       | ❌     | Personal   | ❌          | ❌            |
| Artist            | ✅           | ✅       | ✅     | Advanced   | Limited     | ❌            |
| Label Manager     | ✅           | ✅       | ✅     | Full       | Moderate    | ❌            |
| Content Moderator | ✅           | ✅       | Review | Moderation | Yes         | ❌            |
| System Admin      | ✅           | ✅       | ✅     | Full       | Full        | Limited       |
| Super Admin       | ✅           | ✅       | ✅     | Full       | Full        | Full          |

### 2.3 Authentication & Authorization Flow

1. **Authentication Service**

   - **OAuth 2.0 Implementation**:

     - Authorization server validates credentials
     - Issues JWT tokens with role information
     - **Why OAuth 2.0**: Industry standard, secure, supports multiple grant types

   - **Multi-Factor Authentication (MFA)**:
     - SMS, Email, or App-based verification
     - Required for admin accounts and payment operations
     - **Why MFA**: Additional security layer against account compromise

2. **Authorization Middleware**
   - **JWT Token Validation**:
     - Verifies token signature and expiration
     - Extracts user roles and permissions
   - **Permission Checking**:
     - Each API endpoint has required permissions
     - Real-time permission validation
   - **Rate Limiting by Role**:
     - Different API rate limits based on user tier
     - Premium users get higher limits

### 2.4 Data Access Control

- **Row-Level Security (RLS)**:

  - Users can only access their own playlists and data
  - Artists can only modify their own content
  - **Implementation**: Database-level security policies

- **API-Level Filtering**:
  - Geographic content restrictions
  - Age-appropriate content filtering
  - **Implementation**: Middleware filters based on user location and age

## 3. Database Design & Table-Level Explanation

### 3.1 PostgreSQL (Primary OLTP Database)

**Why PostgreSQL?**

- ACID compliance for financial transactions
- Complex queries for user management and analytics
- JSON support for flexible user preferences
- Strong consistency for critical business data

#### Core Tables:

**Users Table**

```sql
CREATE TABLE users (
    user_id BIGSERIAL PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    username VARCHAR(100) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    subscription_type VARCHAR(20) DEFAULT 'free', -- free, premium, family, student
    country_code VARCHAR(2) NOT NULL,
    date_of_birth DATE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    last_login TIMESTAMP,
    is_active BOOLEAN DEFAULT true,
    preferences JSONB, -- Flexible preference storage
    INDEX idx_email (email),
    INDEX idx_subscription (subscription_type),
    INDEX idx_country (country_code)
);
```

**Purpose**: Central user management and subscription tracking
**Why These Fields**: Email for authentication, subscription for feature access, country for content licensing

**Playlists Table**

```sql
CREATE TABLE playlists (
    playlist_id BIGSERIAL PRIMARY KEY,
    user_id BIGINT REFERENCES users(user_id),
    name VARCHAR(255) NOT NULL,
    description TEXT,
    is_public BOOLEAN DEFAULT false,
    is_collaborative BOOLEAN DEFAULT false,
    cover_image_url VARCHAR(500),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    song_count INTEGER DEFAULT 0,
    total_duration INTEGER DEFAULT 0, -- in seconds
    INDEX idx_user_id (user_id),
    INDEX idx_public (is_public),
    INDEX idx_collaborative (is_collaborative)
);
```

**Purpose**: Organize user music collections
**Why These Fields**: Collaborative flag for shared playlists, public flag for discovery

**Playlist_Songs Table**

```sql
CREATE TABLE playlist_songs (
    playlist_id BIGINT REFERENCES playlists(playlist_id),
    song_id VARCHAR(50) NOT NULL, -- References Cassandra
    position INTEGER NOT NULL,
    added_by BIGINT REFERENCES users(user_id),
    added_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (playlist_id, song_id),
    INDEX idx_position (playlist_id, position)
);
```

**Purpose**: Many-to-many relationship between playlists and songs
**Why This Design**: Maintains song order, tracks who added each song

### 3.2 Cassandra (Music Metadata & Analytics)

**Why Cassandra?**

- High write throughput for analytics events
- Linear scalability for massive music catalog
- Geographic distribution for global content
- Time-series data for user interactions

#### Core Column Families:

**Songs Column Family**

```cql
CREATE TABLE songs (
    song_id TEXT PRIMARY KEY,
    title TEXT,
    artist_id TEXT,
    album_id TEXT,
    duration INTEGER, -- in seconds
    genre TEXT,
    release_date DATE,
    audio_file_url TEXT,
    bitrate_options MAP<TEXT, TEXT>, -- quality -> url mapping
    lyrics TEXT,
    explicit_content BOOLEAN,
    country_restrictions SET<TEXT>,
    created_at TIMESTAMP,
    play_count COUNTER
);
```

**Purpose**: Central music catalog with metadata
**Why This Design**: Denormalized for fast reads, counter for play statistics

**User_Play_History Column Family**

```cql
CREATE TABLE user_play_history (
    user_id TEXT,
    play_date DATE,
    play_time TIMESTAMP,
    song_id TEXT,
    duration_played INTEGER,
    device_type TEXT,
    platform TEXT,
    PRIMARY KEY ((user_id, play_date), play_time)
) WITH CLUSTERING ORDER BY (play_time DESC);
```

**Purpose**: Track user listening behavior for recommendations
**Why This Design**: Partitioned by user and date for efficient queries

**Song_Analytics Column Family**

```cql
CREATE TABLE song_analytics (
    song_id TEXT,
    date DATE,
    hour INTEGER,
    play_count COUNTER,
    skip_count COUNTER,
    like_count COUNTER,
    share_count COUNTER,
    PRIMARY KEY ((song_id, date), hour)
);
```

**Purpose**: Real-time analytics for content performance
**Why This Design**: Time-based partitioning for analytical queries

### 3.3 Redis (Caching & Real-time Data)

**Why Redis?**

- Sub-millisecond response times for frequently accessed data
- Pub/Sub capabilities for real-time features
- Session management with automatic expiration
- Atomic operations for counters and sets

#### Data Structures:

**User Sessions**

```redis
Key Pattern: session:{user_id}
Data Type: Hash
Fields: {
    "token": "jwt_token_string",
    "device_id": "unique_device_identifier",
    "last_activity": "timestamp",
    "current_song": "song_id",
    "queue": "serialized_song_list"
}
TTL: 24 hours (auto-refresh on activity)
```

**Purpose**: Maintain user session state across requests
**Why Hash**: Efficient partial updates and field access

**Recently Played Cache**

```redis
Key Pattern: recent:{user_id}
Data Type: Sorted Set
Score: timestamp, Member: song_id
Max Size: 50 songs per user
```

**Purpose**: Quick access to user's recent listening history
**Why Sorted Set**: Automatic ordering by timestamp, efficient range queries

**Real-time Presence**

```redis
Key Pattern: presence:online
Data Type: Set
Members: user_id list
TTL: 5 minutes (refreshed by heartbeat)
```

**Purpose**: Track which users are currently online
**Why Set**: Fast membership testing and atomic operations

## 4. System Working Logic & Data Flow

### 4.1 Audio Streaming Process (Detailed)

```
[Client Request] → [Load Balancer] → [API Gateway] → [Music Service]
                                                            ↓
[CDN Selection] ← [Geo-location Service] ← [Content Metadata]
       ↓
[Audio Chunks] → [Adaptive Bitrate] → [Buffer Management] → [Client Playback]
```

**Step-by-Step Process:**

1. **Song Request Initiation**

   - Client sends song ID and quality preference
   - API Gateway validates user subscription and permissions
   - **Why This Step**: Ensures user has rights to access content

2. **CDN Selection Algorithm**

   - Geo-location service determines user's location
   - Selects nearest CDN edge server with the content
   - Falls back to next closest if primary is unavailable
   - **Why Geographic Selection**: Reduces latency from ~500ms to ~50ms

3. **Audio File Segmentation**

   - Audio files pre-segmented into 10-15 second chunks
   - Multiple bitrate versions (128kbps, 320kbps, lossless)
   - Each chunk independently streamable
   - **Why Segmentation**: Enables adaptive streaming and faster start times

4. **Adaptive Bitrate Streaming**

   - Monitors user's bandwidth in real-time
   - Automatically adjusts quality to prevent buffering
   - Algorithm: Start with medium quality, adjust based on buffer health
   - **Why Adaptive**: Maintains smooth playback across varying network conditions

5. **Buffer Management**
   - Client maintains 30-60 seconds of audio buffer
   - Preloads next song when current song is 80% complete
   - Implements circular buffer to manage memory
   - **Why Buffering**: Prevents playback interruptions due to network hiccups

### 4.2 Recommendation Engine Working Logic

```
[User Activity] → [Feature Extraction] → [ML Model] → [Ranking] → [Personalized Feed]
       ↓                    ↓                ↓           ↓
[Implicit Feedback] → [Content Analysis] → [Collaborative] → [Diversity Filter]
```

**Recommendation Pipeline:**

1. **Data Collection Layer**

   - **Implicit Signals**: Play count, skip rate, dwell time, repeat plays
   - **Explicit Signals**: Likes, saves, shares, playlist additions
   - **Contextual Data**: Time of day, device type, location, mood tags
   - **Why Multiple Signals**: Single signals can be misleading; ensemble provides better accuracy

2. **Feature Engineering**

   - **User Features**: Listening history embeddings, genre preferences, activity patterns
   - **Item Features**: Audio characteristics (tempo, key, energy), genre, popularity
   - **Contextual Features**: Time-based patterns, seasonal trends, social signals
   - **Why Feature Engineering**: Raw data needs transformation for ML model consumption

3. **Model Architecture**

   - **Collaborative Filtering**: Matrix factorization using Alternating Least Squares
   - **Deep Learning**: Neural collaborative filtering with embeddings
   - **Content-Based**: Audio feature similarity using cosine distance
   - **Bandits**: Multi-armed bandit for exploration vs exploitation
   - **Why Hybrid Approach**: Different models capture different aspects of user preferences

4. **Real-time Serving**
   - Pre-computed candidate generation (batch process)
   - Real-time ranking based on current context
   - A/B testing framework for model experiments
   - **Why Two-Stage**: Balances computational efficiency with personalization quality

### 4.3 Real-time Features Implementation

**Live Activity Feed Architecture:**

```
[User Action] → [Event Publisher] → [Kafka Topic] → [Stream Processor] → [WebSocket Push]
                      ↓                    ↓                ↓                ↓
               [Event Validation] → [Fan-out Logic] → [Friend Graph] → [Active Connections]
```

**Process Flow:**

1. **Event Capture**: User plays a song, creates playlist, follows artist
2. **Event Publishing**: Action serialized and sent to Kafka topic
3. **Stream Processing**: Real-time processing filters relevant friends
4. **WebSocket Delivery**: Push notifications to online friends
5. **Offline Handling**: Store events for users who are offline

**Why This Architecture**: Decouples event generation from delivery, enables real-time social features

### 4.4 Collaborative Playlist Working Logic

**Real-time Collaboration Process:**

```
[User A Edit] → [Conflict Detection] → [Operational Transform] → [Broadcast] → [User B Update]
       ↓               ↓                        ↓                    ↓
[Version Vector] → [Priority Rules] → [State Reconciliation] → [UI Sync]
```

**Conflict Resolution Strategy:**

1. **Operational Transformation**: Transforms concurrent operations to maintain consistency
2. **Last-Writer-Wins**: For simple operations like song addition
3. **Timestamp Ordering**: Uses logical clocks for operation ordering
4. **Manual Resolution**: For complex conflicts, present options to users

**Why This Approach**: Ensures all users see consistent playlist state without blocking operations

## 3. Execution Flow

### 3.1 User Journey

1. **Authentication Flow**

   - OAuth 2.0
   - Social login integration
   - JWT tokens

2. **Music Playback**

   - Request song metadata
   - CDN selection
   - Buffer management
   - Offline mode handling

3. **Playlist Management**
   - CRUD operations
   - Collaborative playlists
   - Version control

### 3.2 Background Processes

- Audio quality optimization
- Recommendation engine updates
- Analytics processing
- Cache management

### 3.3 Real-time Features Implementation

- **Live Activity Feed**

  - Friend activity streaming
  - Real-time listening status
  - Social interactions (likes, shares)
  - WebSocket-based updates

- **Collaborative Playlists**
  - Real-time playlist editing
  - Conflict resolution for simultaneous edits
  - Version control and change tracking
  - Live collaboration indicators

### 3.4 Recommendation System Architecture

- **Collaborative Filtering**

  - User-item interaction matrix
  - Matrix factorization (SVD, NMF)
  - Neighborhood-based methods
  - Deep learning embeddings

- **Content-Based Filtering**

  - Audio feature extraction
  - Genre and mood classification
  - Artist similarity computation
  - Lyrical content analysis

- **Hybrid Approach**
  - Weighted ensemble methods
  - Multi-armed bandit optimization
  - Real-time learning and adaptation
  - Cold start problem solutions

## 4. User & Business Journeys

### 4.1 User Side

1. **Free User Journey**

   - Ad-supported listening
   - Limited features
   - Quality restrictions

2. **Premium User Journey**

   - Ad-free experience
   - High-quality audio
   - Offline downloads
   - Cross-device sync

3. **Artist Journey**
   - Music upload
   - Analytics dashboard
   - Royalty tracking
   - Fan engagement

### 4.2 Business Side

1. **Content Management**

   - License management
   - Quality control
   - Metadata management

2. **Monetization**
   - Subscription management
   - Ad delivery system
   - Revenue sharing
   - Royalty calculations

## 5. Limitations

### 5.1 Technical Limitations

- Bandwidth constraints
- Storage costs
- Cache hit ratio
- Cold start problems

### 5.2 Business Limitations

- Licensing restrictions
- Geographic limitations
- Revenue sharing constraints
- Market competition

## 6. Scope & Scalability

### 6.1 Current Scope

- Music streaming
- Podcast hosting
- Social features
- Artist promotion

### 6.2 Future Scope

- Live audio rooms
- Video content
- Virtual concerts
- NFT integration
- AI-powered features

## 7. Competitive Advantages

### 7.1 Technical Differentiators

- Superior recommendation engine
- Low latency streaming
- Efficient data compression
- Cross-platform sync

### 7.2 Business Differentiators

- Large music library
- Strong brand recognition
- Artist-friendly platform
- Social features

## 8. Additional Important Areas

### 8.1 Security Measures

- DRM implementation
- End-to-end encryption
- Rate limiting
- Fraud detection

### 8.2 Performance Optimization

- Edge computing
- Smart caching
- Predictive prefetching
- Connection optimization

### 8.3 Analytics & Monitoring

- Real-time metrics
- User behavior analysis
- System health monitoring
- Performance tracking

### 8.4 Compliance & Regulations

- GDPR compliance
- DMCA handling
- Geographic restrictions
- Age restrictions

### 8.5 Disaster Recovery

- Multi-region redundancy
- Backup strategies
- Failover mechanisms
- Data replication
