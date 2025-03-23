# 📌 Caching and Distributed Systems: A Detailed Explanation

## 🏗 1. How a Request Flows in a Web Application

Imagine you open a website like `example.com` on your browser. What happens in the background?

1. **DNS Resolution (Domain Name System)**  
   - The browser doesn’t understand domain names like `example.com`; it needs an IP address (e.g., `192.168.1.1`).  
   - The **DNS server** helps by converting `example.com` into its IP address.  
   - This is like checking a phone book to find someone's phone number using their name.

2. **Load Balancer**  
   - Think of this as a traffic cop that directs incoming requests to different servers.  
   - If one server is too busy, the load balancer sends the request to another, less busy server.  
   - This ensures the website runs smoothly even with many users.

3. **Application Server**  
   - The machine that processes the request (e.g., logging in, fetching data).  
   - This server may need to interact with a **database** to retrieve or store data.

4. **Database (Storage Layer)**  
   - Where website data is stored (e.g., user details, posts, comments).  
   - The application server fetches data from the database when needed.

5. **Caching**  
   - Instead of asking the database every time, frequently used data is stored in memory (cache).  
   - This speeds up responses and reduces the workload on the database.

---

## 🔥 2. Why Do We Need Caching?

### ✅ **Benefits of Caching:**
- **Faster Responses:** Reduces the time needed to fetch data.  
- **Less Load on Database:** Avoids querying the database for the same data repeatedly.  
- **Saves Bandwidth:** Reduces data transferred over the internet.  
- **Better User Experience:** Faster websites lead to happy users.

### 🏗 **Where Can We Store Cached Data?**
1. **Browser Caching:**  
   - The browser stores parts of a website (images, JavaScript, CSS) to load faster next time.  
   - **Example:** When you visit a website for the second time, it loads faster because images and scripts are stored in your browser.

2. **DNS Caching:**  
   - Stores recently used **IP addresses** to avoid repeating the DNS lookup.  
   - **Example:** If you visit `example.com`, the browser remembers its IP for a while.

3. **CDN Caching (Content Delivery Network):**  
   - Caches static files like images and videos on servers worldwide.  
   - Users get data from the closest server for a faster experience.

4. **Application Layer Caching:**  
   - The **backend server** keeps frequently accessed data in memory (e.g., **Redis** or **Memcached**).  
   - This avoids hitting the database every time.

5. **Database Layer Caching:**  
   - The database itself caches frequently used query results.  
   - Helps reduce **disk access time**, making queries faster.

---

## 🔄 3. Cache Eviction Policies

When the cache is full, old data needs to be removed to store new data. But **which data should be deleted?**  

### 📌 **Common Strategies:**
- **Least Recently Used (LRU):** Removes the data that hasn’t been used for the longest time.  
- **Least Frequently Used (LFU):** Removes the data that is accessed the least.  
- **First In, First Out (FIFO):** Deletes the oldest data first.  

### ⚖ **Comparison:**
| Eviction Policy | Best Used When |
|----------------|---------------|
| **LRU** | When recent data is more important. |
| **LFU** | When frequently accessed data should be kept longer. |
| **FIFO** | When old data should be cleared first. |

---

## 🌍 4. Content Delivery Networks (CDNs)

### 🌍 **Why Use a CDN?**
- **Reduces Latency:** Loads websites faster by serving content from a nearby server.  
- **Handles High Traffic:** Balances load across multiple locations.  
- **Caches Static Files:** Images, videos, CSS, JavaScript are stored closer to users.

### 🔄 **How a Client Finds the Nearest CDN Server?**
- Uses **Anycast Routing**, which directs the request to the closest available CDN node.  

### ⏳ **First Load vs. Second Load:**
1. **First Load:**
   - The request is sent to the main website server.
   - CDN caches the response.
2. **Second Load:**
   - The request is served from the **CDN cache**, making it much faster.

### 🛑 **Cache Invalidation in CDN:**
- **Time-based Expiry (TTL).**
- **Explicit Invalidation API (forcing the CDN to remove specific files).**
- **Versioning (e.g., `app.js?v=2` forces a new file download).**

---

## ⚡ 5. Database Access and Caching Strategies

### 🐢 **Why is Reading from the Database Slow?**
- Data is stored on a **hard disk** or **SSD**, which is slower than RAM.
- Query execution requires processing.

### 🏎 **Local Caching (Application Layer Cache)**
- Stores frequently accessed data in memory (RAM) on the **application server**.
- Reduces the need to query the database repeatedly.

### 🔄 **Keeping Cache and DB in Sync**
- **Global Caching:** Uses a separate high-speed cache (**Redis**) that multiple app servers share.

---

## 🔧 6. Caching Strategies

| Strategy | Description | Use Case |
|----------|------------|----------|
| **Write-Through Cache** | Data is written to cache and DB simultaneously. | Ensures consistency but slightly slower. |
| **Write-Back Cache** | Data is written to cache first, then asynchronously to DB. | Faster writes but risks data loss. |
| **Write-Around Cache** | Data is written directly to DB, cache updated only on read. | Reduces unnecessary cache writes but may cause cache misses. |

### 🎥 **Example: YouTube View Counter**
- Uses **write-back caching** to handle high loads.
- Updates cache first, then syncs periodically with the database.

---

## 🏗 7. Metadata and DSA Website Architecture

### 📌 **What is Metadata?**
- **"Data about data."**  
- **Example:** A YouTube video has metadata like title, duration, resolution.

### 🏛 **Architecture of a DSA (Competitive Programming) Website**
1. **Load Balancer**
2. **Application Servers**
3. **Global Caching (Redis)**
4. **SQL Database (Problems, Solutions, Users)**
5. **S3 Storage (Test Cases, Submissions)**

---

## 💾 8. SSD vs HDD vs Magnetic Tape

| Storage Type | Speed | Use Case |
|-------------|------|----------|
| **SSD (Solid State Drive)** | ⚡ Fast | Web applications, gaming. |
| **HDD (Hard Disk Drive)** | 🐢 Slower | Personal computers, bulk storage. |
| **Magnetic Tape** | 🐌 Slowest | Archival storage (e.g., backups). |

### 🛠 **What is S3?**
- **Amazon S3** is a **cloud storage** service for storing large files.
- Does not support traditional **relational queries** like SQL databases.

---

## 🚀 9. Optimizing Caching in DSA Contests

### ❌ **Problem: Updating Incorrect Test Cases**
1. Redis caches outdated test cases.
2. If test cases are updated, **local caches also need to be updated**.
3. **Network Load Issue:**  
   - 50 users × 200MB files = **10GB transferred!**
   - This is **expensive** and **slow**.

### ✅ **Best Solution**
- Use **Write-Through Cache** for **strong consistency**.
- Local cache with **timestamp-based invalidation** to reduce network load.
- **Hybrid caching:** Redis + Local Cache.

---

## 🔄 10. Recap: Full Flow of Caching & Performance Optimization

| Caching Type | Purpose | Location |
|--------------|---------|----------|
| **Browser Caching** | Reduces repeated downloads | User’s device |
| **CDN Caching** | Serves static content faster | Edge servers |
| **Local Caching** | Reduces DB queries | Application servers |
| **Global Caching** | Shared cache across instances | Dedicated cache servers |

🚀 **Caching makes applications faster, more efficient, and scalable!**
