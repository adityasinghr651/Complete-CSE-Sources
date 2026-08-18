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
- For simple scripts, hackathon prototypes, or static landing pages where scale is not an immediate concern and speed of delivery is the only priority.

### Trade-offs
- **What do I gain?** Scalability, reliability, and clear boundaries between components.
- **What do I sacrifice?** Development speed (upfront planning takes time) and operational simplicity (managing 5 services is harder than managing 1).

### Implementation Idea
If building a simple MVP, you don't need a complex design.
**MVP:** Frontend (React) → Backend (Express.js) → Database (PostgreSQL).
**Scaled:** Frontend → CDN → Load Balancer → Multiple Express.js nodes → Redis Cache → PostgreSQL primary/replica cluster.

### Interview Question
"What are the most important components to consider when designing a scalable backend system?"

### How to Answer
**The 'Think' Process:** Break this down into the standard layers of a request lifecycle: Entry (Load Balancing), Compute (APIs), Storage (DBs), and Speed (Caching/Queues). Don't just list them; explain *why* they matter.
**The Answer:** "I think about a scalable backend in layers. At the entry point, a **Load Balancer** is critical to distribute traffic and prevent single points of failure. For compute, the **API Servers** must be stateless so they can scale horizontally. For storage, choosing the right **Database** (SQL for transactions, NoSQL for massive scale) is key. Finally, to ensure low latency and high throughput, I consider **Caching** (like Redis) for read-heavy operations and **Message Queues** (like Kafka) to offload heavy background tasks asynchronously."

### Follow-up
"If the database becomes the bottleneck, what are two immediate architectural changes you would make?"

### How to Answer (Follow-up)
**The 'Think' Process:** A database bottleneck usually means too many reads or too many writes. Offer one solution for reads (cache/replicas) and one for writes (queues/sharding).
**The Answer:** "First, I would look at the read-to-write ratio. If it's read-heavy, I would introduce a **Distributed Cache** like Redis in front of the database to absorb repeated queries, or add **Read Replicas** to spread the load. If it's write-heavy, I would introduce a **Message Queue** to buffer the incoming writes, smoothing out the traffic spikes so the database isn't overwhelmed."

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
```text
REQUIREMENTS DICTATE ARCHITECTURE

High Availability? ──────> Add Replicas & Load Balancers
Low Latency?       ──────> Add Redis / CDN
Data Integrity?    ──────> Use ACID Relational Database
```

### Data Flow
**Conceptual Flow:**
1. Interviewer gives prompt: "Design Twitter."
2. You ask: "Can users post videos, or just text?" (Functional).
3. You ask: "How many daily active users are we expecting?" (Non-functional).
4. You use these answers to size the database and decide on caching.

### When Would I Use It?
- The absolute first step in *every* system design interview.

### When Would I NOT Use It?
- If the interviewer explicitly hands you a requirements document and says, "Assume these are all the constraints, skip to the architecture." (Very rare).

### Trade-offs
- **What do I gain?** Clear constraints and a defined scope, ensuring you build exactly what the interviewer wants.
- **What do I sacrifice?** Taking 5 minutes at the start means slightly less time for drawing, but it prevents you from wasting 30 minutes designing the wrong system.

### Implementation Idea
In an interview, write them down explicitly:
1. "Let's define the functional requirements: Users can post tweets, follow others, and view timelines."
2. "Now, non-functional: The system must handle 10k reads/sec, be highly available, but eventual consistency on the timeline is acceptable."

### Interview Question
"What requirements would you gather before designing Twitter?"

### How to Answer
**The 'Think' Process:** Group your questions clearly into Functional (User actions) and Non-Functional (System metrics).
**The Answer:** "I would first gather Functional requirements to understand the core features: Can users post media, or just text? Do we need to support a home timeline and a user profile timeline? Is there a search feature? Then, I would ask about Non-Functional requirements to determine the scale: What is the expected Daily Active User (DAU) count? Is the system read-heavy or write-heavy? What are our latency expectations for feed generation, and is high availability more important than strong consistency?"

### Follow-up
"Which non-functional requirement is more critical for a banking app: Availability or Consistency, and why?"

### How to Answer (Follow-up)
**The 'Think' Process:** Recall the CAP theorem. For finance, math must be perfect. If the bank shows the wrong balance (inconsistent), it's a disaster. If the app is down (unavailable), it's annoying but safe.
**The Answer:** "Consistency is far more critical for a banking app. If a user transfers money, the system must guarantee that the sender's account is debited and the receiver's account is credited perfectly before allowing any other operations. If there is a network issue, it is better for the app to be temporarily Unavailable (rejecting the transaction) than to allow an Inconsistent state where money is duplicated or lost."

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
**Horizontal Data Flow:**
1. 1000 requests hit the Load Balancer.
2. Load balancer splits them: 333 to Node A, 333 to Node B, 334 to Node C.
3. Nodes process independently in parallel and return responses.

### When Would I Use It?
- **Vertical:** When you need a quick fix, or for relational databases where horizontal scaling (sharding) is extremely complex.
- **Horizontal:** When building web servers, APIs, or worker queues where tasks are stateless and easily distributed.

### When Would I NOT Use It?
- **Vertical:** Avoid as a long-term solution for web servers because it has a hard physical limit (you can only buy so much RAM) and introduces a single point of failure.
- **Horizontal:** Avoid for legacy monolithic databases that were designed strictly for single-node ACID transactions.

### Trade-offs
- **Vertical:** Easy to implement, no code changes needed. BUT has hardware limits and no redundancy.
- **Horizontal:** Infinite scalability, high availability. BUT requires load balancers, stateless architecture, and distributed data management.

### Implementation Idea
For a Node.js API:
**Vertical:** Run it on an AWS `c5.24xlarge` machine with 96 cores.
**Horizontal:** Dockerize the Node.js API and use Kubernetes (or AWS ECS) to spin up 10 small container replicas, routed via an Nginx Load Balancer.

### Interview Question
"Your API server is at 99% CPU utilization. How do you scale it?"

### How to Answer
**The 'Think' Process:** Briefly mention vertical scaling as a stopgap, but pivot quickly to horizontal scaling as the industry standard, outlining the components needed (Stateless APIs + Load Balancer).
**The Answer:** "As a temporary hotfix, I could vertically scale the server by upgrading its CPU and RAM. However, the proper long-term solution is horizontal scaling. I would ensure the API is stateless—moving any session data to Redis—and then spin up multiple identical instances of the API server. Finally, I would place an Application Load Balancer in front of them to distribute the incoming traffic evenly, giving us infinite scalability and fault tolerance."

### Follow-up
"If you choose to scale horizontally, what must be true about the state of your application servers?"

### How to Answer (Follow-up)
**The 'Think' Process:** Think about what happens if Server A remembers a user, but Server B doesn't.
**The Answer:** "The application servers must be completely stateless. They cannot store any user session data, uploaded files, or cache locally in RAM. If they did, a user's second request might hit a different server and fail. All state must be pushed out to a centralized database, a distributed cache like Redis, or an object store like S3."

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
  ↓ (routes by path /billing)
Billing Service
  ↓ (Calls Auth via HTTP/gRPC to verify token)
Auth Service (Replies OK)
  ↓
Billing Service writes to Billing DB
```

### When Would I Use It?
- **Monolith:** When starting a new project, for startups finding product-market fit, or small teams.
- **Microservices:** When you have a massive engineering organization, strict scaling requirements for *specific* components, or independent deployment needs.

### When Would I NOT Use It?
- Do NOT use microservices for a day-1 startup MVP. The operational overhead (Kubernetes, distributed tracing, network latency) will slow you down and burn your runway.

### Trade-offs
- **Microservices:** Independent deployments, localized scaling. BUT massive operational complexity, network latency, and distributed data consistency headaches.
- **Monolith:** Simple deployment, fast in-memory function calls. BUT tightly coupled code, hard to scale specific bottlenecks.

### Implementation Idea
**MVP (Monolith):** A single NestJS application with an `AuthModule` and `BillingModule` sharing one PostgreSQL DB.
**Scaled (Microservices):** Extract the `BillingModule` into a separate Python/FastAPI service with its own DB. The NestJS app communicates with it via HTTP REST or Kafka.

### Interview Question
"You are tasked with building a new E-commerce backend from scratch. Do you choose a monolith or microservices architecture, and why?"

### How to Answer
**The 'Think' Process:** Don't just say "Microservices because it scales." Interviewers want to see pragmatism. Acknowledge the overhead of microservices and recommend a modular monolith as the starting point.
**The Answer:** "For a brand new backend, I would start with a Modular Monolith. Building microservices on day one introduces massive operational overhead—like managing Kubernetes, distributed tracing, and network latency—before we even have users. By building a well-structured monolith where domains like 'Billing' and 'Inventory' are separated logically in the code but deployed together, we can iterate rapidly. Later, if the 'Billing' module becomes a bottleneck or requires a dedicated team, we can easily extract it into a true microservice."

### Follow-up
"How do you handle a transaction that requires updating data in both the Order Service and the Payment Service?"

### How to Answer (Follow-up)
**The 'Think' Process:** In a monolith, this is a simple ACID database transaction. In microservices, it's a distributed transaction. Mention the Saga pattern.
**The Answer:** "Because microservices have independent databases, we cannot use a standard SQL transaction. Instead, we use the Saga Pattern. The Order Service creates a pending order and publishes an event. The Payment Service listens, processes the payment, and publishes a success or failure event. If the payment fails, the Order Service listens for that failure and executes a 'compensating transaction' to cancel the pending order, ensuring eventual consistency."

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
**Failure Flow:** If a SPOF fails, data flow immediately stops and returns `503 Service Unavailable` or a network timeout to the client.

### When Would I Use It?
- Identifying SPOFs is a critical step in the "Failure Handling" phase of a system design interview.

### When Would I NOT Use It?
- For highly experimental prototype features where cost-saving is more important than uptime.

### Trade-offs
- **What do I gain (by fixing it)?** High availability and fault tolerance.
- **What do I sacrifice?** Infrastructure costs double (e.g., you now pay for two load balancers instead of one) and configuration complexity increases.

### Implementation Idea
- **App Tier SPOF:** Run multiple instances of your Node API behind a Load Balancer.
- **Database SPOF:** Set up a Primary-Replica PostgreSQL architecture with automatic failover (like Amazon RDS Multi-AZ).

### Interview Question
"Looking at this architecture (Client -> App -> DB), identify the single points of failure and explain how to mitigate them."

### How to Answer
**The 'Think' Process:** Scan the architecture from top to bottom. Any box where there is only one of it is a SPOF. Propose a replication/redundancy strategy for each.
**The Answer:** "Currently, both the App server and the Database are single points of failure. If either crashes, the system goes down. To mitigate the App tier SPOF, I would run at least two identical App instances and place a Load Balancer in front of them. To mitigate the DB SPOF, I would provision a Standby Replica in a different availability zone. If the primary DB fails, the system will automatically failover to the standby."

### Follow-up
"If you have two Load Balancers for redundancy, how does the client know which one to talk to?"

### How to Answer (Follow-up)
**The 'Think' Process:** Load Balancers sit behind DNS. DNS can return multiple IPs.
**The Answer:** "This is managed at the DNS level. We can use a service like Amazon Route 53 to point the domain name to multiple Load Balancer IP addresses using DNS Round Robin. Alternatively, we can use Active/Passive failover, where DNS routes traffic to LB 1, but if Route 53 health checks detect LB 1 is down, it automatically updates DNS to route to LB 2."

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
Latency focus (Minimize hops):
Client ───────> Edge CDN Node (Geographically close) ──> Fast Response (20ms)

Throughput focus (Maximize parallelism):
Client ───────> Kafka Queue ──> 50 Worker Nodes ──> Big Database (10k ops/sec)
```

### Data Flow
**Latency Flow:** Optimize for the shortest path. Client -> Cache -> Return.
**Throughput Flow:** Optimize for volume. Client -> Queue -> Batch Process -> DB.

### When Would I Use It?
- Use latency as a metric when discussing user-facing APIs, web page load times, and real-time chat.
- Use throughput when discussing background jobs, video encoding, and analytics pipelines.

### When Would I NOT Use It?
- Do not optimize for extreme low latency in background cron jobs; it's a waste of resources. Focus on throughput instead.

### Trade-offs
- Often you must trade one for the other. For example, batching database writes improves **throughput** (less network overhead per row) but worsens **latency** (requests wait in a buffer for 1 second to be batched before returning).

### Implementation Idea
**Improve Latency:** Add a Redis cache so you don't hit the database, reducing response time from 200ms to 20ms.
**Improve Throughput:** Add a Kafka message queue and horizontally scale consumer workers to process 10,000 tasks per second.

### Interview Question
"What is the difference between latency and throughput? Can you have a system with high latency but high throughput?"

### How to Answer
**The 'Think' Process:** Define both clearly, then provide a real-world example of a high-latency, high-throughput system (like an assembly line or data pipeline).
**The Answer:** "Latency is the time it takes to complete one single request, while throughput is the total volume of requests handled over a period of time. Yes, you can absolutely have high latency and high throughput. For example, a video rendering pipeline might take 10 minutes to render a single video (high latency), but if we spin up 1,000 servers, we can process 1,000 videos simultaneously every 10 minutes, giving us very high throughput."

### Follow-up
"If your API throughput is fine, but p99 latency is spiking to 5 seconds, how would you investigate?"

### How to Answer (Follow-up)
**The 'Think' Process:** p99 latency means 1% of users are having a terrible experience. Mention distributed tracing or slow query logs to find the exact bottleneck.
**The Answer:** "I would use a distributed tracing tool like Jaeger or Datadog APM to see exactly where the time is being spent for those specific slow requests. Usually, p99 spikes are caused by database bottlenecks—such as a missing index causing a slow sequential scan—or by garbage collection pauses in the application runtime. I would check the database slow query logs first."

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
       Requires Load Balancers + Multiple API Nodes + Redis
```

### Data Flow
N/A (This is a mathematical process, not a runtime flow).

### When Would I Use It?
- At the start of every full system design interview, right after clarifying requirements.

### When Would I NOT Use It?
- If the interviewer explicitly says "skip capacity estimation and jump into the architecture." (Always ask if they want you to do the math first).

### Trade-offs
- Keep math simple. Round 1 day to 100,000 seconds (actual is 86,400) to make mental division easy. You trade exact precision for speed.

### Implementation Idea
**Template to memorize:**
1. Monthly Active Users (MAU) -> Daily Active Users (DAU)
2. Writes/sec = (DAU * daily writes) / 100,000
3. Reads/sec = Writes/sec * Read/Write Ratio
4. Storage/day = Writes/day * size of 1 item (e.g. 1KB)

### Interview Question
"Estimate the storage requirements for YouTube over 5 years."

### How to Answer
**The 'Think' Process:** Establish base numbers, make reasonable assumptions about sizes, and do the math out loud.
**The Answer:** "Let's assume YouTube has 1 Billion Daily Active Users. If 1% of users upload 1 video a day, that's 10 million uploads daily. If the average video is 50MB, the daily storage is 10M * 50MB = 500 Terabytes per day. Over 5 years (approx 1,800 days), that equals 1,800 * 500TB = 900,000 TB, or roughly 900 Petabytes of raw storage. Factoring in replication and different resolutions, we are looking at an Exabyte-scale object storage system."

### Follow-up
"Given that most videos are rarely watched, how does this storage estimate impact your caching strategy?"

### How to Answer (Follow-up)
**The 'Think' Process:** Storing Exabytes of data in fast caches is impossible. Mention tiering and the Pareto principle (80/20 rule).
**The Answer:** "Because we cannot cache Exabytes of data, we must rely on the 80/20 rule—80% of traffic comes from 20% of videos (the viral ones). We would only cache the most popular, currently trending videos in the CDN edge nodes. The vast majority of the 'long tail' (rarely watched videos) will sit in cheaper, slower Object Storage (like S3) and will only be fetched on demand."

### Common Mistake
Getting bogged down in exact arithmetic (doing 86,400 division on a whiteboard). Round numbers generously to 100,000.

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
**During a Partition (Node A cannot talk to Node B):**
1. Client writes 'X=5' to Node A. Node A saves it.
2. Another client asks Node B for 'X'.
3. Node B cannot check with Node A. It either returns its old value 'X=2' (Availability) or returns an HTTP 500 error (Consistency).

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

### How to Answer
**The 'Think' Process:** Map traditional SQL to CP, and Cassandra to AP. Explain how they behave during a network split.
**The Answer:** "Traditional relational databases generally favor Consistency (CP). If the primary node cannot replicate to the backup due to a network partition, it will often stop accepting writes to guarantee data integrity. Cassandra, however, is designed as an AP system. It favors Availability. If the network splits, Cassandra nodes will continue accepting writes locally. Once the network heals, they resolve the conflicts (Eventual Consistency), ensuring the system never goes offline for the user."

### Follow-up
"Is it possible to have a CA system?"

### How to Answer (Follow-up)
**The 'Think' Process:** Remember that 'P' (network partitions) are physical realities of the universe. You cannot avoid them.
**The Answer:** "Theoretically yes, but only if you assume the network will *never* fail, which is physically impossible in distributed systems. Networks always drop packets eventually. Therefore, you are forced to accept 'P', and must make the engineering choice between 'C' and 'A'."

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
Client Request (includes all state, e.g., JWT token)
   ↓
Load Balancer routes to ANY API Server randomly
   ↓
API processes request without relying on local memory
   ↓
Database (Persistent State)
```

### When Would I Use It?
- **Stateless:** Almost always for web backend APIs, REST services, and microservices.
- **Stateful:** Real-time multiplayer game servers (tracking player positions in RAM), WebSockets, or databases themselves.

### When Would I NOT Use It?
- Don't use Stateful APIs if you plan to deploy your application to Kubernetes and auto-scale pods dynamically.

### Trade-offs
- **Stateless:** Trivial to horizontally scale. BUT clients must send more data per request (e.g., large JWTs), and processing might take slightly longer.
- **Stateful:** Fast access to local memory. BUT scaling requires complex "sticky sessions" at the load balancer level, and server crashes wipe out active user sessions.

### Implementation Idea
Instead of using `express-session` storing data in Node.js memory, use JWTs (stateless) or store the session ID in a centralized Redis cache (making the API servers stateless, pushing state to the cache tier).

### Interview Question
"Why do we prefer stateless web servers in modern cloud architectures?"

### How to Answer
**The 'Think' Process:** Connect statelessness directly to horizontal scaling and fault tolerance.
**The Answer:** "Stateless web servers are preferred because they enable seamless horizontal scaling and fault tolerance. If a server holds no local state, a load balancer can route a user's request to any available server. If traffic spikes, we can spin up 10 new servers instantly. If a server crashes, we can destroy it without losing any user data. All the actual 'state' is pushed down to a centralized, highly available database or cache layer like Redis."

### Follow-up
"If a web server is entirely stateless, where does the state actually live?"

### How to Answer (Follow-up)
**The 'Think' Process:** State doesn't vanish; it just moves to the edges or the storage layer.
**The Answer:** "The state is offloaded to two places. It can be pushed to the client (for example, embedding user ID and roles inside a JWT that the client sends on every request). Or, it is pushed down to a centralized, stateful data tier, such as a PostgreSQL database or a distributed Redis cache, which all the stateless web servers query on demand."

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
API Server parses URL (Noun) and HTTP Method (Verb)
     ↓
Controller executes business logic
     ↓
Server returns proper HTTP Status Code (e.g., 201 Created) + JSON payload
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

### How to Answer
**The 'Think' Process:** List out the CRUD operations mapping HTTP verbs to resource nouns.
**The Answer:** "I would structure the API around the 'articles' resource. 
- To create a post: `POST /articles` (with a JSON body).
- To retrieve a list of posts: `GET /articles`.
- To retrieve a specific post: `GET /articles/{id}`.
- To completely replace a post: `PUT /articles/{id}`.
- To delete a post: `DELETE /articles/{id}`.
If we had comments, I would nest them: `GET /articles/{id}/comments`."

### Follow-up
"How would you handle updating just the title of an article without overwriting the entire resource?"

### How to Answer (Follow-up)
**The 'Think' Process:** Differentiate between PUT (full replacement) and PATCH (partial update).
**The Answer:** "I would use the `PATCH` HTTP method instead of `PUT`. `PATCH /articles/{id}` allows the client to send a JSON payload containing only the fields that changed, like `{"title": "New Title"}`, and the server will apply a partial update without modifying the existing content or author fields."

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
Request with query parameters -> Controller extracts parameters -> ORM/SQL query built dynamically -> DB executes subset query -> Returns array of 20 items to client.

### When Would I Use It?
- Any `GET` endpoint that returns a list (arrays).

### When Would I NOT Use It?
- Endpoints fetching a single specific resource by ID, or APIs returning small fixed datasets (like a list of 50 US States).

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
"You designed an API returning user posts. As the app grows, the 'GET /posts' endpoint gets extremely slow on deeper pages. How do you fix it?"

### How to Answer
**The 'Think' Process:** Identify the flaw with OFFSET pagination, and propose Cursor pagination as the scalable solution.
**The Answer:** "The slowness is likely caused by Offset pagination. When a user requests page 1000 with `OFFSET 10000`, the database has to scan and discard the first 10,000 rows before returning the data, which is an O(N) operation. I would fix this by migrating the API to Cursor-based (Keyset) pagination. Instead of an offset, the client passes the ID of the last post they saw. The query becomes `WHERE id > last_id LIMIT 20`. Assuming `id` is indexed, the database instantly jumps to the correct row, making it an O(1) operation regardless of how deep the user scrolls."

### Follow-up
"Explain why OFFSET pagination is slow at the database level."

### How to Answer (Follow-up)
**The 'Think' Process:** Explain how the database engine reads disks.
**The Answer:** "At the database level, OFFSET does not magically skip rows. To fulfill `LIMIT 10 OFFSET 100000`, the database engine actually fetches 100,010 rows from the disk, counts through them, throws away the first 100,000, and returns the last 10. This results in massive, unnecessary disk I/O and CPU usage."

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
- Strictly internal APIs between two trusted microservices inside a VPC (though backpressure/circuit breakers are still needed).

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

### How to Answer
**The 'Think' Process:** Mention the architecture (where it lives), the storage (Redis), and the algorithm (Token bucket).
**The Answer:** "I would implement the rate limiter at the API Gateway layer so traffic is blocked before it ever reaches our backend services. Because the system is distributed across multiple gateway servers, I cannot use in-memory counters. I would use a centralized Redis cluster to store the request counts. For the algorithm, I would use the Token Bucket approach—or a Lua script in Redis—using the user's API key or IP address as the Redis key. If the counter exceeds the threshold, the gateway instantly returns an HTTP 429."

### Follow-up
"Why use Redis instead of a relational database for tracking rate limit counters?"

### How to Answer (Follow-up)
**The 'Think' Process:** Compare the read/write speed and disk vs RAM.
**The Answer:** "Rate limiting requires an extremely high volume of read and write operations on every single API request. A relational database writes to disk and requires connection overhead, which would add massive latency and likely crash under the write load. Redis is entirely in-memory and heavily optimized for the atomic `INCR` operations needed for fast, low-latency rate limiting."

### Common Mistake
Implementing rate limiting in application memory when running multiple load-balanced instances. If a user hits Server A 100 times, Server B still thinks they have 0 requests. You must use a centralized store like Redis.

---
*(End of Part 1. I will provide the next set of concepts in the next artifact updates).*
