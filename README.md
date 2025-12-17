# Redis Complete Documentation Guide

## Overview
Redis is an open-source, in-memory data structure store that serves as a database, cache, message broker, and streaming engine. It provides sub-millisecond latency for high-performance applications.

---

## Table of Contents
1. [Getting Started](#getting-started)
2. [Data Types](#data-types)
3. [Commands Reference](#commands-reference)
4. [Client Libraries](#client-libraries)
5. [Persistence](#persistence)
6. [Replication](#replication)
7. [Clustering](#clustering)
8. [Pub/Sub](#pubsub)
9. [Transactions](#transactions)
10. [Lua Scripting](#lua-scripting)
11. [Security](#security)
12. [Performance & Optimization](#performance--optimization)
13. [Redis for AI](#redis-for-ai)
14. [Advanced Features](#advanced-features)

---

## Getting Started

### Installation

**Linux (Ubuntu/Debian):**
```bash
sudo apt-get update
sudo apt-get install redis-server
```

**macOS:**
```bash
brew install redis
```

**From Source:**
```bash
wget http://download.redis.io/redis-stable.tar.gz
tar xvzf redis-stable.tar.gz
cd redis-stable
make
sudo make install
```

**Docker:**
```bash
docker run -d -p 6379:6379 redis
```

### Starting Redis

**Default startup:**
```bash
redis-server
```

**With configuration file:**
```bash
redis-server /etc/redis/redis.conf
```

**Check connection:**
```bash
redis-cli ping
# Response: PONG
```

---

## Data Types

### 1. Strings
The most basic Redis type - binary safe strings up to 512MB.

**Commands:**
```bash
SET key value          # Set a value
GET key                # Get a value
INCR key               # Increment by 1
DECR key               # Decrement by 1
APPEND key value       # Append to string
STRLEN key             # Get string length
SETEX key seconds val  # Set with expiration
SETNX key value        # Set if not exists
MSET k1 v1 k2 v2       # Set multiple
MGET k1 k2             # Get multiple
```

**Use Cases:** Caching, session storage, counters, rate limiting

### 2. Lists
Ordered collections of strings, sorted by insertion order.

**Commands:**
```bash
LPUSH key value        # Push to left (head)
RPUSH key value        # Push to right (tail)
LPOP key               # Pop from left
RPOP key               # Pop from right
LRANGE key start stop  # Get range
LLEN key               # Get length
LINDEX key index       # Get by index
LTRIM key start stop   # Trim to range
BLPOP key timeout      # Blocking pop (left)
BRPOP key timeout      # Blocking pop (right)
```

**Use Cases:** Message queues, activity feeds, latest items

### 3. Sets
Unordered collections of unique strings.

**Commands:**
```bash
SADD key member        # Add member(s)
SREM key member        # Remove member
SMEMBERS key           # Get all members
SISMEMBER key member   # Check membership
SCARD key              # Get set size
SINTER key1 key2       # Intersection
SUNION key1 key2       # Union
SDIFF key1 key2        # Difference
SPOP key count         # Remove random member
SRANDMEMBER key count  # Get random member
```

**Use Cases:** Tags, unique visitors, relationships

### 4. Sorted Sets (ZSets)
Sets where each member has an associated score for ordering.

**Commands:**
```bash
ZADD key score member  # Add with score
ZRANGE key start stop  # Get range by rank
ZRANGEBYSCORE key min max  # Get by score
ZRANK key member       # Get rank
ZSCORE key member      # Get score
ZINCRBY key inc member # Increment score
ZREM key member        # Remove member
ZCARD key              # Get size
ZCOUNT key min max     # Count in score range
ZREVRANGE key s e      # Reverse range
```

**Use Cases:** Leaderboards, priority queues, time-series data

### 5. Hashes
Maps between string fields and values - ideal for objects.

**Commands:**
```bash
HSET key field value   # Set field
HGET key field         # Get field
HMSET key f1 v1 f2 v2  # Set multiple
HMGET key f1 f2        # Get multiple
HGETALL key            # Get all fields
HDEL key field         # Delete field
HEXISTS key field      # Check field exists
HKEYS key              # Get all fields
HVALS key              # Get all values
HINCRBY key field inc  # Increment field
HLEN key               # Get field count
```

**Use Cases:** User profiles, configuration, objects

### 6. Bitmaps
Bit-level operations on strings.

**Commands:**
```bash
SETBIT key offset value  # Set bit
GETBIT key offset        # Get bit
BITCOUNT key             # Count set bits
BITOP operation dest k1  # Bit operations
```

**Use Cases:** Real-time analytics, feature flags

### 7. HyperLogLogs
Probabilistic data structure for counting unique items.

**Commands:**
```bash
PFADD key element      # Add element
PFCOUNT key            # Get count
PFMERGE dest src1 src2 # Merge HLLs
```

**Use Cases:** Unique visitors, cardinality estimation

### 8. Streams
Log data structure for event streaming.

**Commands:**
```bash
XADD stream * field val      # Add entry
XREAD COUNT n STREAMS s id   # Read entries
XRANGE stream start end      # Get range
XLEN stream                  # Get length
XGROUP CREATE stream group $ # Create group
XREADGROUP GROUP g c STREAMS # Consumer read
```

**Use Cases:** Event sourcing, activity logs, messaging

### 9. Geospatial
Store and query geographic coordinates.

**Commands:**
```bash
GEOADD key lon lat member    # Add location
GEODIST key m1 m2 unit       # Get distance
GEORADIUS key lon lat radius # Search radius
GEOPOS key member            # Get position
```

**Use Cases:** Location services, proximity search

### 10. JSON
Native JSON document storage (RedisJSON module).

**Commands:**
```bash
JSON.SET key $ '{"name":"Alice"}'
JSON.GET key $
JSON.ARRAPPEND key $.items value
JSON.DEL key $.field
```

**Use Cases:** Document storage, complex data structures

---

## Commands Reference

### Key Operations
```bash
DEL key                    # Delete key
EXISTS key                 # Check exists
EXPIRE key seconds         # Set expiration
TTL key                    # Get TTL
PERSIST key                # Remove expiration
RENAME key newkey          # Rename key
TYPE key                   # Get type
KEYS pattern               # Find keys (avoid in production)
SCAN cursor MATCH pattern  # Iterate keys (better)
```

### Database Operations
```bash
SELECT index               # Switch database
FLUSHDB                    # Clear current DB
FLUSHALL                   # Clear all DBs
DBSIZE                     # Get key count
INFO                       # Server info
CONFIG GET parameter       # Get config
CONFIG SET parameter value # Set config
SAVE                       # Synchronous save
BGSAVE                     # Background save
LASTSAVE                   # Last save time
SHUTDOWN                   # Shutdown server
```

---

## Client Libraries

### Go (go-redis)
```go
import "github.com/redis/go-redis/v9"

client := redis.NewClient(&redis.Options{
    Addr: "localhost:6379",
    Password: "",
    DB: 0,
})

ctx := context.Background()
err := client.Set(ctx, "key", "value", 0).Err()
val, err := client.Get(ctx, "key").Result()
```

### Python (redis-py)
```python
import redis

r = redis.Redis(host='localhost', port=6379, db=0)
r.set('key', 'value')
value = r.get('key')
```

### Node.js (node-redis)
```javascript
import { createClient } from 'redis';

const client = createClient();
await client.connect();

await client.set('key', 'value');
const value = await client.get('key');
```

### Java (Jedis)
```java
import redis.clients.jedis.Jedis;

Jedis jedis = new Jedis("localhost", 6379);
jedis.set("key", "value");
String value = jedis.get("key");
```

### C# (.NET)
```csharp
using StackExchange.Redis;

var redis = ConnectionMultiplexer.Connect("localhost");
var db = redis.GetDatabase();
db.StringSet("key", "value");
var value = db.StringGet("key");
```

---

## Persistence

### RDB (Redis Database Backup)
Point-in-time snapshots at specified intervals.

**Configuration:**
```bash
save 900 1      # Save if 1 key changed in 900 seconds
save 300 10     # Save if 10 keys changed in 300 seconds
save 60 10000   # Save if 10000 keys changed in 60 seconds

dbfilename dump.rdb
dir /var/lib/redis
```

**Manual Snapshot:**
```bash
SAVE        # Blocking save
BGSAVE      # Non-blocking save
```

**Pros:** Compact, fast restarts, good for backups
**Cons:** May lose data between snapshots

### AOF (Append Only File)
Logs every write operation.

**Configuration:**
```bash
appendonly yes
appendfilename "appendonly.aof"
appendfsync everysec   # always, everysec, no

# Rewrite settings
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb
```

**Manual Rewrite:**
```bash
BGREWRITEAOF
```

**Pros:** More durable, readable log
**Cons:** Larger files, slower restarts

### Hybrid Persistence
Use both RDB and AOF for best durability and performance.

---

## Replication

### Master-Slave Setup

**Slave Configuration:**
```bash
replicaof <master-ip> <master-port>
masterauth <master-password>
```

**Commands:**
```bash
REPLICAOF host port    # Become replica
REPLICAOF NO ONE       # Promote to master
INFO replication       # Replication status
```

**Features:**
- Asynchronous replication
- Multiple slaves per master
- Cascading replication (slave of slave)
- Read scaling with replicas
- Automatic reconnection

---

## Clustering

### Redis Cluster
Automatic sharding across multiple Redis nodes.

**Features:**
- Data automatically split among nodes (16384 hash slots)
- High availability with master-slave replication
- Automatic failover
- No single point of failure

**Creating a Cluster:**
```bash
# Start 6 Redis instances (3 masters, 3 slaves)
redis-server --port 7000 --cluster-enabled yes
redis-server --port 7001 --cluster-enabled yes
# ... (ports 7002-7005)

# Create cluster
redis-cli --cluster create \
  127.0.0.1:7000 127.0.0.1:7001 127.0.0.1:7002 \
  127.0.0.1:7003 127.0.0.1:7004 127.0.0.1:7005 \
  --cluster-replicas 1
```

**Cluster Commands:**
```bash
CLUSTER INFO               # Cluster information
CLUSTER NODES              # List nodes
CLUSTER SLOTS              # Slot distribution
CLUSTER ADDSLOTS slot      # Assign slots
CLUSTER FAILOVER           # Manual failover
```

---

## Pub/Sub

Messaging system for publish/subscribe patterns.

### Basic Pub/Sub
```bash
# Subscribe to channels
SUBSCRIBE channel1 channel2

# Publish message
PUBLISH channel1 "Hello World"

# Pattern-based subscription
PSUBSCRIBE news.*

# Unsubscribe
UNSUBSCRIBE channel1
```

### Example in Go:
```go
// Publisher
client.Publish(ctx, "mychannel", "message")

// Subscriber
pubsub := client.Subscribe(ctx, "mychannel")
msg, err := pubsub.ReceiveMessage(ctx)
```

**Use Cases:** Real-time notifications, chat systems, event broadcasting

---

## Transactions

Execute multiple commands atomically.

### MULTI/EXEC
```bash
MULTI                  # Start transaction
SET key1 value1
SET key2 value2
INCR counter
EXEC                   # Execute transaction
```

### Optimistic Locking with WATCH
```bash
WATCH key              # Watch key for changes
val = GET key
MULTI
SET key new_value
EXEC                   # Fails if key changed
```

**Commands:**
- `MULTI` - Begin transaction
- `EXEC` - Execute transaction
- `DISCARD` - Cancel transaction
- `WATCH` - Monitor keys
- `UNWATCH` - Stop monitoring

**Note:** Redis transactions are atomic but don't support rollback.

---

## Lua Scripting

Execute server-side scripts atomically.

### Basic Script
```bash
EVAL "return redis.call('SET', KEYS[1], ARGV[1])" 1 mykey myvalue
```

### Complex Script
```lua
local current = redis.call('GET', KEYS[1])
if current == false then
    return redis.call('SET', KEYS[1], ARGV[1])
else
    return current
end
```

### Script Management
```bash
SCRIPT LOAD script         # Load and cache script
EVALSHA sha1 numkeys k... # Execute cached script
SCRIPT EXISTS sha1         # Check if script exists
SCRIPT FLUSH               # Remove all scripts
SCRIPT KILL                # Kill running script
```

**Benefits:**
- Atomic execution
- Reduced network round trips
- Complex operations
- Consistent performance

---

## Security

### Authentication
```bash
# redis.conf
requirepass your_password

# CLI connection
redis-cli -a your_password

# AUTH command
AUTH password
```

### ACL (Access Control Lists)
Fine-grained permission control (Redis 6+).

```bash
# Create user
ACL SETUSER alice on >password ~cached:* +get +set

# List users
ACL LIST

# Get current user
ACL WHOAMI

# Remove user
ACL DELUSER alice
```

**ACL Rules:**
- `on/off` - Enable/disable user
- `>password` - Set password
- `~pattern` - Key patterns allowed
- `+command` - Allow command
- `-command` - Deny command
- `@category` - Command categories

### TLS/SSL
```bash
# redis.conf
port 0
tls-port 6379
tls-cert-file /path/to/redis.crt
tls-key-file /path/to/redis.key
tls-ca-cert-file /path/to/ca.crt
```

### Protected Mode
```bash
protected-mode yes
bind 127.0.0.1
```

---

## Performance & Optimization

### Memory Optimization

**Eviction Policies:**
```bash
maxmemory 256mb
maxmemory-policy allkeys-lru
```

**Policies:**
- `noeviction` - Return errors when memory limit reached
- `allkeys-lru` - Evict least recently used keys
- `volatile-lru` - Evict LRU keys with expire set
- `allkeys-lfu` - Evict least frequently used
- `volatile-ttl` - Evict by TTL
- `allkeys-random` - Random eviction
- `volatile-random` - Random with expire

### Performance Tips

1. **Use Pipelining** - Batch commands
```go
pipe := client.Pipeline()
pipe.Set(ctx, "key1", "value1", 0)
pipe.Set(ctx, "key2", "value2", 0)
pipe.Exec(ctx)
```

2. **Use Connection Pooling** - Reuse connections

3. **Avoid KEYS command** - Use SCAN instead
```bash
SCAN 0 MATCH pattern* COUNT 100
```

4. **Use appropriate data types** - Choose efficient structures

5. **Set expiration** - Auto-cleanup with TTL
```bash
SETEX key 3600 value
```

6. **Monitor slow queries**
```bash
SLOWLOG GET 10
CONFIG SET slowlog-log-slower-than 10000
```

7. **Use Redis Cluster** - Horizontal scaling

### Monitoring
```bash
INFO               # Server statistics
INFO stats         # Stats section
INFO memory        # Memory info
MONITOR            # Real-time commands
CLIENT LIST        # Connected clients
LATENCY DOCTOR     # Latency diagnostics
```

---

## Redis for AI

### Vector Search
Store and search vector embeddings for RAG applications.

**Features:**
- Similarity search (cosine, euclidean, IP)
- Hybrid queries (vector + filters)
- Range and KNN queries

### Semantic Caching
Cache LLM responses based on semantic similarity.

**Benefits:**
- Reduced LLM costs
- Lower latency
- Consistent responses

### Use Cases
- Retrieval Augmented Generation (RAG)
- Recommendation systems
- Image/text similarity search
- Feature stores for ML
- Real-time model serving

---

## Advanced Features

### Redis Modules
Extend Redis with additional functionality:

- **RedisJSON** - Native JSON support
- **RediSearch** - Full-text search and secondary indexing
- **RedisGraph** - Graph database
- **RedisTimeSeries** - Time-series data
- **RedisBloom** - Probabilistic data structures
- **RedisAI** - ML model serving

### Redis Streams (Advanced)
```bash
# Consumer groups
XGROUP CREATE mystream mygroup $
XREADGROUP GROUP mygroup consumer1 COUNT 2 STREAMS mystream >

# Acknowledge messages
XACK mystream mygroup message-id

# Pending messages
XPENDING mystream mygroup
```

### Transactions with Lua
```lua
-- Atomic counter with limit
if redis.call('GET', KEYS[1]) < tonumber(ARGV[1]) then
    return redis.call('INCR', KEYS[1])
else
    return 0
end
```

---

## Best Practices

1. **Design Keys Properly**
   - Use namespaces: `user:1000:profile`
   - Keep keys short but meaningful
   - Consistent naming conventions

2. **Choose Right Data Structures**
   - Strings for simple values
   - Hashes for objects
   - Sets for unique collections
   - Sorted Sets for rankings

3. **Set Appropriate Expiration**
   - Always set TTL for cached data
   - Use `EXPIRE` or `SETEX`

4. **Monitor and Tune**
   - Use `INFO` command regularly
   - Check memory usage
   - Monitor slow queries

5. **Security**
   - Enable authentication
   - Use ACLs for fine-grained control
   - Enable TLS in production
   - Bind to specific interfaces

6. **High Availability**
   - Use replication for redundancy
   - Enable persistence (RDB + AOF)
   - Consider Redis Cluster for scaling

7. **Connection Management**
   - Use connection pooling
   - Close connections properly
   - Handle connection errors

---

## Resources

### Official Documentation
- **Main Docs:** https://redis.io/docs/latest/
- **Commands Reference:** https://redis.io/commands/
- **Client Libraries:** https://redis.io/clients/

### Learning
- **Redis University:** https://university.redis.io/
- **Interactive Tutorial:** https://try.redis.io/
- **Community:** https://redis.io/community/

### Tools
- **Redis Insight:** GUI for Redis
- **Redis CLI:** Command-line interface
- **redis-benchmark:** Performance testing

---

## Quick Reference Card

```bash
# Connection
redis-cli -h host -p port -a password

# Basic Operations
SET key value EX 60        # Set with 60s expiration
GET key
DEL key
EXISTS key
EXPIRE key seconds

# Lists
LPUSH list value
RPUSH list value
LRANGE list 0 -1          # Get all

# Sets
SADD set member
SMEMBERS set

# Sorted Sets
ZADD zset score member
ZRANGE zset 0 -1 WITHSCORES

# Hashes
HSET hash field value
HGETALL hash

# Pub/Sub
SUBSCRIBE channel
PUBLISH channel message

# Transactions
MULTI
# commands...
EXEC

# Info & Monitoring
INFO
MONITOR
SLOWLOG GET 10
