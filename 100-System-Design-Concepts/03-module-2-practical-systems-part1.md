# MODULE 2 — CONCEPTS + APPLICATIONS 51–100 (PART 1: 51-62)

# REAL-WORLD APPLICATION + INTERVIEW (CORE & QUEUES)

## #51. Design a URL Shortener [Type B — Practical Design]

### What is it?
A service (like bit.ly) that takes a long URL (e.g., `https://amazon.com/dp/B08J5F3G18...`) and generates a short, unique alias (e.g., `bit.ly/3xYz`). When a user visits the short alias, they are redirected to the original long URL.

### Requirements
- Given a long URL, generate a short URL.
- When hitting the short URL, redirect to the long URL.
- Highly available and extremely low latency for redirects.

### Scale Estimation
- 100 million new URLs generated per month (Writes: ~40/sec).
- 1 billion redirects per month (Reads: ~400/sec).
- 10-year storage = 12 billion records. If each is 500 bytes = ~6TB of storage.
- **Read-Heavy System (10:1 ratio).**

### Architecture / Raw Diagram
```text
(1) Create Short URL:
Client ─> [ API Server ] ─> [ Base62 Encoder ] ─> [ PostgreSQL ]

(2) Redirect:
Client ─> [ Load Balancer ] ─> [ API Server ]
                                     │
                             (Check Cache First)
                                     v
                                [ Redis ] ──(Miss)──> [ PostgreSQL ]
```

### Data Flow
**Write:** API generates a unique ID, converts it to Base62 (e.g., `aB3`), saves `{short_id: "aB3", long_url: "http..."}` to DB.
**Read:** API receives `bit.ly/aB3`. Checks Redis for `aB3`. If hit, returns `301 Redirect`. If miss, queries DB, caches it, and returns `301 Redirect`.

### When Would I Use It?
- A classic, foundational system design interview question to test basic DB, Caching, and Hashing knowledge.

### Trade-offs
- **Hash function (MD5) vs Base62 Counter:** Hashing can cause collisions. A centralized counter (e.g., pulling ranges from ZooKeeper or auto-incrementing in a DB) converted to Base62 (A-Z, a-z, 0-9) guarantees uniqueness and is preferred.

### If I had to code an MVP
- **Backend:** Express.js.
- **Database:** PostgreSQL.
- **Logic:** Generate a random 6-character string. If it exists in DB, try again. (Good for MVP, bad for massive scale). Return `res.redirect(301, long_url)`.

### Interview Question
"Design a URL shortener like bit.ly."

### Follow-up 1:
"How do you ensure you don't generate the same short URL twice in a distributed system?" (Answer: Use a centralized counter, like a Redis INCR or a dedicated database ID generator, and convert that unique integer to a Base62 string).

### Common Mistake
Over-complicating the write path with Kafka and Cassandra when the math shows only 40 writes per second, which a basic PostgreSQL database can handle easily.

---

## #52. Design an Authentication Service [Type B — Practical Design]

### What is it?
A centralized microservice responsible for registering users, validating passwords, and issuing tokens (JWTs) so other services can trust the user's identity.

### Requirements
- Users can register and log in.
- System issues a token that other microservices can verify without calling the Auth service.
- Highly secure (no plaintext passwords).

### Scale Estimation
- Login is write-heavy (creates a session/token) but relatively low volume compared to general API traffic.
- Scale depends on MAU, but usually easily handled by a few replicated Auth nodes.

### Architecture / Raw Diagram
```text
           [ Client ]
               │ (POST /login)
               v
       [ API Gateway ]
               │
               v
       [ Auth Service ] ──> [ Auth DB (PostgreSQL) ]
       (Hashes PW,         (Stores User & Hashed PW)
        Generates JWT)
               │
           (Returns JWT)
```

### Data Flow
1. Client sends `{email, password}`.
2. Auth Service looks up email in DB.
3. Compares password using `bcrypt.compare()`.
4. If valid, generates a JWT signed with a private `SECRET_KEY`.
5. Other services (like Billing) only need the `SECRET_KEY` to mathematically verify the JWT; they never talk to the Auth DB.

### When Would I Use It?
- Any microservice architecture where you want to centralize security logic.

### Trade-offs
- **Centralized Auth DB:** High security, but if it goes down, no one can log in. (Existing logged-in users with valid JWTs can still use the system).

### If I had to code an MVP
- Use **NestJS** or **FastAPI**.
- Table: `Users(id, email, password_hash)`.
- Use `bcrypt` for hashing (never MD5 or SHA256 alone; bcrypt has built-in salting and is deliberately slow to prevent brute force).
- Use `jsonwebtoken` to sign the payload.

### Interview Question
"Design the authentication flow for a microservices architecture."

### Follow-up 1:
"How do you handle password resets securely?" (Answer: Generate a random cryptographic token, save it to the DB with a 15-minute expiration, and email a link containing the token to the user).

### Common Mistake
Storing the JWT Secret Key in the codebase. It must be stored in a secure Secrets Manager (like AWS Secrets Manager or HashiCorp Vault) and injected as an environment variable.

---

## #53. Design a Rate Limiter System [Type B — Practical Design]

### What is it?
A system that sits in front of your APIs to limit the number of requests a user or IP can make in a given time frame to prevent abuse.

### Requirements
- Limit requests per user per minute (e.g., 100 req/min).
- Must be distributed (work across 50 API servers).
- Must have extremely low latency (cannot add 50ms to every request).

### Scale Estimation
- Matches your total API request volume. If your system handles 50,000 RPS, the rate limiter must handle 50,000 RPS.
- Needs an ultra-fast in-memory datastore (Redis).

### Architecture / Raw Diagram
```text
Client ─> [ API Gateway ] ─(1) Check Limit─> [ Redis Cluster ]
               │ (2) If < Limit                (Stores Counters)
               v
         [ API Server ]
```

### Data Flow
1. Client sends request with IP or `API_KEY`.
2. Gateway queries Redis: `INCR rate_limit:{API_KEY}`.
3. If value > 100, return `429 Too Many Requests`.
4. If value <= 100, pass to API server.

### When Would I Use It?
- Protecting public endpoints, scraping prevention, and tiered pricing (Free vs Pro tiers).

### Trade-offs
- **Sliding Window Log vs Token Bucket algorithms:**
  - Token Bucket is memory efficient (just stores the bucket capacity and last refill time in Redis).
  - Sliding Window is highly accurate but consumes massive memory (requires storing the exact timestamp of *every* request).

### If I had to code an MVP
- Implement a **Token Bucket** algorithm in Redis using Lua scripts (to ensure the read and update of the bucket happen atomically).

### Interview Question
"Design a distributed rate limiter for a public API."

### Follow-up 1:
"Why use a Lua script in Redis instead of two separate `GET` and `SET` commands from Node.js?" (Answer: To prevent race conditions. If two requests hit simultaneously, both might `GET` a counter of 99, both increment, and both bypass the limit of 100. Lua scripts in Redis run atomically).

### Common Mistake
Recommending a relational database for rate limiting. The write-throughput required for checking and incrementing every single API request will instantly destroy a SQL database.

---

## #54. Design a Notification Service [Type B — Practical Design]

### What is it?
A system responsible for sending Emails, SMS, and Push Notifications to users asynchronously so the main application isn't blocked waiting for email servers.

### Requirements
- Send millions of notifications daily.
- Do not block the main API servers.
- Handle failures (e.g., if the Apple Push server is down).
- Deduplication (don't send the same email twice).

### Scale Estimation
- High throughput, asynchronous.
- Requires message queues to buffer spikes.

### Architecture / Raw Diagram
```text
[ Core API ] ──> (Publishes Event) ──> [ Kafka / RabbitMQ ]
                                            │
                                            v
                                  [ Notification Workers ]
                                   /        |         \
                               [Twilio] [SendGrid] [Apple APNS]
                                (SMS)    (Email)    (Push)
```

### Data Flow
1. Core API (e.g., Order Service) processes an order.
2. Core API drops a message: `{"type": "email", "to": "aditya@...", "body": "Order confirmed"}` into Kafka.
3. API instantly replies to the user.
4. Background Worker pulls message from Kafka, calls SendGrid API.
5. If SendGrid fails, Worker puts message in a Retry Queue.

### When Would I Use It?
- Any system that emails users (password resets, receipts, alerts).

### Trade-offs
- **Async Queue:** Greatly improves API latency. BUT makes debugging harder (if an email doesn't arrive, you have to trace it through the queue logs).

### If I had to code an MVP
- **Queue:** Redis + BullMQ (in Node.js) is easier to set up than Kafka.
- **Workers:** A separate Node.js process listening to BullMQ and using the `nodemailer` library.

### Interview Question
"Design a highly scalable notification system."

### Follow-up 1:
"How do you prevent a user from being spammed with 10 duplicate emails if your worker node crashes mid-processing?" (Answer: Idempotency keys. Store the `notification_id` in Redis or DB; the worker checks it before sending to SendGrid).

### Common Mistake
Sending the email synchronously. e.g., `await sendEmail()` inside the HTTP request handler. Third-party email APIs can take 2-3 seconds to respond, locking up your web server threads.

---

## #55. Design a Logging System [Type B — Practical Design]

### What is it?
A centralized system to collect, store, and search log data from thousands of distributed microservices (e.g., ELK Stack - Elasticsearch, Logstash, Kibana).

### Requirements
- Aggregate logs from all servers.
- Search logs by keyword or request ID.
- Extremely Write-Heavy.

### Scale Estimation
- 1,000 servers generating 100 logs/sec = 100,000 writes/sec.
- Requires heavy write-buffering.

### Architecture / Raw Diagram
```text
[ App Server 1 ] \
[ App Server 2 ] -> [ Log Agent (Fluentd) ] -> [ Kafka ] (Buffer)
[ App Server 3 ] /                                |
                                                  v
                                            [ Logstash ] (Processor)
                                                  |
                                                  v
                                      [ Elasticsearch ] (Search DB)
                                                  |
                                              [ Kibana ] (UI)
```

### Data Flow
1. App Server writes logs to a local file (`/var/log/app.log`).
2. Fluentd agent tails the file and ships logs to Kafka.
3. Kafka buffers the massive data stream.
4. Logstash pulls from Kafka, parses the JSON, and inserts into Elasticsearch.
5. Developer uses Kibana UI to search.

### When Would I Use It?
- Mandatory observability for any distributed system in production.

### Trade-offs
- **Kafka Buffer:** Prevents Elasticsearch from crashing during traffic spikes. BUT adds infrastructure cost.

### If I had to code an MVP
- Use a managed service like **Datadog** or **AWS CloudWatch**. Writing your own ELK stack is usually an anti-pattern for small teams.

### Interview Question
"You have 50 microservices. When an order fails, how do you trace the error across the 5 services involved?"

### Follow-up 1:
"What is a Correlation ID?" (Answer: A unique UUID generated at the API Gateway and passed in the headers to every downstream microservice. Every service includes it in its logs, allowing you to search one ID and see the entire request lifecycle).

### Common Mistake
Writing logs directly to a database from the application code. This creates a massive coupling and network bottleneck. Apps should write to standard output (`stdout`), and an agent should ship them.

---

## #56. Design an Image Processing Pipeline [Type B — Practical Design]

### What is it?
A system that takes user-uploaded images, compresses them, generates thumbnails, and stores them for delivery.

### Requirements
- Support massive file uploads.
- Generate 3 sizes (Thumbnail, Medium, Original).
- Asynchronous processing (don't make the user wait).

### Architecture / Raw Diagram
```text
(1) Direct Upload
Client ──────> [ Amazon S3 (Raw Bucket) ]
                      │
           (2) S3 Trigger (Event)
                      v
             [ AWS Lambda Worker ] (Downloads, Resizes)
                      │
           (3) Upload Resized Images
                      v
          [ Amazon S3 (Processed Bucket) ] ──> [ CDN ]
```

### Data Flow
1. Client uses Pre-signed URL to upload image directly to the "Raw" S3 bucket.
2. S3 emits an `ObjectCreated` event.
3. AWS Lambda function wakes up, downloads the image.
4. Lambda uses ImageMagick to create 3 thumbnails.
5. Lambda uploads them to the "Processed" bucket and updates the Database.

### When Would I Use It?
- Social media apps (Instagram), E-commerce product catalogs.

### Trade-offs
- **Event-Driven (Lambda) vs Polling:** Event-driven scales infinitely and costs nothing when idle. BUT debugging Lambda failures requires good observability tools.

### If I had to code an MVP
- Node.js backend using `multer` to accept the upload, `sharp` to resize it in-memory, and then upload to S3. (Fine for MVP, but risks memory crashes on large files).

### Interview Question
"Design the backend for Instagram's photo upload process."

### Follow-up 1:
"Why use a separate 'Raw' and 'Processed' bucket?" (Answer: Separation of concerns, easier lifecycle policies (e.g., delete raw files after 30 days to save money), and prevents endless loops of S3 events triggering Lambdas).

### Common Mistake
Processing the image synchronously in the main web thread. Image resizing is highly CPU-intensive and will block the Node.js event loop, freezing the server for all other users.

---

# MESSAGE QUEUES + EVENT-DRIVEN SYSTEMS

## #57. Message Queues (Kafka vs RabbitMQ) [Type A — Concept]

### What is it?
Systems that allow applications to communicate asynchronously by sending messages to a queue, where they wait until a consumer is ready to process them.
- **RabbitMQ:** A traditional queue. Messages are pushed, consumed, and then deleted. (Like an email inbox).
- **Kafka:** A distributed, append-only event streaming platform. Messages are kept for a set time even after being consumed, and can be read by many different consumers. (Like a public bulletin board).

### Mental Model
RabbitMQ = A to-do list. Once you finish a task, you cross it out and it's gone.
Kafka = A historian's journal. You write events in order. Anyone can read the journal from the beginning, and reading it doesn't erase it.

### Why does it exist?
To decouple systems. If the Order System needs to tell the Email System to send a receipt, doing it via a Queue means if the Email System is down, the message just waits in the queue safely until it reboots.

### Real-World Example
**Uber:** Uses Kafka to track millions of GPS coordinates. The "Pricing Service" reads the stream to calculate surge pricing. The "Map Service" reads the *exact same stream* to show cars on the map.

### Architecture / Raw Diagram
```text
           [ Producer ] (Order Service)
                 │
                 v
   [ Message Queue / Kafka Topic ] (Buffer)
                 │
                 v
           [ Consumer ] (Email Service)
```

### Data Flow
N/A

### When Would I Use It?
- **RabbitMQ:** Simple task queues (e.g., resizing images, sending emails).
- **Kafka:** Event streaming, analytics pipelines, or architectures where multiple independent services need to react to the same event.

### Trade-offs
- **RabbitMQ:** Easy to use, great routing features. BUT less scalable for massive data streams.
- **Kafka:** Incredible throughput, replayable messages. BUT steep learning curve, requires ZooKeeper/KRaft to manage.

### Implementation Idea
For a basic Node.js app, start with **Redis + BullMQ**. It provides 80% of RabbitMQ's functionality with zero extra infrastructure if you already have Redis.

### Interview Question
"You need to decouple your microservices. When would you choose Kafka over RabbitMQ?"

### Follow-up 1:
"What does it mean to 'replay' events in Kafka?"

### Common Mistake
Defaulting to Kafka for a simple background job queue (like sending welcome emails). Kafka is overkill for simple task queues.

---

## #58. Event-Driven Architecture [Type A — Concept]

### What is it?
An architecture where microservices communicate by producing and consuming *events* (something that happened) rather than making direct synchronous *commands* (telling another service what to do).

### Mental Model
Command-Driven = The boss walking to your desk and saying "Send an email right now, I will wait."
Event-Driven = The boss announcing on a loudspeaker "A new user registered." The Email Team hears it and sends a welcome email. The Analytics Team hears it and updates a graph. The boss goes back to work immediately.

### Why does it exist?
Synchronous HTTP calls (REST) couple systems tightly. If Service A calls B, and B calls C, and C is down, A fails. In Event-Driven, A just announces the event and doesn't care who listens or if they are currently online.

### Real-World Example
**E-Commerce Checkout:** The `CheckoutService` emits `OrderPlaced`. The `InventoryService` listens and reduces stock. The `ShippingService` listens and generates a label. If Shipping is down, it just processes the event when it reboots.

### Architecture / Raw Diagram
```text
[ Checkout Service ] ──> (Publishes: "Order Placed") ──> [ Event Bus (Kafka) ]
                                                              /        \
                                                        (Listens)    (Listens)
                                                           v            v
                                                 [ Inventory Svc ]  [ Shipping Svc ]
```

### Data Flow
N/A

### When Would I Use It?
- Large microservice ecosystems.
- Workflows that don't require an immediate synchronous response to the user.

### When Would I NOT Use It?
- When the user *must* know the result immediately. (e.g., Credit Card Validation must be synchronous).

### Trade-offs
- **What do I gain?** Ultimate decoupling and extreme fault tolerance.
- **What do I sacrifice?** Observability. Tracing a bug through an event-driven system is notoriously difficult because there is no clear sequential stack trace.

### Implementation Idea
Use **AWS EventBridge** or **Apache Kafka** as the central nervous system.

### Interview Question
"What is the difference between Orchestration (Command-driven) and Choreography (Event-driven) in microservices?"

### Follow-up 1:
"How do you handle a scenario where the Inventory Service processes the event, but the Shipping Service throws an error and fails?" (Answer: Sagas / Distributed Transactions / Compensating Events).

### Common Mistake
Using events for queries. Events should represent state changes (`UserCreated`), not requests for data (`GetUserRequest`).

---

## #59. Pub/Sub Pattern [Type E — Implementation Scenario]

### What is it?
Publish-Subscribe. A messaging pattern where senders (Publishers) categorize messages into topics, without knowing who will receive them. Receivers (Subscribers) express interest in topics and only receive those messages.

### Mental Model
A YouTube channel. The creator (Publisher) uploads a video to the channel (Topic). They don't know exactly who will watch it. The users (Subscribers) who clicked 'Subscribe' automatically get a notification in their feed.

### Why does it exist?
To allow a "Fan-out" architecture. One event can trigger 10 different systems simultaneously without the publisher needing to know about those 10 systems.

### Real-World Example
**Live Chat (Discord):** When you send a message, it is published to the `Channel_123` topic. The Pub/Sub system instantly fans out that message to the 5,000 users currently subscribed to that topic.

### Architecture / Raw Diagram
```text
[ Publisher ] ──> [ Topic: "Sports_News" ]
                       /        |        \
                 [ Sub 1 ]  [ Sub 2 ]  [ Sub 3 ]
```

### Data Flow
1. Publisher pushes `{score: "1-0"}` to `TopicX`.
2. Broker duplicates the message for every active Subscriber.
3. Subscribers receive the data asynchronously.

### When Would I Use It?
- WebSockets real-time broadcasting.
- Event-driven microservices.

### When Would I NOT Use It?
- Point-to-point task queues where a task should only be executed by exactly *one* worker. (Pub/Sub sends it to *all* subscribers).

### Trade-offs
- **What do I gain?** Easy addition of new features. (Want to add a fraud detection service? Just have it subscribe to the existing topic; no changes needed in the publisher).
- **What do I sacrifice?** No guarantee of delivery to a specific service.

### Implementation Idea
**Redis Pub/Sub:** Very fast, but fire-and-forget (if a subscriber is offline, they miss the message forever).
**AWS SNS:** Managed, reliable pub/sub.

### Interview Question
"How would you design the backend to push a live score update to 100,000 connected mobile devices simultaneously?"

### Follow-up 1:
"What happens in Redis Pub/Sub if a subscriber momentarily loses network connection when a message is published?"

### Common Mistake
Confusing Pub/Sub with a Task Queue. Task Queue = 1 message goes to 1 worker (Load Balancing). Pub/Sub = 1 message goes to ALL workers (Fan-out).

---

## #60. Dead-Letter Queues (DLQ) [Type C — Debugging Scenario]

### What is it?
A secondary queue where a message system sends messages that cannot be processed successfully after a certain number of retries.

### Mental Model
The "Return to Sender" bin at the post office. If the postman tries to deliver a package 3 times and the address is completely unreadable, they don't keep trying forever—they put it in the DLQ bin for manual inspection.

### Why does it exist?
If a message is malformed (e.g., missing a required JSON field), the consumer will crash, restart, pull the same message, and crash again in an infinite loop ("Poison Pill"). The DLQ prevents this.

### Real-World Example
**Payment Processing:** If an event queue tells a worker to process a refund, but the third-party API is permanently blocking the transaction, the worker retries 3 times, then moves the event to a DLQ so an engineer can manually investigate, allowing the worker to move on to the next refund.

### Architecture / Raw Diagram
```text
[ Queue ] ──> [ Worker ] (Fails 3x)
                 │
                 v
            [ Dead-Letter Queue ] ──> (Alerts Engineer to inspect)
```

### Data Flow
N/A

### When Would I Use It?
- ANY asynchronous queue system processing important data (Payments, Emails, Orders).

### When Would I NOT Use It?
- Temporary metric streams where dropping a bad data point is acceptable.

### Trade-offs
- **What do I gain?** System stability. Unblocks queues from poison pills.
- **What do I sacrifice?** Requires operational overhead (someone must actually monitor and empty the DLQ).

### Implementation Idea
In AWS SQS, you can configure a DLQ simply by setting a `maxReceiveCount = 3` on the main queue and linking it to a secondary DLQ.

### Interview Question
"Your background workers process image uploads. Suddenly, all workers are stuck crashing and restarting, and the queue is backing up. What happened, and how do you fix it structurally?"

### Follow-up 1:
"Once a message is in the DLQ and the developer fixes the bug in the worker code, what happens to the message?" (Answer: It is manually "redriven" or pushed back into the main queue for processing).

### Common Mistake
Setting up a DLQ but never setting up an alert (PagerDuty/Slack) for it. A DLQ full of messages means business processes are silently failing.

---

## #61. Consumer Groups & Partitioning [Type B — Practical Design]

### What is it?
Concepts primarily from Kafka used to scale event consumption.
- **Partitions:** Splitting a single topic into multiple logs to allow parallel writes.
- **Consumer Group:** A group of workers cooperating to read from a topic. Each partition is assigned to exactly *one* worker in the group.

### Mental Model
Topic = A massive stack of 1000 exams to grade.
Partitions = Splitting the stack into 4 piles of 250 exams.
Consumer Group = 4 TAs (workers). Each TA takes exactly 1 pile. They work in parallel, and no exam is graded twice.

### Why does it exist?
To allow extreme parallel processing. A single Node.js worker can only process so many messages per second. Consumer groups allow you to horizontally scale your workers flawlessly.

### Real-World Example
**Kafka Analytics:** A `page_views` topic has 10 partitions. You deploy a Consumer Group of 10 microservice instances. Kafka ensures each instance reads exclusively from one partition, giving you 10x throughput.

### Architecture / Raw Diagram
```text
           [ Topic: Page_Views ]
          /          |          \
    (Part 1)      (Part 2)      (Part 3)
       |             |             |
   [Worker A]    [Worker B]    [Worker C]
    \__________________________________/
          ( Consumer Group 1 )
```

### Data Flow
N/A

### When Would I Use It?
- When designing high-throughput event processing systems (Kafka, Kinesis).

### Trade-offs
- **What do I gain?** Scalability and strict ordering *within* a partition.
- **What do I sacrifice?** You lose global ordering. (Event A in Partition 1 might be processed after Event B in Partition 2, even if A happened first).

### Implementation Idea
When sending messages to Kafka, use a **Partition Key** (e.g., `user_id`). This guarantees that all events for a specific user go to the exact same partition, ensuring they are processed in order by the exact same worker.

### Interview Question
"You have a Kafka topic with 1 million messages per minute. One worker is too slow. How do you scale the processing?"

### Follow-up 1:
"If you have 4 partitions, but you spin up 5 workers in your Consumer Group, what happens to the 5th worker?" (Answer: It sits idle. You cannot have more active workers in a group than you have partitions).

### Common Mistake
Assuming Kafka guarantees global ordering across all partitions. It does not. Ordering is only guaranteed *per partition*.

---

## #62. Exactly-Once Delivery [Type D — Trade-off Scenario]

### What is it?
Message delivery semantics:
- **At-most-once:** Send it. If it fails, forget it. (Data loss possible).
- **At-least-once:** Send it. Keep retrying until acknowledged. (Duplicates possible).
- **Exactly-once:** Guaranteed to process the message exactly one time. (Extremely hard).

### Mental Model
At-most-once = Standard mail (might get lost).
At-least-once = Certified mail (mailman keeps knocking until you sign, might knock twice if he forgot you signed).
Exactly-once = Hand-delivering cash (flawless, but very slow and expensive).

### Why does it exist?
Network failures mean the sender doesn't always know if the receiver got the message. At-least-once is the industry standard, but it means your system *must* handle duplicate messages.

### Real-World Example
**Financial ledgers:** Require Exactly-Once processing to prevent double-charging a user. Kafka provides transaction APIs to achieve this, but it is complex.

### Architecture / Raw Diagram
```text
At-Least-Once Issue:
Queue ─> Worker (Processes payment)
Queue <─ Worker (Network dies before sending ACK)
Queue ─> Worker (Sends same payment again!)
```

### Data Flow
N/A

### When Would I Use It?
- Use **At-least-once + Idempotency** for 99% of backend architectures.

### Trade-offs
- Achieving true Exactly-Once delivery at the infrastructure level requires distributed consensus and massive performance overhead. It is almost always better to use At-least-once delivery and make the consumer *idempotent* (Concept #14).

### Implementation Idea
Instead of fighting for Exactly-Once in the message queue, make the database handle it.
Worker receives `Message_ID_5`. Worker queries DB: `INSERT INTO processed_messages (id) VALUES (5)`. If the DB throws a unique constraint violation, the worker knows it's a duplicate and ignores it.

### Interview Question
"Kafka guarantees at-least-once delivery by default. What architectural problem does this cause for the consumer, and how do you solve it?"

### Follow-up 1:
"Why is At-most-once delivery often acceptable for IoT sensor data (like temperature readings)?"

### Common Mistake
Trying to design an architecture that never produces duplicate events. It is physically impossible in distributed networks. Always design consumers to expect and handle duplicates.

---
*(End of Part 1 for Module 2. Next part will cover intermediate practical systems).*
