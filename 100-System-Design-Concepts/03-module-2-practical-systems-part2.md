# MODULE 2 — CONCEPTS + APPLICATIONS 51–100 (PART 2: 63-75)

# INTERMEDIATE PRACTICAL SYSTEMS

## #63. Design a Chat Application (WhatsApp/Discord) [Type B — Practical Design]

### What is it?
A system allowing users to send real-time text messages to each other (1-on-1 or group chats) with low latency.

### Requirements
- 1-on-1 and Group chats.
- Real-time delivery (<100ms latency).
- Online/Offline presence indicator.
- Message history.

### Scale Estimation
- 50M DAU, 100 messages/user/day = 5 Billion messages/day.
- Extremely write-heavy. Must use NoSQL for message storage (Cassandra/DynamoDB) and WebSockets for real-time delivery.

### Architecture / Raw Diagram
```text
[ Client A ] <--(WebSocket)--> [ Chat Server 1 ]
                                     |
                                [ Redis Pub/Sub ] ──> [ Database (Cassandra) ]
                                     |
[ Client B ] <--(WebSocket)--> [ Chat Server 2 ]
```

### Data Flow
1. Client A sends message to Chat Server 1 via WebSocket.
2. Chat Server 1 saves to Cassandra.
3. Chat Server 1 publishes message to Redis Pub/Sub on channel `user_B_channel`.
4. Chat Server 2 (which holds Client B's WebSocket) is subscribed to `user_B_channel`.
5. Chat Server 2 pushes the message down the WebSocket to Client B.

### When Would I Use It?
- Standard interview question testing WebSockets, Pub/Sub, and NoSQL.

### Trade-offs
- **Stateful vs Stateless:** Chat servers holding WebSockets are stateful (they must track which user holds which socket). Scaling requires a central state store (Redis) to know "User B is connected to Server 2".

### If I had to code an MVP
- Node.js + `Socket.io`.
- Use Redis Adapter for Socket.io to allow multi-server scaling.
- MongoDB or PostgreSQL for storing chat history.

### Interview Question
"Design a 1-on-1 chat application like WhatsApp."

### Follow-up 1:
"How do you handle delivering a message to a user who is currently offline?" (Answer: The server saves it to the DB. When the user comes online, their app sends a sync request via HTTP to fetch all messages since their last 'last_seen_timestamp').

### Common Mistake
Designing it with pure HTTP polling. Polling the database every 1 second from millions of phones will instantly destroy the database and drain mobile batteries.

---

## #64. Design a Feed System (Twitter/Instagram) [Type B — Practical Design]

### What is it?
A system that aggregates posts from people a user follows and displays them in reverse chronological (or algorithmic) order.

### Requirements
- Post a tweet (fast write).
- View timeline (fast read).
- Support celebrities with millions of followers.

### Scale Estimation
- 300M DAU. Read-heavy (100 reads per 1 write).
- Timeline generation is computationally expensive, requires pre-computation (Fan-out).

### Architecture / Raw Diagram
```text
(1) Post Tweet
[ Client ] ─> [ API ] ─> [ DB ]
                 │
                 v (Fan-out on write)
           [ Redis Timeline Cache ]
           (User A)  (User B)  (User C)

(2) View Feed
[ Client ] ─> [ API ] ─> [ Redis Timeline Cache ] (Instantly returns Feed)
```

### Data Flow
**Fan-out on Write (Push Model):**
1. Aditya posts a tweet.
2. The API fetches all of Aditya's followers.
3. The API explicitly inserts the new Tweet ID into every follower's timeline cache in Redis.
4. When a follower opens the app, they just read their pre-computed Redis list. Extremely fast reads.

### When Would I Use It?
- Social media, news aggregators, activity logs.

### Trade-offs
- **Push vs Pull:** Fan-out on write (Push) is amazing for reads but fails for celebrities. If Justin Bieber tweets, writing to 100M Redis lists will crash the cache.
- **Hybrid Approach:** Push for normal users. Pull for celebrities (when you load your feed, it merges your pre-computed list with Justin Bieber's recent tweets dynamically).

### If I had to code an MVP
- Use a SQL DB for Tweets. Query the timeline dynamically on read: `SELECT * FROM tweets WHERE user_id IN (SELECT following_id FROM follows WHERE follower_id = Me) ORDER BY date DESC LIMIT 20`. (This MVP fails at scale but is the correct logical starting point).

### Interview Question
"Design the Twitter timeline architecture."

### Follow-up 1:
"How do you handle the 'Celebrity Problem' in a Fan-out-on-write architecture?"

### Common Mistake
Suggesting the MVP SQL `JOIN` approach for a system at Twitter's scale. The DB will freeze trying to join tables with billions of rows for every page refresh.

---

## #65. Design a Ride-Booking System (Uber) [Type B — Practical Design]

### What is it?
A system matching riders with nearby drivers in real-time.

### Requirements
- Drivers continuously broadcast location.
- Riders can see nearby drivers and request a ride.
- Match rider and driver reliably.

### Scale Estimation
- Highly stateful and write-heavy (millions of drivers updating GPS every 4 seconds).

### Architecture / Raw Diagram
```text
           [ Rider App ]            [ Driver App ]
                 \                        / (Send GPS)
                  v                      v
             [ API Gateway / WebSocket Hub ]
                 /                   \
        [ Matching Svc ]          [ Location Svc ]
              |                          |
        [ DB (PostgreSQL) ]        [ Redis Geo / QuadTree ]
```

### Data Flow
1. Driver sends GPS via WebSocket every 4s.
2. Location Service updates Redis GeoHash.
3. Rider opens app. Requests nearby drivers. Location Svc queries Redis for drivers within 3km.
4. Rider requests ride. Matching Service creates a lock (Redis Mutex) on a Driver.
5. Sends WebSocket push to Driver. If Driver accepts, ride starts.

### When Would I Use It?
- Delivery apps (Doordash), ride-sharing.

### Trade-offs
- **Storing GPS:** Writing GPS data to PostgreSQL every 4 seconds will crash it. GPS data must be held in memory (Redis) for real-time operations, and asynchronously batched into a NoSQL DB (Cassandra) for historical analytics.

### If I had to code an MVP
- Use PostgreSQL with `PostGIS` extension for spatial queries. (Fine for a few thousand drivers).

### Interview Question
"Design a backend system for Uber."

### Follow-up 1:
"How do you efficiently find drivers within a 3km radius without calculating the distance between the rider and EVERY driver in the database?" (Answer: Geohashing or QuadTrees, which convert 2D coordinates into 1D strings/buckets that can be indexed).

### Common Mistake
Overlooking the massive write volume of GPS pings and suggesting a standard relational database as the primary storage for active locations.

---

## #66. Design a Search System (Autocomplete) [Type B — Practical Design]

### What is it?
A system that predicts search queries as the user types (Typeahead).

### Requirements
- Sub-50ms latency.
- Return top 5 suggestions based on historical popularity.

### Scale Estimation
- Extremely read-heavy. (User typing "A..P..P..L..E" triggers 5 API requests).

### Architecture / Raw Diagram
```text
Typing: "sys"
[ Client ] ─> [ API ] ─> [ Redis Trie ] (Returns: "system design", "syslog")

Async Data Gathering:
[ Search Logs ] ─> [ Hadoop/Spark ] ─> [ Trie Builder ] ─> [ Redis ]
```

### Data Flow
1. User types `s`. Frontend debounces (waits 50ms) then sends request.
2. API queries an in-memory Trie data structure (often in Redis or local RAM).
3. Trie returns top 5 cached results for prefix `s`.
4. Background analytics jobs parse daily search logs to update the Trie weights every few hours.

### When Would I Use It?
- Search bars on E-commerce sites, Google search.

### Trade-offs
- **Real-time updates vs Pre-computation:** Updating the autocomplete dictionary in real-time as users search is too expensive. It must be decoupled and processed via batch jobs (Hadoop/Spark) offline.

### If I had to code an MVP
- Use Elasticsearch's built-in Completion Suggester, or store a basic Trie structure in Redis.

### Interview Question
"Design the autocomplete/typeahead system for Google Search."

### Follow-up 1:
"What data structure is fundamentally used for autocomplete?" (Answer: A Trie, or Prefix Tree, optimized by storing the top 5 results at every node to avoid traversing the whole tree on every keystroke).

### Common Mistake
Using SQL `LIKE 'sys%'` for autocomplete. It is far too slow (O(N) scan) for typeahead latency requirements.

---

## #67. Design an E-commerce Checkout/Payment System [Type B — Practical Design]

### What is it?
A system that processes user shopping carts, manages inventory, and interfaces with Payment Gateways (Stripe) securely.

### Requirements
- No double charging.
- No overselling inventory.
- High reliability (ACID transactions).

### Architecture / Raw Diagram
```text
[ Client ]
    │
[ API Server ] ─(1. Lock Inventory)─> [ PostgreSQL ]
    │
(2. Call Stripe)
    │
[ Stripe API ] ──(3. Success)──> [ API Server ]
                                       │
                              (4. Commit Order, Unlock)
                                       v
                                 [ PostgreSQL ]
```

### Data Flow
1. User clicks Pay. API generates an Idempotency Key.
2. DB Transaction starts: `UPDATE inventory SET count = count - 1 WHERE id = X AND count > 0`. (If 0, abort).
3. API calls Stripe with the Idempotency Key.
4. If Stripe succeeds, Commit DB transaction. Emit `OrderSuccess` event to Kafka.
5. If Stripe fails, Rollback DB transaction.

### When Would I Use It?
- Any financial transaction or inventory-bound system.

### Trade-offs
- **Distributed Transactions:** If Inventory and Payments are different microservices, you cannot use a simple SQL transaction. You must use the **Saga Pattern** (compensating transactions) to revert inventory if payment fails.

### If I had to code an MVP
- A single Monolith with PostgreSQL. Use strict `BEGIN; COMMIT; ROLLBACK;` blocks. Use Stripe's SDK.

### Interview Question
"How do you design a checkout system to guarantee you don't sell an item if only 1 is left and 2 users click buy simultaneously?"

### Follow-up 1:
"What happens if your server crashes exactly after Stripe processes the payment, but before you can save the 'Order Confirmed' status to your database?" (Answer: Use Stripe Webhooks to asynchronously reconcile the database state).

### Common Mistake
Calculating prices on the frontend and sending `{"total": 100}` to the backend. The backend must *always* recalculate the price from the secure database to prevent tampering.

---

## #68. Design a Ticket Booking System (BookMyShow) [Type B — Practical Design]

### What is it?
A system for booking highly contested, limited inventory (movie seats, concert tickets).

### Requirements
- Highly concurrent (thousands fighting for 100 seats).
- Hold a seat while a user enters payment info (5 minutes).
- Release seat if payment times out.

### Architecture / Raw Diagram
```text
(1) Select Seat
[ Client ] ─> [ API ] ─> [ Redis (Distributed Lock) ]
                            (Seat 5A locked for 5 mins)
(2) Pay
[ Client ] ─> [ API ] ─> [ Payment Gateway ]
                            (Success)
(3) Confirm
[ API ] ─> [ PostgreSQL ] (Mark booked) ─> [ Redis ] (Unlock)
```

### Data Flow
1. User clicks Seat 5A.
2. API requests a Redis Lock: `SET seat:5A user123 EX 300 NX`.
3. If success, seat is reserved. If failure, tell user "Seat taken".
4. User pays.
5. API writes final booking to Relational DB and releases Redis lock.

### When Would I Use It?
- Flash sales, ticketing, flight booking.

### Trade-offs
- **Database Locks vs Redis Locks:** You could use `SELECT ... FOR UPDATE` in SQL to lock the row. BUT holding a database connection open for a 5-minute payment window will exhaust the connection pool. Redis is better for long-held temporary states.

### If I had to code an MVP
- Use a Redis Mutex (Redlock) for the temporary reservation, and PostgreSQL for the durable booking record.

### Interview Question
"Design a system to sell 100,000 Taylor Swift tickets in 5 minutes without crashing."

### Follow-up 1:
"If you use Redis to lock the seat for 5 minutes, how do you handle the edge case where the user pays at exactly 4:59, but the payment takes 3 seconds, meaning the lock expired before payment completed?"

### Common Mistake
Trying to process all users at once. Real systems use a **Virtual Waiting Room** (Queue) that only lets a manageable batch of users into the booking flow at a time.

---

## #69. Design a Distributed Job Queue [Type B — Practical Design]

### What is it?
A system that accepts long-running tasks from the web server and executes them asynchronously across a pool of worker nodes (e.g., Celery, Sidekiq, BullMQ).

### Requirements
- Execute tasks asynchronously (e.g., generating a 50-page PDF).
- Retry failed tasks.
- Track task status (Pending, Processing, Done).

### Architecture / Raw Diagram
```text
           (POST /generate-report)
[ Client ] ────────> [ Web API ] ──(Returns JobID: 123)
                          │
                   (Pushes Task JSON)
                          v
                    [ Redis / SQS ]
                          │
                  (Pulls Task JSON)
                          v
                   [ Worker Node ] (Generates PDF, uploads to S3)
                          │
                  (Updates Job Status)
                          v
                    [ PostgreSQL ]
```

### Data Flow
1. API receives request, creates a DB record (Status: Pending), pushes Job ID to Queue.
2. Returns Job ID to client immediately.
3. Worker pulls Job ID, updates DB (Status: Processing).
4. Worker finishes task, updates DB (Status: Done, URL: s3...).
5. Client polls API `GET /job/123` to check status.

### When Would I Use It?
- Any API endpoint that takes longer than 1-2 seconds to execute.

### Trade-offs
- **Polling vs WebSockets:** To notify the client it's done, the client can poll every 3 seconds (easy, but wasteful), or the server can push via WebSockets (complex, but instant).

### If I had to code an MVP
- Node.js + BullMQ + Redis.

### Interview Question
"Users are complaining that the 'Export to CSV' button causes the webpage to freeze for 30 seconds and sometimes crash. How do you fix this architecture?"

### Follow-up 1:
"How do you ensure a worker doesn't get stuck processing the same PDF forever if the PDF library freezes?" (Answer: Queue visibility timeouts/heartbeats. If the worker doesn't report progress in X minutes, the queue assumes it died and gives the task to another worker).

### Common Mistake
Storing the actual massive payload (like a 5MB image) inside the Queue message. Queues are for small control messages. Store the image in S3, and pass the S3 URL in the queue message.

---

## #70. Design a Video Streaming Architecture (Netflix) [Type B — Practical Design]

### What is it?
A system that transcodes uploaded videos into multiple formats and streams them flawlessly to millions of users globally.

### Requirements
- Video upload and processing.
- Smooth playback on any device, under any network condition.

### Architecture / Raw Diagram
```text
Upload Flow:
[ Creator ] ─> [ S3 ] ─> [ Transcoding Workers (FFmpeg) ] ─> [ S3 ]

Delivery Flow:
[ Viewer ] ─> [ Open Connect / CDN ] ─> (Streams video chunks)
```

### Data Flow
1. Video uploaded to S3.
2. Event triggers a Job Queue.
3. Workers split the video into 10-second chunks and transcode into 1080p, 720p, 480p.
4. Processed chunks are pushed to global CDNs.
5. Client uses **DASH or HLS** (Adaptive Bitrate Streaming). The client downloads a manifest file, then dynamically requests 720p chunks if WiFi is fast, or 480p if WiFi drops.

### When Would I Use It?
- Streaming platforms, internal training video portals.

### Trade-offs
- **Storage Cost vs Compute Cost:** Storing 10 different resolutions of a video costs massive S3 space, but saves compute because you don't encode on-the-fly. (Storage is cheaper than compute).

### If I had to code an MVP
- Upload to S3. Trigger AWS Elastic Transcoder to generate an HLS manifest (`.m3u8`) and transport stream files (`.ts`). Serve via CloudFront.

### Interview Question
"How does Netflix ensure your video doesn't buffer when your home WiFi suddenly gets slow?"

### Follow-up 1:
"Why are videos split into tiny 10-second chunks instead of just serving one massive 2GB MP4 file?" (Answer: Chunks allow Adaptive Bitrate Streaming—switching quality mid-stream—and distribute caching perfectly across CDN edge nodes).

### Common Mistake
Recommending WebSockets or custom TCP protocols for video streaming. Almost all modern streaming uses standard HTTP (HLS/DASH) because HTTP caches perfectly on existing CDNs.

---

## #71. Design a Leaderboard System (Gaming/Duolingo) [Type B — Practical Design]

### What is it?
A system that tracks points for millions of users and can instantly answer "What is User X's rank?" and "Who are the Top 10?".

### Requirements
- Highly concurrent score updates.
- Real-time ranking lookups.

### Scale Estimation
- Relational databases `ORDER BY score LIMIT 10` is incredibly slow for millions of rows. Requires specialized memory structures.

### Architecture / Raw Diagram
```text
[ Client ] ─(Win Game: +10 pts)─> [ API ]
                                     │
                                     v
                        [ Redis Sorted Set (ZSET) ]
```

### Data Flow
1. User earns points.
2. API calls `ZINCRBY leaderboard_global 10 user_123`.
3. To get Top 10, API calls `ZREVRANGE leaderboard_global 0 9`. (O(log(N)) complexity).
4. To get User Rank, API calls `ZREVRANK leaderboard_global user_123`.

### When Would I Use It?
- Gaming leaderboards, sales dashboards, reddit-style upvote rankings.

### Trade-offs
- **Memory Cost:** Redis stores this in RAM. For 100 million users, this requires gigabytes of RAM. But the performance is unbeatable.

### If I had to code an MVP
- Use a Redis `ZSET` (Sorted Set). It is tailor-made for this exact system design problem.

### Interview Question
"Design a real-time global leaderboard for a mobile game with 10 million daily active players."

### Follow-up 1:
"How do you handle a system crash? Redis is in-memory, so if it restarts, does the leaderboard reset?" (Answer: Redis can be configured with AOF/RDB persistence. Or, strictly write scores to a SQL database as the source of truth, and asynchronously update the Redis cache).

### Common Mistake
Suggesting a SQL database with a B-Tree index on the `score` column. While `LIMIT 10` is fast with an index, finding the exact rank of User X (`SELECT count(*) FROM users WHERE score > X`) requires scanning a massive portion of the index, which is too slow for real-time.

---

## #72. Design a Proximity Service (Yelp/Tinder) [Type B — Practical Design]

### What is it?
A system that can quickly answer "Find all restaurants/users within a 5km radius of my current GPS coordinates."

### Requirements
- Store millions of locations (Lat/Long).
- Query by radius efficiently.

### Architecture / Raw Diagram
```text
[ Client (Lat, Lng) ] ─> [ API ] ─> [ Redis Geo / PostgreSQL PostGIS ]
                                       │
                              (Returns places in radius)
```

### Data Flow
1. Business registers address. Backend converts to Lat/Long and stores in DB.
2. User opens app at (40.71, -74.00).
3. API queries database using spatial indexing.
4. Returns list of businesses.

### When Would I Use It?
- Local search, dating apps, delivery radius tracking.

### Trade-offs
- **Geohashing vs QuadTrees:**
  - Geohash converts 2D coordinates into a 1D string (e.g., `dr5ru`). If two places share a prefix (`dr5`), they are close. Easy to use in any DB.
  - QuadTree recursively divides a map into 4 quadrants. Highly efficient for areas with dense data (cities) vs sparse data (oceans). Harder to implement.

### If I had to code an MVP
- Use PostgreSQL with the **PostGIS** extension. It natively supports `ST_Distance` and bounding box queries perfectly out of the box.

### Interview Question
"Design the backend for Yelp to show restaurants near a user."

### Follow-up 1:
"If you are searching a massive SQL database for restaurants within 5km, why can't you just use standard indexes on the Latitude and Longitude columns?" (Answer: Because you are querying a 2-dimensional space. A standard B-Tree index can only optimize one dimension at a time, resulting in massive table scans).

### Common Mistake
Writing a math formula (Haversine formula) in the API code and calculating the distance for every single restaurant in the database.

---

## #73. Design a Multi-Tenant SaaS Architecture [Type B — Practical Design]

### What is it?
A single software application serving multiple distinct customers (Tenants) while keeping their data completely isolated (e.g., Salesforce, Slack, Shopify).

### Requirements
- Tenant A cannot see Tenant B's data.
- Manage scale if Tenant A is a massive enterprise and Tenant B is a startup.

### Architecture / Raw Diagram
```text
                       [ API Server ]
                             │
     (Tenant ID injected into all DB Queries automatically)
                             v
           [ Shared PostgreSQL Database (Row-level) ]
          Tenant A Data | Tenant B Data | Tenant C Data
```

### Data Flow
1. User logs in. JWT contains `tenant_id: 5`.
2. API middleware intercepts request, attaches `tenant_id` to context.
3. ORM applies a global scope: `WHERE tenant_id = 5` to every single query.

### When Would I Use It?
- B2B Software as a Service.

### Trade-offs
- **Silo vs Pool:**
  - Silo (Database per tenant): Ultimate security, easy backups. BUT incredibly expensive and hard to manage 1000 databases.
  - Pool (Shared Database with `tenant_id` column): Cheap, easy schema migrations. BUT high risk of data leakage if a developer forgets a WHERE clause, and "Noisy Neighbor" problems (Tenant A's heavy query slows down Tenant B).

### If I had to code an MVP
- Use a Pooled database. Add a `tenant_id` column to every table. Use Postgres Row-Level Security (RLS) to enforce isolation at the database engine level so application bugs can't leak data.

### Interview Question
"How do you design a B2B SaaS database to ensure data privacy between different companies?"

### Follow-up 1:
"What is the 'Noisy Neighbor' problem, and how do you mitigate it in a shared database?"

### Common Mistake
Failing to index the `tenant_id` column, meaning a query for a specific tenant's data scans the entire shared database.

---

## #74. Polling vs Webhooks [Type D — Trade-off Scenario]

### What is it?
How two different systems communicate state changes.
- **Polling:** Client asks the Server every 5 minutes, "Is the report done?"
- **Webhooks:** Client tells the Server, "When the report is done, send an HTTP POST to `https://my-api.com/webhook`."

### Mental Model
Polling = Calling the mechanic every hour asking if your car is fixed.
Webhook = Giving the mechanic your phone number so they can call you the second it's ready.

### Why does it exist?
Polling wastes massive amounts of bandwidth and CPU on empty "No, not ready yet" responses. Webhooks are perfectly efficient (Reverse APIs).

### Real-World Example
**Stripe:** When a customer's monthly subscription payment succeeds in the background, Stripe fires a Webhook to your server (`POST /stripe-webhook`) to tell you to update their account.

### Architecture / Raw Diagram
```text
POLLING:
[ Your API ] ─(Is it done?)─> [ Stripe ] (No)
[ Your API ] ─(Is it done?)─> [ Stripe ] (No)

WEBHOOK:
[ Stripe ] ──(Payment Success)──> [ Your API (/webhook) ]
```

### Data Flow
N/A

### When Would I Use It?
- **Webhooks:** Integrating with third-party SaaS (Stripe, GitHub, Twilio) for asynchronous events.
- **Polling:** When integrating with legacy systems that cannot make outbound HTTP requests.

### Trade-offs
- **Webhooks:** Highly efficient, immediate. BUT your webhook endpoint must be highly available and secure (must verify signatures to ensure the webhook actually came from Stripe, not an attacker).

### Implementation Idea
Create an Express endpoint `app.post('/webhook', ...)` that verifies the cryptographic signature in the header, acknowledges receipt immediately with `200 OK`, and processes the event via a background queue.

### Interview Question
"You are integrating with a payment provider. Should you poll their API to check payment status, or use webhooks? Explain why."

### Follow-up 1:
"If your server is down for 5 minutes when the webhook fires, how do you prevent losing that payment event?" (Answer: The provider (Stripe) implements exponential backoff retries for failed webhooks. You can also implement a nightly polling reconciliation job as a fallback).

### Common Mistake
Putting heavy business logic (like generating a PDF) synchronously inside the webhook endpoint. If you don't return `200 OK` within a few seconds, the provider will assume your server is broken and retry the webhook, causing duplicate processing.

---

## #75. Design a Real-Time Collaboration System (Google Docs) [Type B — Practical Design]

### What is it?
A system allowing multiple users to edit the same document simultaneously without overwriting each other's changes.

### Requirements
- Real-time updates via WebSockets.
- Conflict resolution (User A and User B type on the same line at the same millisecond).

### Architecture / Raw Diagram
```text
[ User A ]      [ User B ]
    \              /
     (WebSockets)
          v
  [ Collaboration Server ]
          |
 [ Operational Transformation (OT) Engine ]
          |
    [ Database ]
```

### Data Flow
1. User A types 'H'. Client sends operation to Server.
2. Server receives it, applies it to the master document.
3. Server broadcasts the operation to User B.
4. If A and B type simultaneously, the server uses OT or CRDT algorithms to mathematically merge the keystrokes so both screens look identical.

### When Would I Use It?
- Figma, Google Docs, multiplayer whiteboards.

### Trade-offs
- **OT (Operational Transformation) vs CRDT (Conflict-free Replicated Data Type):** OT requires a central server to dictate the true order of events. CRDTs allow peer-to-peer merging without a central server but consume more memory.

### If I had to code an MVP
- Use WebSockets to broadcast changes. The actual implementation of OT/CRDT is heavily mathematical; rely on existing open-source libraries (like `Yjs` or `ShareDB`) rather than writing the merge algorithm from scratch.

### Interview Question
"How does Google Docs handle the conflict if two users delete the same word at the exact same time?"

### Follow-up 1:
"Why are standard database locks inappropriate for collaborative editing?" (Answer: Locking the document or paragraph prevents real-time collaboration. Optimistic concurrency or OT is required to allow simultaneous typing).

### Common Mistake
Suggesting that the client sends the *entire* document text to the server on every keystroke. Clients send tiny *operations* (e.g., "Insert 'a' at position 5"), not the full state.

---
*(End of Part 2 for Module 2. Next part covers Advanced topics, AI Systems, Security, and Observability).*
