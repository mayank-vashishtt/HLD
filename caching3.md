# **High-Level Design (HLD) of Caching**

## **1. What is Caching?**
Caching is the process of storing copies of frequently accessed data in a fast-access storage layer to reduce latency and improve system performance. A cache acts as a high-speed storage layer that sits between the primary storage (database, disk) and the application.

### **Types of Cache Storage**
- **In-Memory Cache**: Uses RAM for fast data retrieval (e.g., Redis, Memcached).
- **Disk-Based Cache**: Uses SSDs or HDDs for larger storage but slower retrieval.
- **Distributed Cache**: Multiple cache instances distributed across nodes for scalability (e.g., Redis Cluster).

## **2. Cron Jobs and Daemons**

### **Cron Jobs**
A cron job is a scheduled task in Unix-like operating systems, managed by the `cron` daemon and defined in the **crontab** file.

Example crontab entry:
```bash
* * * * * /path/to/script.sh
```
This runs the script every minute.

### **Daemons**
A daemon is a background process that runs continuously, unlike a cron job that runs at scheduled times.

Example:
- `redis-server` is a daemon that runs Redis in the background.
- `nginx` runs as a daemon to serve web requests.

## **3. How TTL Works in Redis**
TTL (Time-To-Live) in Redis is used to set an expiration time on keys.

Example:
```bash
SET user:123 "John Doe" EX 60  # Expires in 60 seconds
```
Redis has two ways of removing expired keys:
1. **Lazy Expiration**: When a key is accessed, Redis checks if it’s expired and removes it.
2. **Active Expiration**: A background process randomly checks keys and removes expired ones.

## **4. Race Conditions in Redis**
A race condition occurs when multiple processes try to modify the same data at the same time, leading to unexpected behavior.

### **Example Problem:**
- Two users withdraw money from the same account at the same time.
- Both read the same balance and update it simultaneously, causing an incorrect final balance.

### **Solutions:**
1. **Using Transactions (MULTI/EXEC)**
   ```bash
   MULTI
   DECR account:balance 100
   EXEC
   ```
2. **Using Redis Locks**
   ```bash
   SETNX lock:account:123 1
   EXPIRE lock:account:123 5  # Set a timeout
   ```

## **5. Storing and Displaying Live Rankings (LeetCode-style)**
### **Schema Design**
```sql
CREATE TABLE leaderboard (
    user_id BIGINT PRIMARY KEY,
    score INT,
    current_rank INT
);
```
### **Using Redis Sorted Sets for Ranking**
```bash
ZADD leaderboard 100 "user:1"
ZADD leaderboard 200 "user:2"
ZADD leaderboard 150 "user:3"
```
- **Get a user’s rank**:
  ```bash
  ZREVRANK leaderboard "user:1"
  ```
- **Get the top 10 users**:
  ```bash
  ZREVRANGE leaderboard 0 9 WITHSCORES
  ```

## **6. Ways to Invalidate Local Cache**
Cache invalidation is crucial to ensure data consistency. Methods include:

### **a) Time-to-Live (TTL)**
- Each cache entry has an expiration time.
- Example in Redis:
  ```bash
  SET user:123 "John Doe" EX 60  # Expires in 60 seconds
  ```

### **b) Least Recently Used (LRU) Eviction**
- Removes the least accessed cache entries when space is needed.
- Example in Redis:
  ```bash
  CONFIG SET maxmemory-policy allkeys-lru
  ```

### **c) Manual Invalidation**
- Delete cache entries when data is updated.
- Example:
  ```bash
  DEL user:123
  ```

### **d) Event-Driven Invalidation (Pub/Sub Model)**
- When data changes in the primary database, an event is published to a message broker (e.g., Kafka, Redis Pub/Sub), notifying all cache instances to update or delete outdated entries.

## **7. Case Study: Instagram News Feed Caching**
### **Requirements:**
- Users should see posts only from people they follow.
- The system should handle millions of posts efficiently.
- Images are stored in a CDN.

### **Schema Design**
```sql
CREATE TABLE posts (
    post_id BIGINT PRIMARY KEY,
    user_id BIGINT,
    content TEXT,
    image_url TEXT,
    created_at TIMESTAMP
);

CREATE TABLE followers (
    follower_id BIGINT,
    followee_id BIGINT,
    PRIMARY KEY (follower_id, followee_id)
);
```

### **Sharding Strategy**
- **User-Based Sharding:** Each user's posts are stored in a specific shard.
- **Range-Based Sharding:** Posts are stored based on post_id ranges.

### **News Feed Generation Approaches**
#### **a) Pull-Based Approach**
- When a user requests their feed, fetch posts from followed users.
- Pros: Real-time updates.
- Cons: High latency for large follow lists.

#### **b) Push-Based Approach**
- When a user posts, push the post to all followers’ caches.
- Pros: Fast retrieval.
- Cons: High memory usage.

#### **c) Hybrid Approach**
- Frequently active users get push-based updates.
- Less active users get pull-based updates.

### **Alternative Approach: Event-Driven Model**
- Each time a post is created, an event is sent to a queue (Kafka, RabbitMQ).
- Workers process the event and update follower feeds asynchronously.
- This ensures fast feed updates while avoiding database bottlenecks.

### **How Instagram Shows the Feed Quickly**
1. Use **Redis** as a cache to store precomputed feeds.
2. Store only recent posts in the cache, older ones are fetched from the database.
3. Use a **Message Queue (Kafka, RabbitMQ)** to process post distribution efficiently.

## **8. Cache Definition & Storage Types**
- A cache is any type of storage that maintains a **copy** of frequently accessed data.
- It is not limited to RAM and can be stored on disks, SSDs, or distributed systems.

## **Conclusion**
- Caching improves performance and scalability.
- Strategies like TTL, LRU, and Event-Driven Invalidation help maintain cache consistency.
- Instagram and ranking systems use caching to ensure fast and scalable performance.
