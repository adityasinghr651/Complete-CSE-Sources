# MODULE 1 — CONCEPTS 1–50 (PART 1: 1-12)

# A. SYSTEM DESIGN FUNDAMENTALS

## #1. System Design [Type A — Concept]

### What is it?
System design is the process of defining the architecture, components, modules, interfaces, and data for a system to satisfy specified requirements. It bridges the gap between raw business requirements and the actual code.

### Mental Model
It’s like being the architect of a city. Before the builders (coders) start pouring concrete, you must decide where the roads go, how the plumbing works, and how to prevent traffic jams.

### Why does it exist?
To ensure that a software system can scale, remain reliable under heavy traffic, and be maintained over time. Without it, a system will buckle under load or become too complex to update.

### Real-World Example
**Netflix** doesn't just have one massive computer streaming video. It uses system design to split video storage globally (Open Connect), manage user accounts separately, and cache thumbnails so the home page loads instantly.

### Architecture / Raw Diagram
```text
      ┌─────────┐
      │  User   │
      └────┬────┘
           │ (1) Request
           v
    ┌────────────┐
    │ Load Balancer│
    └────┬───────┘
         │ (2) Distribute
  ┌──────┴──────┐
  v             v
┌──────┐      ┌──────┐
│ App 1│      │ App 2│
└───┬──┘      └───┬──┘
    │ (3) Read/Write
    v             v
   ┌──────────────┐
   │   Database   │
   └──────────────┘
```

### Data Flow
```text
User Request
     ↓
Load Balancer
     ↓
Application Server (processes logic)
     ↓
Database (stores data)
     ↓
Response back to User
```

### When Would I Use It?
- When building any backend service that will serve more than a handful of users.
- When transitioning a prototype to a production-ready application.
- When interviewing for backend or full-stack engineering roles.

### When Would I NOT Use It?
- For simple scripts, hackathon prototypes, or static landing pages where scale is not an immediate concern.

### Trade-offs
- **What do I gain?** Scalability, reliability, and clear boundaries between components.
- **What do I sacrifice?** Development speed (upfront planning takes time) and operational simplicity (managing 5 services is harder than managing 1).

### Implementation Idea
If building a simple MVP, you don't need a complex design.
**MVP:** Frontend (React) → Backend (Express.js) → Database (PostgreSQL).
**Scaled:** Frontend → CDN → Load Balancer → Multiple Express.js nodes → Redis Cache → PostgreSQL primary/replica cluster.

### Interview Question
"What are the most important components to consider when designing a scalable backend system?"

### Follow-up
"If the database becomes the bottleneck, what are two immediate architectural changes you would make?"

### Common Mistake
Over-engineering. Candidates immediately suggest microservices, Kafka, and Cassandra for a system that will only have 1,000 daily users. 

---

## #2. Functional vs Non-functional Requirements [Type A — Concept]

### What is it?
- **Functional requirements** define *what* the system must do (e.g., "Users can upload a photo").
- **Non-functional requirements** define *how well* the system must do it (e.g., "Photo uploads must finish in under 2 seconds and be highly available").

### Mental Model
Functional = The features on a car (steering wheel, brakes, radio).
Non-functional = The performance of the car (top speed, fuel efficiency, safety rating).

### Why does it exist?
To clarify the scope of a system before designing it. You cannot design an architecture until you know exactly what it must do and how much load it must handle.

### Real-World Example
**WhatsApp:**
Functional: Users can send text messages to each other.
Non-functional: Messages must be delivered with minimum latency (under 100ms), and the system must be highly available (99.99% uptime).

### Architecture / Raw Diagram
(Not applicable for requirements gathering, but conceptually dictates the architecture below).
```text
REQUIREMENTS DICTATE ARCHITECTURE

High Availability? ──────> Add Replicas & Load Balancers
Low Latency?       ──────> Add Redis / CDN
Data Integrity?    ──────> Use ACID Relational Database
```

### Data Flow
N/A

### When Would I Use It?
- The absolute first step in *every* system design interview.

### When Would I NOT Use It?
- Never. Skipping requirements gathering guarantees you will build the wrong system.

### Trade-offs
- **What do I gain?** Clear constraints and a defined scope.
- **What do I sacrifice?** N/A (It’s a mandatory process step).

### Implementation Idea
In an interview, write them down explicitly:
1. "Let's define the functional requirements: Users can post tweets, follow others, and view timelines."
2. "Now, non-functional: The system must handle 10k reads/sec, be highly available, but eventual consistency on the timeline is acceptable."

### Interview Question
"What requirements would you gather before designing Twitter?"

### Follow-up
"Which non-functional requirement is more critical for a banking app: Availability or Consistency, and why?"

### Common Mistake
Skipping non-functional requirements. Candidates build a system that works, but fail to address how it survives 1 million concurrent users.

---

## #3. Scalability (Horizontal vs Vertical) [Type A — Concept]

### What is it?
Scalability is the ability of a system to handle increased load.
- **Vertical Scaling (Scale Up):** Adding more power (CPU, RAM) to an existing machine.
- **Horizontal Scaling (Scale Out):** Adding more machines to the resource pool.

### Mental Model
Vertical = Upgrading your Honda Civic to a Ferrari to carry things faster.
Horizontal = Buying 5 Honda Civics to carry 5 times as many things at once.

### Why does it exist?
Because systems grow. When a server hits 100% CPU, you need a way to handle the next incoming request without crashing.

### Real-World Example
**Amazon Web Services (AWS):** If your single EC2 instance is struggling, you can either upgrade it to a larger instance type (Vertical) or use an Auto Scaling Group to spin up 5 identical instances behind an Application Load Balancer (Horizontal).

### Architecture / Raw Diagram
```text
VERTICAL SCALING:
  ┌────┐         ┌────────┐
  │ 2GB│   --->  │  16GB  │
  └────┘         └────────┘

HORIZONTAL SCALING:
                 ┌────┐
  ┌────┐   --->  │ 2GB│
  │ 2GB│         ├────┤
  └────┘         │ 2GB│
                 ├────┤
                 │ 2GB│
                 └────┘
```

### Data Flow
N/A (Structural concept)

### When Would I Use It?
- **Vertical:** When you need a quick fix, or for relational databases where horizontal scaling (sharding) is extremely complex.
- **Horizontal:** When building web servers, APIs, or worker queues where tasks are stateless and easily distributed.

### When Would I NOT Use It?
- **Vertical:** Avoid as a long-term solution because it has a hard hardware limit and introduces a single point of failure.

### Trade-offs
- **Vertical:** Easy to implement, no code changes needed. BUT has hardware limits and no redundancy.
- **Horizontal:** Infinite scalability, high availability. BUT requires load balancers, stateless architecture, and distributed data management.

### Implementation Idea
For a Node.js API:
**Vertical:** Run it on a machine with 32 cores.
**Horizontal:** Dockerize the Node.js API and use Kubernetes (or AWS ECS) to spin up 10 container replicas, routed via an Nginx Load Balancer.

### Interview Question
"Your API server is at 99% CPU utilization. How do you scale it?"

### Follow-up
"If you choose to scale horizontally, what must be true about the state of your application servers?" (Answer: They must be stateless).

### Common Mistake
Saying "I will horizontally scale the database." Scaling a relational database horizontally (sharding) is notoriously difficult; you usually scale databases vertically first, then use read replicas, and only shard as a last resort.

---

## #4. Monolith vs Microservices [Type D — Trade-off Scenario]

### What is it?
- **Monolith:** All business logic, UI, and data access code is bundled into a single deployable application.
- **Microservices:** The application is split into small, loosely coupled, independently deployable services organized around business capabilities.

### Mental Model
Monolith = A Swiss Army knife. One tool that does everything.
Microservices = A mechanic's toolbox. Many specialized tools, each doing one thing perfectly.

### Why does it exist?
To manage complexity and deployment friction as engineering teams grow. A monolith is easy for 5 developers but a nightmare for 500 developers who step on each other's toes during deployments.

### Real-World Example
**Uber** started as a single monolithic backend (dispatch, billing, driver tracking in one app). As they grew to thousands of engineers, they broke it down into thousands of microservices so the Billing team could deploy updates without breaking the Driver Dispatch system.

### Architecture / Raw Diagram
```text
MONOLITH                      MICROSERVICES

┌───────────────┐             ┌────────┐   ┌────────┐
│  Auth         │             │  Auth  │   │Billing │
│  Billing      │             │ Service│   │Service │
│  Inventory    │             └────┬───┘   └────┬───┘
└───────┬───────┘                  │            │
        │                          v            v
        v                     ┌────────┐   ┌────────┐
┌───────────────┐             │  DB 1  │   │  DB 2  │
│ Shared DB     │             └────────┘   └────────┘
└───────────────┘
```

### Data Flow
**Microservices Flow:**
```text
Request
  ↓
API Gateway
  ↓ (routes by path)
Billing Service
  ↓ (HTTP / gRPC)
Notification Service
  ↓
Database
```

### When Would I Use It?
- **Monolith:** When starting a new project, for startups finding product-market fit, or small teams.
- **Microservices:** When you have a massive engineering organization, strict scaling requirements for *specific* components, or independent deployment needs.

### When Would I NOT Use It?
- Do NOT use microservices for a day-1 startup MVP. The operational overhead (Kubernetes, distributed tracing, network latency) will slow you down.

### Trade-offs
- **Microservices:** Independent deployments, localized scaling. BUT massive operational complexity, network latency, and distributed data consistency headaches.
- **Monolith:** Simple deployment, fast in-memory function calls. BUT tightly coupled code, hard to scale specific bottlenecks.

### Implementation Idea
**MVP (Monolith):** A single NestJS application with an `AuthModule` and `BillingModule` sharing one PostgreSQL DB.
**Scaled (Microservices):** Extract the `BillingModule` into a separate Python/FastAPI service with its own DB. The NestJS app communicates with it via HTTP REST or Kafka.

### Interview Question
"You are tasked with building a new E-commerce backend from scratch. Do you choose a monolith or microservices architecture, and why?"

### Follow-up
"How do you handle a transaction that requires updating data in both the Order Service and the Payment Service?"

### Common Mistake
Defaulting to microservices for everything because it's "modern." Good engineers highlight that monoliths (specifically modular monoliths) are usually the correct starting point.

---

## #5. Single Point of Failure (SPOF) [Type C — Debugging Scenario]

### What is it?
A part of a system that, if it fails, will stop the entire system from working.

### Mental Model
A SPOF is a one-lane bridge connecting two halves of a city. If the bridge collapses, traffic halts entirely.

### Why does it exist?
SPOFs often exist in early architectures because redundancy (having backups) costs money and time to build.

### Real-World Example
If your entire startup's database runs on a single AWS EC2 instance without a replica, and that instance crashes, your whole app goes offline.

### Architecture / Raw Diagram
```text
WITH SPOF (Load Balancer is a SPOF):

      Client
        │
        v
 ┌─────────────┐ <--- If this crashes, system is dead
 │Load Balancer│
 └──────┬──────┘
        │
    ┌───┴───┐
    v       v
  API 1   API 2

-----------------------------------------
ELIMINATED SPOF (Active/Passive Failover):

         Client
           │
           v
        [ DNS ]
       /       \
      v         v
┌───────┐     ┌───────┐
│ LB 1  │-HB->│ LB 2  │ (Heartbeat checks)
└───────┘     └───────┘
```

### Data Flow
If a SPOF fails, data flow immediately stops and returns `503 Service Unavailable` or a network timeout.

### When Would I Use It?
- Identifying SPOFs is a critical step in the "Failure Handling" phase of a system design interview.

### When Would I NOT Use It?
- N/A

### Trade-offs
- **What do I gain (by fixing it)?** High availability and fault tolerance.
- **What do I sacrifice?** Infrastructure costs double (e.g., you now pay for two load balancers instead of one).

### Implementation Idea
- **App Tier SPOF:** Run multiple instances of your Node API behind a Load Balancer.
- **Database SPOF:** Set up a Primary-Replica PostgreSQL architecture with automatic failover (like Amazon RDS Multi-AZ).

### Interview Question
"Looking at this architecture (Client -> App -> DB), identify the single points of failure and explain how to mitigate them."

### Follow-up
"If you have two Load Balancers for redundancy, how does the client know which one to talk to?" (Answer: DNS routing policies, like Route53).

### Common Mistake
Only removing SPOFs at the application layer while forgetting the database layer or the Load Balancer itself.

---

## #6. Latency vs Throughput [Type A — Concept]

### What is it?
- **Latency:** The time it takes for a single request to travel from the client, be processed, and return. (Measured in milliseconds).
- **Throughput:** The number of requests the system can process in a given amount of time. (Measured in Requests Per Second - RPS).

### Mental Model
Latency is how fast a single drop of water travels through a pipe.
Throughput is how many gallons of water flow out of the pipe per minute.

### Why does it exist?
They are the two primary metrics used to measure system performance and dictate entirely different architectural choices.

### Real-World Example
**Multiplayer Game (Valorant):** Requires extremely **low latency** (under 30ms) so players don't lag. Throughput per player is relatively low.
**Data Pipeline (Hadoop):** Requires high **throughput** (processing terabytes of logs per hour). Latency of a single log doesn't matter (it can take 5 minutes).

### Architecture / Raw Diagram
```text
Latency focus:
Client ───────> Edge CDN Node (Geographically close) ──> Fast Response

Throughput focus:
Client ───────> Kafka Queue ──> 50 Worker Nodes ──> Big Database
```

### Data Flow
N/A

### When Would I Use It?
- Use latency as a metric when discussing user-facing APIs, web page load times, and real-time chat.
- Use throughput when discussing background jobs, video encoding, and analytics pipelines.

### When Would I NOT Use It?
- Never, always define these during the requirements phase.

### Trade-offs
- Often you must trade one for the other. For example, batching database writes improves **throughput** (less network overhead) but worsens **latency** (requests wait in a queue to be batched before returning).

### Implementation Idea
**Improve Latency:** Add a Redis cache so you don't hit the database, reducing response time from 200ms to 20ms.
**Improve Throughput:** Add a Kafka message queue and horizontally scale consumer workers to process 10,000 tasks per second.

### Interview Question
"What is the difference between latency and throughput? Can you have a system with high latency but high throughput?"

### Follow-up
"If your API throughput is fine, but p99 latency is spiking to 5 seconds, how would you investigate?"

### Common Mistake
Assuming they are the same thing. Saying "I will add a load balancer to reduce latency" is wrong; a load balancer increases throughput but actually adds a tiny bit of network latency.

---

## #7. Capacity Estimation & Back-of-the-envelope [Type B — Practical Design]

### What is it?
Quick mathematical estimates done at the beginning of an interview to determine the scale of the system (bandwidth, storage, memory, and RPS).

### Mental Model
Like estimating how much food to buy for a party. You don't need exact grams, just rough math: 50 people × 2 slices of pizza = 100 slices.

### Why does it exist?
To determine if you can store everything on one machine or if you need a distributed system. 10 GB of data fits in RAM; 10 Petabytes requires distributed storage.

### Real-World Example
**Twitter:**
- 300 million active users.
- 10% write tweets daily = 30 million tweets/day.
- 30M / 100,000 seconds (approx seconds in a day) = 300 writes/second.
- 300 writes/sec is easily handled by a single modern SQL database. No extreme write-sharding needed.

### Architecture / Raw Diagram
```text
Estimate Scale:
10M Users ─> 100 req/day ─> 1 Billion req/day ─> ~12,000 RPS
                  ↓
       Requires Load Balancers + Multiple API Nodes
```

### Data Flow
N/A

### When Would I Use It?
- At the start of every full system design interview, right after clarifying requirements.

### When Would I NOT Use It?
- If the interviewer explicitly says "skip capacity estimation and jump into the architecture."

### Trade-offs
- Keep math simple. Round 1 day to 100,000 seconds (actual is 86,400) to make mental division easy.

### Implementation Idea
**Template to memorize:**
1. Monthly Active Users (MAU) -> Daily Active Users (DAU)
2. Writes/sec = (DAU * daily writes) / 100,000
3. Reads/sec = Writes/sec * Read/Write Ratio
4. Storage/day = Writes/day * size of 1 item (e.g. 1KB)

### Interview Question
"Estimate the storage requirements for YouTube over 5 years."

### Follow-up
"Given that most videos are rarely watched, how does this storage estimate impact your caching strategy?"

### Common Mistake
Getting bogged down in exact arithmetic (doing 86,400 division on a whiteboard). Round numbers generously.

---

## #8. CAP Theorem [Type A — Concept]

### What is it?
In a distributed data store, you can only guarantee two out of three properties simultaneously:
- **C**onsistency: Every read receives the most recent write or an error.
- **A**vailability: Every request receives a non-error response, without the guarantee that it contains the most recent write.
- **P**artition Tolerance: The system continues to operate despite an arbitrary number of messages being dropped or delayed by the network between nodes.

### Mental Model
Imagine you and your friend manage a ledger, but you are in different cities communicating by phone. If the phone line breaks (Network Partition):
You must choose: Reject customer transactions until the phone works (Consistency), or keep accepting transactions independently knowing your ledgers will briefly mismatch (Availability).

### Why does it exist?
Because network failures (Partitions) are inevitable in the real world. Therefore, a distributed system MUST choose between Consistency (CP) and Availability (AP) during a failure.

### Real-World Example
**Banking System (CP):** If an ATM network splits, it prefers to reject withdrawals (loss of Availability) rather than allow an overdrawn account (strict Consistency).
**Social Media Feed (AP):** If the network splits, Facebook prefers showing you slightly stale posts (loss of Consistency) rather than showing an error page (high Availability).

### Architecture / Raw Diagram
```text
           [ Network Partition X ]
           
 ┌─────────┐                     ┌─────────┐
 │ Node A  │ ─ ─ ─ ─ X ─ ─ ─ ─ ─ │ Node B  │
 └─────────┘                     └─────────┘
 (Updated to v2)                 (Still at v1)
 
 Client requests data from Node B:
 - AP system: Returns v1 (Available, but stale).
 - CP system: Returns Error (Consistent, but unavailable).
```

### Data Flow
N/A

### When Would I Use It?
- When deciding between NoSQL databases (Cassandra is AP, MongoDB is generally CP).
- When discussing the trade-offs of replicating data globally.

### When Would I NOT Use It?
- Single-node systems. CAP only applies to distributed data stores.

### Trade-offs
- **AP:** Better user experience (system is always up), but risk of reading stale data.
- **CP:** Absolute data correctness, but user-facing errors during network issues.

### Implementation Idea
If building a chat app, configure your database for AP (e.g., Cassandra). It's okay if a message appears a second late on a secondary device, as long as the app doesn't crash.

### Interview Question
"How does the CAP theorem apply to the choice between a relational database and a system like Cassandra?"

### Follow-up
"Is it possible to have a CA system?" (Answer: Theoretically yes, but only if you assume the network will *never* fail, which is physically impossible in distributed systems. Hence, you always have P, and choose between A and C).

### Common Mistake
Thinking CAP applies during normal operations. CAP forces a choice *only* when there is a network partition (failure). During normal operations, you can have both C and A.

---

## #9. Stateless vs Stateful Systems [Type D — Trade-off Scenario]

### What is it?
- **Stateless:** The server stores no information about past client requests. Every request must contain all the context needed to process it.
- **Stateful:** The server remembers previous interactions (state), saving data locally in memory or on disk.

### Mental Model
Stateless = Ordering at a fast-food drive-thru. If you come back 5 minutes later, they don't know who you are; you have to explain your whole order again.
Stateful = A bartender who knows you. "I'll have the usual" works because they remember your state.

### Why does it exist?
Statelessness is the secret to easy horizontal scaling. If servers don't store session data, any load balancer can route any request to any server.

### Real-World Example
**JWT Authentication (Stateless):** The token itself holds all user info. Any API server can verify it without looking up a session in memory.
**In-Memory Session (Stateful):** A Node.js app storing user logins in RAM. If the load balancer sends the user's 2nd request to a different Node.js instance, they appear logged out.

### Architecture / Raw Diagram
```text
STATEFUL (Hard to scale)
User 1 ─> LB ─> Server A (Remembers User 1)
User 1 ─> LB ─> Server B (Who is User 1? -> ERROR)

STATELESS (Easy to scale)
User 1 [Token] ─> LB ─> Server A (Validates token)
User 1 [Token] ─> LB ─> Server B (Validates token) -> SUCCESS
```

### Data Flow
```text
Client Request (includes state/token)
   ↓
Any API Server
   ↓
API processes request without relying on local memory
   ↓
Database (Persistent State)
```

### When Would I Use It?
- **Stateless:** Almost always for web backend APIs, REST services, and microservices.
- **Stateful:** Real-time multiplayer game servers (tracking player positions in RAM), WebSockets, or databases themselves.

### When Would I NOT Use It?
- Don't use Stateful APIs if you plan to deploy your application to Kubernetes and auto-scale pods.

### Trade-offs
- **Stateless:** Trivial to horizontally scale. BUT clients must send more data per request (e.g., large JWTs), and processing might take slightly longer.
- **Stateful:** Fast access to local memory. BUT scaling requires complex "sticky sessions" at the load balancer level.

### Implementation Idea
Instead of using `express-session` storing data in Node.js memory, use JWTs (stateless) or store the session ID in a centralized Redis cache (making the API servers stateless, pushing state to the cache tier).

### Interview Question
"Why do we prefer stateless web servers in modern cloud architectures?"

### Follow-up
"If a web server is entirely stateless, where does the state actually live?" (Answer: Pushed out to the client as tokens, or pushed down to a centralized Database/Cache).

### Common Mistake
Thinking "stateless" means the application has no data at all. It just means the *application server* doesn't hold the data locally; the state is stored in the database.

---

# B. API DESIGN

## #10. REST API Design [Type A — Concept]

### What is it?
Representational State Transfer (REST) is an architectural style for APIs that uses standard HTTP methods (GET, POST, PUT, DELETE) to perform CRUD operations on resources, identified by URLs.

### Mental Model
REST is like a library catalog system. You ask for a resource by its exact address (URL), and specify whether you want to read it (GET), add a new one (POST), or throw it away (DELETE).

### Why does it exist?
Before REST, APIs used complex XML/SOAP protocols that were heavy and hard to debug. REST leverages standard web protocols (HTTP), making it universally compatible and simple.

### Real-World Example
**GitHub API:**
- `GET /users/octocat` (Fetch a user)
- `POST /repos/octocat/hello-world/issues` (Create an issue)

### Architecture / Raw Diagram
```text
Client (Web/Mobile)
  │ 
  │ GET /api/v1/users/123
  v
API Gateway
  │
  v
User Service (REST API)
  │ SELECT * FROM users WHERE id = 123
  v
Database
```

### Data Flow
```text
Client sends HTTP Request (Method, Headers, JSON Body)
     ↓
API Server parses URL and maps to a Controller
     ↓
Business Logic executes
     ↓
Server returns HTTP Status Code (e.g., 200 OK) + JSON payload
```

### When Would I Use It?
- Standard web and mobile application backends.
- Public-facing APIs (e.g., Stripe, Twilio).

### When Would I NOT Use It?
- When you need bi-directional real-time communication (use WebSockets).
- When a client needs to fetch deeply nested, highly specific data graphs without over-fetching (use GraphQL).

### Trade-offs
- **What do I gain?** Simplicity, cacheability (GET requests are natively cacheable by browsers/CDNs), and standard status codes.
- **What do I sacrifice?** Over-fetching/under-fetching data (you get the whole resource, even if you only needed one field).

### Implementation Idea
Use Express.js or FastAPI.
Structure routes by noun, not verb:
Good: `POST /articles`
Bad: `POST /createArticle`

### Interview Question
"Design the REST API endpoints for a simple blogging platform."

### Follow-up
"How would you handle updating just the title of an article without overwriting the entire resource?" (Answer: Use `PATCH` instead of `PUT`).

### Common Mistake
Using verbs in the URI (e.g., `/getUsers`) and returning `200 OK` for errors with a JSON body saying `{"status": "error"}` instead of using proper HTTP error codes like `404` or `400`.

---

## #11. Pagination, Filtering, Sorting [Type B — Practical Design]

### What is it?
Techniques to manage large datasets returned by an API.
- **Pagination:** Splitting results into pages.
- **Filtering:** Returning only results matching criteria.
- **Sorting:** Ordering results.

### Mental Model
Like shopping on Amazon. You filter by "Electronics", sort by "Price: Low to High", and look at Page 1 of 50.

### Why does it exist?
If your database has 1 million users, `GET /users` returning 1 million JSON objects will crash the database, the network, and the client browser.

### Real-World Example
**Reddit** uses cursor-based pagination. As you scroll, the API requests `?after=t3_xyz123`, allowing infinite scroll without performance degradation.

### Architecture / Raw Diagram
```text
API Request: GET /products?category=shoes&sort=price_desc&limit=20&offset=40

      [ API Server ]
            │
            │ Translates to SQL:
            │ SELECT * FROM products 
            │ WHERE category = 'shoes' 
            │ ORDER BY price DESC 
            │ LIMIT 20 OFFSET 40;
            v
      [ Database ]
```

### Data Flow
Request with query parameters -> Controller extracts parameters -> ORM/SQL query built dynamically -> DB executes subset query -> Returns array of 20 items.

### When Would I Use It?
- Any `GET` endpoint that returns a list (arrays).

### When Would I NOT Use It?
- Endpoints fetching a single specific resource by ID.

### Trade-offs
- **Offset Pagination (`limit`, `offset`):** Easy to implement, allows jumping to page 5. BUT degrades severely in performance on deep pages (e.g., offset 1,000,000 means the DB scans and discards a million rows).
- **Cursor/Keyset Pagination (`next_cursor`):** Extremely fast for infinite scroll because it uses indexed lookups (`WHERE id > last_id`). BUT you cannot jump straight to page 50.

### Implementation Idea
**Node.js/Express MVP:**
```javascript
app.get('/users', async (req, res) => {
  const limit = parseInt(req.query.limit) || 10;
  const cursor = req.query.cursor; // Last seen ID
  
  // Cursor-based approach
  const users = await db.query(
    'SELECT * FROM users WHERE id > $1 ORDER BY id ASC LIMIT $2', 
    [cursor || 0, limit]
  );
  res.json({ data: users, next_cursor: users[users.length - 1].id });
});
```

### Interview Question
"You designed an API returning user posts. As the app grows, the 'GET /posts' endpoint gets extremely slow. How do you fix it?"

### Follow-up
"Explain why OFFSET pagination is slow at the database level."

### Common Mistake
Implementing pagination in memory. E.g., fetching all 1 million rows from the DB into the Node.js array, and then slicing `array.slice(0, 20)`. This destroys server memory.

---

## #12. Rate Limiting [Type E — Implementation Scenario]

### What is it?
A mechanism to control the number of requests a client can make to an API within a specified time window (e.g., 100 requests per minute).

### Mental Model
Like a bouncer at a club. The club (API) can only fit so many people. If you try to enter 10 times a minute, the bouncer tells you to wait.

### Why does it exist?
To protect backend servers from being overwhelmed by traffic spikes, prevent DDoS attacks, limit brute-force login attempts, and enforce pricing tiers (e.g., free tier = 10 req/min).

### Real-World Example
**Twitter API:** If you write a script to scrape tweets, you will quickly hit a `429 Too Many Requests` error with an `X-RateLimit-Reset` header telling you when you can try again.

### Architecture / Raw Diagram
```text
Client Request
      ↓
┌──────────────┐
│ API Gateway  │ ─> Query Redis: "How many reqs for user_id_123?"
└──────┬───────┘
       │  (If < 100) -> Route to API
       │  (If >= 100) -> Return 429
       v
  API Server
```

### Data Flow
1. Client sends request with API key or IP.
2. Gateway checks Redis for `key: request_count`.
3. If count exceeds limit, immediately return `HTTP 429`.
4. If under limit, increment count in Redis, pass request to backend.

### When Would I Use It?
- Login endpoints (prevent brute force).
- Public/External APIs.
- Anywhere a single malicious or buggy client could exhaust server resources.

### When Would I NOT Use It?
- Strictly internal APIs between two trusted microservices inside a VPC (though backpressure is still needed).

### Trade-offs
- **What do I gain?** System stability and cost control.
- **What do I sacrifice?** Added latency (every request now requires a cache lookup) and infrastructure complexity (need a distributed cache).

### Implementation Idea
Use the **Token Bucket** or **Sliding Window** algorithm via Redis.
MVP using Express + `express-rate-limit` (in-memory):
```javascript
const rateLimit = require('express-rate-limit');
const limiter = rateLimit({
  windowMs: 1 * 60 * 1000, // 1 minute
  max: 100 // limit each IP to 100 reqs
});
app.use(limiter);
```
Scaled: Use Redis so the rate limit state is shared across all 10 load-balanced API servers.

### Interview Question
"How would you implement a distributed rate limiter for a public API?"

### Follow-up
"Why use Redis instead of a relational database for tracking rate limit counters?" (Answer: Redis is entirely in-memory and heavily optimized for the `INCR` operations needed for fast rate limiting; a DB would be too slow).

### Common Mistake
Implementing rate limiting in application memory when running multiple load-balanced instances. If a user hits Server A 100 times, Server B still thinks they have 0 requests. You must use a centralized store like Redis.

---
*(End of Part 1. I will provide the next set of concepts in the next artifact updates).*
