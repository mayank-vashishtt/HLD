# Microservices and Network Protocols: A Deep Dive

## Microservices Architecture

### What is Microservices?
Microservices is a way to build systems where all functionalities are broken down into multiple independent applications that communicate via APIs.

- Originally formalized ~11 years ago, but similar concepts existed before.
- Companies usually start with a **monolithic architecture** and transition to microservices when a **friction point** is reached.
- First service to be extracted is often **authentication service**.

### Advantages of Microservices
- Overcomes the limitations of monolithic systems:
  - No **memory leaks** impacting the entire system.
  - **Better issue locality**, debugging becomes easier.
  - **Easier onboarding** of developers.
  - **Selective scaling** is possible.
  - No dependency on **old tech choices**.
  - **Faster deployments**.
  - Reduces **inter-team conflicts**.
- Each microservice acts as a **separate company**, communicating **only via APIs**.

### Problems with Microservices
- **Higher latency** due to API calls over the network.
  - In a monolith, function calls are much faster than network calls.
  - [Latency numbers every developer should know](https://gist.github.com/jboner/2841832)
- **Infrastructure overhead**.
- **Integration testing is complex**.
  - Unit tests don't work well; **edge cases are often missed**.

### Flipkart's Shift from Monolithic to Microservices
- **Problems with Monolith:**
  - **Memory leaks** crash the whole system.
  - **Difficult issue localization**.
  - **Onboarding challenges** for new devs.
  - **No selective scaling**.
  - **Tied to past tech choices**.
  - **Slow deployment times**.
  - **Inter-team conflicts**.

### Amazon's "Two-Pizza Rule"
- Teams should be small enough that they can be fed with **two pizzas**.
- Encourages **independent teams with microservices**.

## Communication in Microservices

### API Calls
- **Synchronous communication:** Service A calls Service B **immediately**.
- Example:
  - **Order Service** calls **Notification Service** via an HTTP API.

### Event-Driven Communication
- Used when responses don’t need to be immediate.
- Example:
  - **Order Service** places an order.
  - **Notification Service** asynchronously sends a confirmation email.

## Networking and Protocols

### What are Protocols?
- Set of rules that define **communication between devices**.
- Examples: **HTTP, TCP, UDP, gRPC**.

### OSI Model (Deep Dive)
1. **Physical Layer** - Cables, signals, hardware.
2. **Data Link Layer** - MAC addresses, Ethernet.
3. **Network Layer** - IP addresses, routing.
4. **Transport Layer** - TCP/UDP.
5. **Session Layer** - Manages sessions.
6. **Presentation Layer** - Data formatting.
7. **Application Layer** - Protocols like HTTP, FTP, SMTP.

### TCP/IP Model (Deep Dive)
- **Network Interface Layer**: Hardware-level communication.
- **Internet Layer**: IP addressing, routing.
- **Transport Layer**: TCP/UDP.
- **Application Layer**: HTTP, FTP, SMTP.

### HTTP Protocol
- **Application Layer Protocol**.
- **Stateless** (No built-in session management).
- Uses **client-server architecture**.

#### HTTP Request Components
1. **Request Method**: GET, POST, PUT, DELETE, HEAD.
2. **Request Headers**: Authorization, Content-Type.
3. **Request Body** (optional, not used in GET requests).

#### HTTP Response Components
1. **Status Codes:**
   - **1xx**: Informational
   - **2xx**: Success
   - **4xx**: Client errors
   - **5xx**: Server errors
2. **Response Headers**: Set-Cookie, Content-Type.
3. **Response Body**: JSON, XML, Binary, Text.

### HTTPS vs. HTTP
- **HTTPS** encrypts data using **SSL/TLS**.
- Prevents **MITM attacks** and eavesdropping.

## TCP vs. UDP
| Feature  | TCP  | UDP  |
|----------|------|------|
| Connection-Oriented | Yes | No |
| Reliable | Yes | No |
| Ordered Delivery | Yes | No |
| Speed | Slower | Faster |
| Use Case | Web browsing, emails | Video streaming, gaming |

### TCP Headers and Their Roles
- **Sequence Number**: Ensures order.
- **Acknowledgment Number**: Confirms receipt.
- **Flags**: SYN, ACK, FIN (controls connection states).
- **Checksum**: Detects errors.

## Caching and Content Delivery Networks (CDNs)

### Where to Cache Requests in CDN?
- **Cache GET requests in the URL**.
- Search queries are cached differently (query string parameters).
- **Live streaming services (Jio, Hotstar)** use CDNs to cache video chunks closer to users.

## JSON Performance Issues & Alternatives

### Problems with JSON
- **Verbose format** (large size, slow parsing).
- **High CPU usage**.
- **Lack of type safety**.

### Cost-Saving Alternatives
- **Protocol Buffers (ProtoBuf)**: Used in **gRPC**, faster and smaller.
- **MessagePack**: Efficient binary format.
- **Avro**: Optimized for big data.

## Summary
- **Microservices** solve monolithic problems but introduce network overhead.
- **HTTP** is a stateless request-response protocol.
- **gRPC** is an alternative that uses ProtoBuf for efficiency.
- **CDNs** cache data to optimize performance.
- **TCP vs UDP**: TCP for reliability, UDP for speed.
- **JSON is slow**; alternatives like ProtoBuf exist.

## Additional Reading
- [Microservices at Netflix](https://netflixtechblog.com/)
- [Google’s Protocol Buffers](https://developers.google.com/protocol-buffers)
- [REST vs. gRPC](https://grpc.io/docs/what-is-grpc/introduction/)
