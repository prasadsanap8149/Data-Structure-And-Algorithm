# WhatsApp System Design

## 1. System Architecture Overview

### 1.1 High-Level Architecture

```
[Mobile Clients] → [Load Balancer] → [API Gateway] → [Connection Manager]
                                                            ↓
[Authentication Service] ← [Message Router] ← [Presence Service] ← [Group Manager]
                                ↓                    ↓                    ↓
                        [Message Storage]    [Media Storage]    [User Database]
                                ↓                    ↓                    ↓
                            [Encryption Service] ← [Notification Service] → [Analytics]
```

**Why This Architecture?**

- **Connection Manager**: Handles millions of concurrent WebSocket connections efficiently
- **Message Router**: Ensures messages reach intended recipients across global infrastructure
- **Stateless Services**: Enables horizontal scaling and fault tolerance
- **Separation of Concerns**: Each service has a specific responsibility for better maintainability

### 1.2 Working Logic Flow

1. **Connection Establishment** → **Message Exchange** → **End-to-End Encryption** → **Delivery Confirmation**
2. **Background Processes**: Media processing, message indexing, analytics aggregation, backup operations

### 1.3 Core Components Detailed

- **Messaging Infrastructure**

  - **Purpose**: Handle real-time bidirectional communication
  - **Component Details**:
    - WebSocket connection pools (10M+ concurrent connections)
    - Message queuing system for offline users
    - Delivery guarantee mechanisms (at-least-once delivery)
    - Message ordering preservation per conversation
  - **Why Essential**: Real-time messaging requires persistent connections and guaranteed delivery

- **Connection Manager Service**

  - **Purpose**: Manage client connections and routing
  - **Responsibilities**:
    - WebSocket connection lifecycle management
    - Load balancing across connection servers
    - Connection migration during server maintenance
    - Heartbeat and presence tracking
  - **Why Needed**: Centralizes connection management and enables efficient resource utilization

- **End-to-End Encryption Engine**

  - **Purpose**: Ensure message privacy and security
  - **Implementation**: Signal Protocol with Double Ratchet
  - **Key Features**:
    - Perfect Forward Secrecy (PFS)
    - Post-compromise security
    - Metadata protection
    - Key rotation and management
  - **Why Critical**: Privacy is core to WhatsApp's value proposition

- **Data Storage Architecture**
  - **Cassandra (Message Storage)**
    - **Tables**: messages, message_threads, group_messages, media_metadata
    - **Why Cassandra**: Write-heavy workload, global distribution, linear scalability
  - **Redis (Real-time Data)**
    - **Data**: User sessions, online presence, typing indicators, message queues
    - **Why Redis**: Sub-millisecond latency, pub/sub capabilities, atomic operations
  - **PostgreSQL (User Management)**
    - **Tables**: users, contacts, groups, group_members, blocked_users
    - **Why PostgreSQL**: ACID compliance for user data, complex queries for contact management
  - **S3/Blob Storage (Media)**
    - **Content**: Images, videos, audio messages, documents
    - **Why Object Storage**: Cost-effective, automatic scaling, global CDN integration
  - **HBase (Metadata & Analytics)**
    - **Data**: Message delivery status, read receipts, analytics events
    - **Why HBase**: Time-series data, real-time analytics, integration with Hadoop ecosystem

### 1.4 Technical Stack & Justification

- **Backend**: Java (Spring Boot), Go

  - **Microservices Architecture**:
    - Message Service, User Service, Group Service, Media Service, Notification Service
    - Each service handles specific functionality and scales independently
    - **Why Java & Go**: Java for complex business logic, Go for high-performance connection handling
  - **WebSocket Servers**:
    - Persistent connections for real-time messaging
    - Connection pooling and load balancing
    - **Why WebSocket**: Bidirectional, low latency, efficient for real-time communication
  - **XMPP Protocol**:
    - Standard messaging protocol for presence and messaging
    - Extensions for group chat and media sharing
    - **Why XMPP**: Proven protocol, extensible, supports real-time features
  - **gRPC Internal Communication**:
    - High-performance RPC for service-to-service communication
    - Protocol buffer serialization for efficiency
    - **Why gRPC**: Type-safe, high performance, supports streaming

- **Frontend**: React Native
  - **Cross-platform Development**:
    - Single codebase for iOS and Android
    - Native module integration for platform-specific features
    - **Why React Native**: Faster development, code reuse, near-native performance
  - **WebSocket Client**:
    - Maintains persistent connection to server
    - Automatic reconnection and offline handling
  - **End-to-End Encryption**:
    - Client-side encryption/decryption
    - Key management and rotation
    - **Why Client-side**: Server never sees unencrypted content

## 2. Access Control & Security (RBAC)

### 2.1 Role-Based Access Control Implementation

```
User Access Levels:
├── Regular User (Standard Messaging)
├── Group Admin (Group Management)
├── Business Account (Business Features)
├── Content Moderator (Report Review)
├── Support Agent (Limited User Support)
├── System Admin (Infrastructure Access)
└── Security Admin (Encryption & Compliance)
```

### 2.2 Permission Matrix

| Role              | Send Message | Create Group | Admin Group     | Business API | Moderation | System Access |
| ----------------- | ------------ | ------------ | --------------- | ------------ | ---------- | ------------- |
| Regular User      | ✅           | ✅           | Own Groups      | ❌           | ❌         | ❌            |
| Group Admin       | ✅           | ✅           | Assigned Groups | ❌           | ❌         | ❌            |
| Business          | ✅           | ✅           | Own Groups      | ✅           | ❌         | ❌            |
| Content Moderator | ✅           | ✅           | ❌              | ❌           | ✅         | ❌            |
| Support Agent     | Limited      | ❌           | ❌              | ❌           | Limited    | ❌            |
| System Admin      | ✅           | ✅           | ✅              | ✅           | ✅         | Limited       |
| Security Admin    | ✅           | ✅           | ✅              | ✅           | ✅         | Full          |

### 2.3 Authentication & Authorization Flow

1. **Phone Number Authentication**

   - **SMS-based OTP Verification**:

     - Primary phone number serves as unique identifier
     - 6-digit OTP with 5-minute expiration
     - Rate limiting: 3 attempts per hour per number
     - **Why Phone-based**: Global accessibility, reduces fake accounts, unique identity

   - **Registration Security**:
     - Device fingerprinting for suspicious activity detection
     - IP-based rate limiting for registration attempts
     - Captcha integration for automated bot prevention
     - **Why These Measures**: Prevents spam and abuse at account creation level

2. **Session Management**

   - **JWT Token Implementation**:
     - Short-lived access tokens (15 minutes)
     - Refresh tokens with device binding
     - Automatic token rotation on each use
     - **Why Short-lived**: Minimizes impact of token compromise

3. **Message-Level Security**
   - **End-to-End Encryption Validation**:
     - Each message requires valid encryption keys
     - Key verification through safety numbers
     - Forward secrecy through key rotation
     - **Why E2E**: Ensures privacy even from WhatsApp servers

### 2.4 Privacy & Data Protection

- **Data Minimization**:

  - Messages stored temporarily for delivery only
  - Metadata collection limited to operational needs
  - User content not accessible to WhatsApp
  - **Implementation**: Zero-knowledge architecture where possible

- **User Consent Management**:
  - Granular privacy settings
  - Opt-in for data sharing features
  - Right to data deletion (GDPR compliance)
  - **Implementation**: Privacy settings API with user-controlled permissions

## 3. Database Design & Table-Level Explanation

### 3.1 PostgreSQL (User Management Database)

**Why PostgreSQL?**

- ACID compliance for critical user data
- Complex queries for contact relationships
- JSON support for flexible user preferences
- Strong consistency for account operations

#### Core Tables:

**Users Table**

```sql
CREATE TABLE users (
    user_id BIGSERIAL PRIMARY KEY,
    phone_number VARCHAR(15) UNIQUE NOT NULL,
    country_code VARCHAR(3) NOT NULL,
    username VARCHAR(50),
    display_name VARCHAR(100),
    profile_picture_url VARCHAR(500),
    status_message VARCHAR(200),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    last_seen TIMESTAMP,
    is_active BOOLEAN DEFAULT true,
    privacy_settings JSONB DEFAULT '{}',
    business_account BOOLEAN DEFAULT false,
    INDEX idx_phone_number (phone_number),
    INDEX idx_country_code (country_code),
    INDEX idx_last_seen (last_seen)
);
```

**Purpose**: Central user identity and profile management
**Why These Fields**: Phone number as unique ID, privacy settings for user control

**Contacts Table**

```sql
CREATE TABLE user_contacts (
    user_id BIGINT REFERENCES users(user_id),
    contact_user_id BIGINT REFERENCES users(user_id),
    contact_name VARCHAR(100),
    added_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    is_blocked BOOLEAN DEFAULT false,
    PRIMARY KEY (user_id, contact_user_id),
    INDEX idx_user_contacts (user_id),
    INDEX idx_blocked (user_id, is_blocked)
);
```

**Purpose**: Manage user contact relationships and blocking
**Why This Design**: Bidirectional relationships, local contact naming

**Groups Table**

```sql
CREATE TABLE groups (
    group_id BIGSERIAL PRIMARY KEY,
    group_name VARCHAR(100) NOT NULL,
    description TEXT,
    created_by BIGINT REFERENCES users(user_id),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    group_picture_url VARCHAR(500),
    max_members INTEGER DEFAULT 256,
    is_active BOOLEAN DEFAULT true,
    group_settings JSONB DEFAULT '{}',
    INDEX idx_created_by (created_by),
    INDEX idx_created_at (created_at)
);
```

**Purpose**: Group metadata and configuration
**Why These Fields**: Creator tracking, member limits, extensible settings

### 3.2 Cassandra (Message Storage)

**Why Cassandra?**

- Extremely high write throughput for messages
- Linear scalability across global data centers
- Tunable consistency for different use cases
- Time-series data optimization

#### Core Column Families:

**Messages Column Family**

```cql
CREATE TABLE messages (
    conversation_id TEXT,
    message_timestamp TIMEUUID,
    message_id TEXT,
    sender_id TEXT,
    message_type TEXT, -- text, image, video, audio, document
    content BLOB, -- encrypted message content
    metadata MAP<TEXT, TEXT>, -- file size, duration, etc.
    delivery_status TEXT,
    created_at TIMESTAMP,
    PRIMARY KEY (conversation_id, message_timestamp)
) WITH CLUSTERING ORDER BY (message_timestamp DESC);
```

**Purpose**: Store all messages with guaranteed ordering
**Why This Design**: Partitioned by conversation for efficient queries, TIMEUUID for ordering

**Group_Messages Column Family**

```cql
CREATE TABLE group_messages (
    group_id TEXT,
    message_timestamp TIMEUUID,
    message_id TEXT,
    sender_id TEXT,
    message_type TEXT,
    content BLOB,
    metadata MAP<TEXT, TEXT>,
    delivery_receipts MAP<TEXT, TIMESTAMP>, -- user_id -> delivered_at
    read_receipts MAP<TEXT, TIMESTAMP>, -- user_id -> read_at
    PRIMARY KEY (group_id, message_timestamp)
) WITH CLUSTERING ORDER BY (message_timestamp DESC);
```

**Purpose**: Group message storage with delivery tracking
**Why Separate Table**: Different access patterns for group vs individual messages

### 3.3 Redis (Real-time & Caching)

**Why Redis?**

- Sub-millisecond response times for real-time features
- Atomic operations for presence and typing indicators
- Pub/Sub for real-time notifications
- Built-in data structures for complex operations

#### Data Structures:

**User Sessions**

```redis
Key Pattern: session:{user_id}
Data Type: Hash
Fields: {
    "device_id": "unique_device_identifier",
    "connection_id": "websocket_connection_id",
    "last_activity": "timestamp",
    "status": "online|away|offline",
    "platform": "android|ios|web"
}
TTL: 1 hour (refreshed by heartbeat)
```

**Purpose**: Track active user sessions and presence
**Why Hash**: Efficient partial updates and atomic operations

**Message Queues for Offline Users**

```redis
Key Pattern: offline_queue:{user_id}
Data Type: List
Values: Serialized message objects
Max Size: 1000 messages per user
TTL: 7 days
```

**Purpose**: Store messages for offline users
**Why List**: FIFO ordering, efficient push/pop operations

**Typing Indicators**

```redis
Key Pattern: typing:{conversation_id}
Data Type: Set
Members: user_id list
TTL: 10 seconds (auto-expire)
```

**Purpose**: Track who is currently typing in each conversation
**Why Set with TTL**: Automatic cleanup, fast membership operations
