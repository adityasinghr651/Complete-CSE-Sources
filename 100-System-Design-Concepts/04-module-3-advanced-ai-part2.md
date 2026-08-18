# MODULE 3 — CONCEPTS 76–100 (PART 2: 89-100)

## #89. GraphQL vs REST [Type D — Trade-off Scenario]

### What is it?
Two different paradigms for building APIs.
- **REST:** Multiple endpoints (`/users/1`, `/users/1/posts`). The server dictates exactly what data shape is returned.
- **GraphQL:** A single endpoint (`/graphql`). The client sends a specific query asking *exactly* for what it wants, and the server returns exactly that.

### Mental Model
REST = Ordering off a set menu at a restaurant. You want the burger, you also get the fries and the drink, even if you just wanted the burger (Over-fetching). If you also want a salad, you have to place a second order (Under-fetching).
GraphQL = A buffet. You walk in with a plate and take exactly what you want, no more, no less, in one single trip.

### Why does it exist?
In REST, if a mobile app only needs a user's `name` to render the header, it calls `/users/1` and downloads a massive JSON object with 50 fields (email, address, bio), wasting mobile data and battery (Over-fetching). GraphQL solves this.

### Architecture / Raw Diagram
```text
REST:
GET /users/1 -> { id: 1, name: "A", email: "a@a.com", address: "..." }

GRAPHQL:
POST /graphql
Query: { user(id: 1) { name } }  ->  Response: { data: { user: { name: "A" } } }
```

### When Would I Use It?
- Use GraphQL when building APIs consumed by multiple different frontend clients (Mobile, Web, Smartwatch) that all have drastically different data needs.

### Trade-offs
- **What do I gain?** Incredible frontend flexibility. Eliminates over-fetching and under-fetching.
- **What do I sacrifice?** Backend complexity. Caching GraphQL is notoriously difficult because everything goes through a single POST endpoint. It also suffers heavily from the N+1 Query problem if the backend resolvers aren't optimized using `DataLoader`.

### Interview Question
"In what scenario would a mobile team strongly advocate for migrating an API from REST to GraphQL?"

### How to Answer
**The 'Think' Process:** Focus on Over-fetching, Under-fetching, and mobile bandwidth.
**The Answer:** "A mobile team would advocate for GraphQL to solve 'Over-fetching' and 'Under-fetching' caused by rigid REST endpoints. Mobile devices have strict constraints on network bandwidth and battery life. If the mobile app needs to render a dashboard showing a user's name and their top 3 recent posts, a REST API might force the app to make two separate requests, downloading massive JSON payloads full of irrelevant data. With GraphQL, the mobile client can send a single query requesting exactly those two specific fields, minimizing the payload size and the number of network hops."

---

## #90. gRPC [Type A — Concept]

### What is it?
A modern, open-source Remote Procedure Call (RPC) framework developed by Google. It uses HTTP/2 and Protocol Buffers (Protobufs) to allow microservices to communicate with each other exponentially faster than standard REST JSON APIs.

### Mental Model
REST JSON = Sending a long, handwritten English letter in a large envelope. (Human readable, but bulky and slow to read).
gRPC Protobuf = Sending a highly compressed binary Morse code message. (Machines read it instantly, humans cannot read it).

### Why does it exist?
When Microservice A calls Microservice B 10,000 times a second using REST, generating, sending, and parsing the JSON strings consumes massive amounts of CPU and network bandwidth. gRPC sends the data as raw binary, skipping the JSON overhead entirely.

### Real-World Example
**Internal Netflix Microservices:** The public internet talks to the Netflix API Gateway using standard REST JSON. But internally, the Gateway talks to the Billing and Recommendation microservices using gRPC for maximum speed.

### Architecture / Raw Diagram
```text
[ Service A (Node) ] ─(Binary Protobuf)─> [ Service B (Go) ]
```

### Data Flow
1. Developer defines a `.proto` schema file (e.g., `string name = 1;`).
2. The gRPC compiler automatically generates the client and server code for both Node.js and Go.
3. Node.js calls the function `getUser(1)` exactly as if it were a local function.
4. The gRPC library serializes it into binary, sends it over HTTP/2, and the Go server deserializes and executes it instantly.

### When Would I Use It?
- Internal Server-to-Server (microservice) communication.

### When Would I NOT Use It?
- Public-facing APIs (Web browsers do not natively support gRPC without heavy proxying tools like gRPC-Web).

### Trade-offs
- **What do I gain?** 10x-100x faster serialization than JSON, strict type safety via `.proto` schemas, and built-in HTTP/2 streaming.
- **What do I sacrifice?** Human readability. You cannot use Postman or `curl` to easily test a gRPC endpoint because the payload is binary gibberish.

### Interview Question
"Your company's internal microservices communicate via REST JSON, but CPU utilization is very high due to parsing JSON millions of times a second. How do you optimize this?"

### How to Answer
**The 'Think' Process:** Propose migrating internal comms to gRPC and Protobufs.
**The Answer:** "I would migrate the internal inter-service communication from REST JSON to gRPC. JSON is a text-based format that is human-readable, making it slow for machines to serialize and deserialize. gRPC uses Protocol Buffers, which transmits data in a highly compressed binary format. By defining strict `.proto` schemas, we eliminate the parsing overhead, drastically reducing CPU utilization and network bandwidth while simultaneously gaining strict type safety between services."

---

## #91. CAP Theorem [Type A — Concept]

### What is it?
A fundamental theorem stating that in any distributed data store, you can only guarantee **TWO** out of the following three properties simultaneously:
- **Consistency:** Every read receives the most recent write.
- **Availability:** Every request receives a non-error response (even if the data is stale).
- **Partition Tolerance:** The system continues to operate despite a network failure dropping messages between nodes.

### Mental Model
Imagine two librarians (Node A and Node B). They share the job of keeping a ledger. The phone line between them gets cut (Network Partition). 
If a customer asks Librarian A for a balance, Librarian A has a choice:
1. Reply with the balance they have, knowing Librarian B might have updated it (Chooses Availability over Consistency).
2. Refuse to answer until the phone line is fixed (Chooses Consistency over Availability).

### Why does it exist?
To force engineers to recognize the fundamental physics of networks. Networks *will* fail (Partition). Therefore, you must architect your database to sacrifice either Consistency or Availability when that happens.

### Architecture / Raw Diagram
```text
           [ Network Partition (Connection Broken) ]
[ Database Node A ] <====== X ======> [ Database Node B ]
```

### When Would I Use It?
- Used purely for theoretical discussion and choosing database technologies.

### Trade-offs
- **CP (Consistency + Partition Tolerance):** E.g., MongoDB, Redis, HBase. If the network splits, they lock down and refuse writes to prevent data corruption. (Favored by Banks).
- **AP (Availability + Partition Tolerance):** E.g., Cassandra, DynamoDB. If the network splits, they continue accepting writes on both sides. When the network heals, they resolve the conflicts (Eventual Consistency). (Favored by Social Media).
- **CA:** Does not exist in distributed systems across the internet, because you cannot prevent network partitions.

### Interview Question
"Explain the CAP Theorem and how it applies to choosing between Cassandra and a traditional SQL cluster for a massive e-commerce site."

### How to Answer
**The 'Think' Process:** Define CAP, state that P is unavoidable, and contrast AP (Cassandra) vs CP (SQL).
**The Answer:** "The CAP theorem states that a distributed database can only provide two of three guarantees: Consistency, Availability, and Partition Tolerance. Because network partitions (P) are unavoidable on the internet, the real choice is between Consistency and Availability. For an e-commerce shopping cart, we usually favor Availability (AP) because if the database locks up, users can't add items to their cart, directly losing revenue. A database like Cassandra (AP) will accept the write even during a network split, relying on Eventual Consistency to resolve the state later. A traditional SQL cluster (CP) would lock the database during a network failure to guarantee absolute Consistency, which is better for financial ledgers but bad for raw uptime."

---

## #92. The API Gateway Pattern [Type E — Implementation Scenario]
*(Applied concept of #14)*

### What is it?
Architecting the API Gateway not just as a proxy, but as an aggregator and orchestrator for microservices to prevent the frontend from making dozens of requests.

### Real-World Example
**Mobile E-commerce App:** To load the product page, the mobile app needs data from the Product Service, the Reviews Service, and the Inventory Service. Instead of the mobile app making 3 separate HTTP requests over the slow cellular network, it makes 1 request to the API Gateway. The Gateway makes the 3 fast internal requests, aggregates the JSON, and sends 1 response back.

### Architecture / Raw Diagram
```text
[ Mobile App ] ──(1 Request)──> [ API Gateway ]
                                  /    |    \
                            (3 Fast Internal Requests)
                                /      |      \
                         [ Product ] [ Review ] [ Inventory ]
```

### Interview Question
"In a microservices architecture, the mobile app is currently making 5 separate HTTP requests to load the home screen, causing it to load very slowly on 3G networks. How do you re-architect the backend to fix this?"

### How to Answer
**The 'Think' Process:** Mobile networks have high latency. Reduce network hops using API Gateway Aggregation.
**The Answer:** "The slow load time is caused by the latency of establishing 5 separate HTTP connections over a cellular network. I would implement the API Gateway Aggregation pattern. We would expose a single endpoint on the Gateway, such as `/mobile/home`. The mobile app makes one single HTTP request to this endpoint. The Gateway, which sits inside the fast AWS datacenter, makes the 5 internal microservice calls in parallel, aggregates the JSON responses into one unified payload, and sends it back to the mobile app in a single round-trip."

---

## #93. Distributed Locking (Redis Redlock) [Type C — Debugging Scenario]
*(Applied concept used heavily in ticketing/payments)*

### What is it?
A mechanism to ensure that across a cluster of hundreds of servers, only *one* server can execute a specific piece of code or modify a specific resource at a time.

### Mental Model
The "Speaking Conch" in Lord of the Flies. Even if there are 100 people in the room, you are only allowed to speak if you hold the conch. Once you finish, you put the conch down so someone else can grab it.

### Why does it exist?
In a monolith, you can use a thread lock in memory. In distributed systems (e.g., 50 Node.js servers), Server A doesn't share memory with Server B. If both try to process the same cron job or charge the same user, you get duplicates. They need a central authority to issue the lock.

### Real-World Example
**Cron Jobs:** You have a script that emails a weekly newsletter. Your API is load-balanced across 10 servers. At 9:00 AM, all 10 servers wake up and try to send the email. Without a distributed lock, the user gets 10 identical emails.

### Architecture / Raw Diagram
```text
[ Server 1 ] ──(SET lock:email NX)──> [ Redis ] (Returns Success: You have the lock)
[ Server 2 ] ──(SET lock:email NX)──> [ Redis ] (Returns Fail: Lock already taken)
(Server 2 aborts. Server 1 sends the email).
```

### Interview Question
"You have a microservice deployed across 5 instances. There is a scheduled cron job that runs every hour. How do you guarantee the job is only executed by one instance, preventing duplicate execution?"

### How to Answer
**The 'Think' Process:** Isolated memory means you need a central lock. Propose Redis distributed locks.
**The Answer:** "Because the instances don't share memory, we must use a centralized Distributed Lock, typically implemented using Redis. When the hour strikes, all 5 instances wake up and attempt to write a specific key to Redis using the `SET NX` (Set if Not Exists) command. Redis is single-threaded, so it guarantees that only the very first instance to send the command will succeed. That instance gains the lock and executes the cron job. The other 4 instances receive a failure response from Redis and immediately abort their execution."

---

## #94. Consistent Hashing [Type A — Concept]

### What is it?
An advanced algorithmic technique used in distributed caching and databases (like DynamoDB or Cassandra) to distribute data evenly across a cluster of servers, minimizing data movement when servers are added or removed.

### Mental Model
Instead of putting data in buckets numbered 1, 2, and 3, you place the servers on a giant circle (a clock face). You place the data on the clock face based on its hash. The data always belongs to the first server it finds by walking clockwise around the clock.

### Why does it exist?
If you have 3 Redis servers and use standard modulo hashing (`Hash(Key) % 3`), it works fine. But if Server 3 crashes, your formula becomes `% 2`. Suddenly, *every single hash* routes to a different server, resulting in a 100% cache miss rate and crushing your database.

### Architecture / Raw Diagram
```text
      [ Server A ] (12:00)
    /              \
(9:00)           (3:00) [ Data Key: User123 ] -> Walks clockwise, hits B.
[ Srv C ]          |
    \              /
      [ Server B ] (6:00)
```

### When Would I Use It?
- Only needed if you are building your own distributed caching layer or explaining how Cassandra routes traffic.

### Interview Question
"You have a cluster of 5 Redis cache servers. If you use standard modulo hashing (`user_id % 5`) to route requests, what catastrophic event happens if one server crashes?"

### How to Answer
**The 'Think' Process:** Changing the denominator changes all the results. Propose Consistent Hashing.
**The Answer:** "If a server crashes, the routing formula changes from modulo 5 to modulo 4. This mathematically changes the routing destination for almost every single key in the system. The result is a massive cache invalidation—nearly 100% cache misses—causing a 'Thundering Herd' that will instantly crush the underlying database. To prevent this, distributed systems use Consistent Hashing, which places servers and keys on a virtual ring. If a server dies, only the data specifically held by that one dead server is remapped to the next node on the ring; the rest of the cache remains perfectly intact."

---

## #95. Disaster Recovery (DR) & Backups [Type A — Concept]

### What is it?
The architectural plan and infrastructure setup designed to recover the system after a catastrophic event (e.g., an entire AWS Data Center burning down, or a hacker dropping the database).

### Metrics
- **RPO (Recovery Point Objective):** How much data can you afford to lose? (e.g., Backups run every hour = RPO is 1 hour).
- **RTO (Recovery Time Objective):** How long can the system be offline? (e.g., Taking 4 hours to restore the DB = RTO is 4 hours).

### Real-World Example
**AWS Multi-Region:** Your primary app runs in US-East (Virginia). You asynchronously replicate the database to US-West (California). If a hurricane destroys Virginia, you execute a DNS failover to California.

### Interview Question
"A junior developer accidentally executes a `DROP TABLE users;` command in production. Explain how your disaster recovery architecture dictates your response."

### How to Answer
**The 'Think' Process:** Standard Active-Passive replication won't save you from human error. You need Point-in-Time Recovery.
**The Answer:** "Standard database replication (like Read Replicas) will not save us here, because the `DROP TABLE` command is instantly replicated to all standby servers, deleting the data everywhere. To recover from this human error, we must rely on Point-in-Time Recovery (PITR) backups. Because the database takes automated daily snapshots and continuously archives the Write-Ahead Log (WAL), we can instruct the cloud provider to spin up a brand new database instance and replay the logs to restore the data to the exact second before the developer ran the destructive query."

---

## #96. Chaos Engineering [Type E — Implementation Scenario]

### What is it?
The practice of intentionally injecting failures into a production system (like randomly unplugging servers) to verify that the system is truly resilient and that failover mechanisms actually work.

### Mental Model
A fire drill. You don't wait for a real fire to find out if the emergency doors are locked. You pull the alarm randomly to test the system.

### Real-World Example
**Netflix Chaos Monkey:** A script that constantly runs in Netflix's production AWS environment, randomly terminating EC2 instances. Because developers know their servers could be killed at any second, they are forced to architect their microservices statelessly and with proper circuit breakers.

### Interview Question
"What is the philosophy behind Chaos Engineering, and how does a tool like Chaos Monkey improve system architecture?"

### How to Answer
**The 'Think' Process:** Proactive testing of resilience over reactive debugging.
**The Answer:** "The philosophy of Chaos Engineering is that distributed systems are inherently unpredictable, and you cannot guarantee High Availability just by writing good code. You must proactively test failure. Tools like Chaos Monkey deliberately and randomly shut down production servers or simulate network latency. By doing this continuously, it forces engineering teams to design their microservices defensively—using redundancy, circuit breakers, and stateless architectures—ensuring that when a real hardware failure occurs, the system degrades gracefully rather than crashing."

---

## #97. BFF Pattern (Backend for Frontend) [Type B — Practical Design]

### What is it?
Instead of having one massive, generalized API Gateway that serves Web, Mobile, and Smartwatch clients, you create a specific, dedicated API Gateway for each client type.

### Mental Model
A custom tailor. The Mobile app gets a tailor (BFF) that trims the JSON payloads down to be small and efficient. The Web dashboard gets a tailor (BFF) that aggregates massive amounts of data for complex charts.

### Why does it exist?
Mobile apps need highly optimized, small payloads to save battery and work on 3G. Web apps have broadband and need massive data payloads. Forcing them to share the exact same API endpoints results in terrible performance for mobile or missing data for web.

### Architecture / Raw Diagram
```text
[ Mobile App ] ──> [ Mobile BFF (Gateway) ] ──┐
                                              v
                                      [ Core Microservices ]
                                              ^
[ Web Browser ] ─> [ Web BFF (Gateway) ] ─────┘
```

### Interview Question
"Your company has a Web App and a Mobile App that both use the exact same REST API. The Mobile team is complaining that the API returns massive JSON payloads, slowing down the app. How do you solve this architecturally?"

### How to Answer
**The 'Think' Process:** Don't change the core microservices. Introduce the BFF pattern.
**The Answer:** "I would implement the Backend-for-Frontend (BFF) pattern. Modifying the core microservices to handle specific mobile formatting creates messy, coupled code. Instead, we introduce a dedicated Mobile BFF layer—a lightweight API Gateway owned specifically by the mobile team. The mobile app calls the BFF, and the BFF calls the core microservices. The BFF is responsible for aggregating the data, stripping out the heavy, unnecessary fields, and returning a perfectly tailored, lightweight JSON payload to the mobile device."

---

## #98. Canary Deployments & Blue/Green [Type E — Implementation Scenario]

### What is it?
Strategies for releasing new code to production with zero downtime and minimal risk.
- **Blue/Green:** You have two identical production environments. Blue is live. You deploy the new code to Green, test it, and then instantly flip the Load Balancer switch to point 100% of traffic to Green.
- **Canary:** You deploy the new code to a small subset of servers. You route 5% of user traffic to it. If errors don't spike, you slowly ramp it up to 100%.

### Why does it exist?
Deploying code by turning off the server, copying files, and turning it back on causes downtime. Releasing a massive bug to 100% of users ruins the company's reputation.

### Interview Question
"You are deploying a massive rewrite of the payment service. How do you release it to production while guaranteeing that if there is a critical bug, it affects the absolute minimum number of users?"

### How to Answer
**The 'Think' Process:** Risk mitigation requires a Canary release.
**The Answer:** "I would use a Canary Deployment strategy. Instead of replacing the existing payment service all at once, we deploy the new version alongside the old one. We configure the Load Balancer or API Gateway to route exactly 1% or 5% of live user traffic to the new service, while 95% remains on the stable version. We closely monitor the error rates and latency logs of the canary. If a critical bug appears, it only affects that 5%, and we can instantly route traffic back to the stable version. If the canary is healthy, we gradually dial the traffic up to 100%."

---

## #99. Data Warehouse vs Data Lake [Type A — Concept]

### What is it?
Massive storage systems for analytics (OLAP).
- **Data Warehouse:** Highly structured, cleaned, and organized relational data (e.g., Snowflake, AWS Redshift). Requires "Schema-on-Write".
- **Data Lake:** A massive dumping ground of unstructured, raw data (JSON, CSVs, images) usually stored in AWS S3. Requires "Schema-on-Read".

### Why does it exist?
Running analytical queries (`GROUP BY month, region`) across 5 years of sales data on your primary transactional PostgreSQL database (OLTP) will lock the tables and crash the live application.

### Interview Question
"What is the difference between a Data Warehouse and a Data Lake, and why shouldn't a Data Scientist run heavy analytical queries directly on the production PostgreSQL database?"

### How to Answer
**The 'Think' Process:** OLTP vs OLAP, and Structured vs Unstructured.
**The Answer:** "Running heavy analytical queries on the production PostgreSQL database (an OLTP system) consumes massive CPU and locks rows, which will slow down or crash the live application for users. Analytics must be done offline. A Data Warehouse (like Snowflake) stores this data in a highly structured, relational format optimized specifically for fast querying and business intelligence. A Data Lake, on the other hand, is a vast repository (like AWS S3) that stores raw, unstructured data—like raw JSON logs and images—exactly as they were generated, allowing Data Scientists to explore it later for machine learning without pre-defining a strict schema."

---

## #100. Putting It All Together: The Ultimate System Design Answer Framework [Type F — Framework]

### What is it?
This isn't a concept; it is the **Methodology** you must memorize to answer ANY system design interview question successfully. If you just start drawing boxes, you will fail.

### The 5-Step Framework (RADIO)

**1. Requirements (R)**
*Never start designing.* Ask clarifying questions.
- "How many daily active users?"
- "Is this read-heavy or write-heavy?"
- "Do we need high availability or strict consistency?"

**2. API Design (A)**
Define the contract between the frontend and backend.
- `POST /api/v1/tweet` (Payload: `{ text: "Hello" }`)
- `GET /api/v1/feed`

**3. Database Schema (D)**
Define the core tables and relationships.
- Relational or NoSQL?
- `Users (id, name)` | `Tweets (id, user_id, content)`

**4. Initial Design (I)**
Draw the basic MVP.
- Client -> Load Balancer -> API Servers -> Primary DB.

**5. Optimize & Scale (O)**
Identify the bottlenecks and apply the concepts you just learned.
- "Reads are too slow" -> **Add Redis Cache (Concept 34)** or **Read Replicas (Concept 32)**.
- "Uploads block the API" -> **Add SQS Queue (Concept 39)** or **S3 Pre-signed URLs (Concept 38)**.
- "Global users have high latency" -> **Add a CDN (Concept 36)**.
- "Database is too large" -> **Implement Sharding (Concept 33)**.
- "System crashes from traffic spikes" -> **Add Auto-scaling and API Gateway Rate Limiting (Concept 53)**.

### Final Interview Advice
A System Design interview is a **discussion**, not a test with one right answer. The interviewer wants to see you weigh **Trade-offs**. Always say: *"We could do X, which gives us [Benefit], but the trade-off is [Drawback]. Given our requirement of Y, I choose X."*

---
*(End of 100 System Design Concepts).*
