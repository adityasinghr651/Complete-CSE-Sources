# MODULE 2 — CONCEPTS + APPLICATIONS 51–100 (PART 2: 63-75)

# INTERMEDIATE PRACTICAL SYSTEMS

## #63. Design a Chat Application (WhatsApp/Discord) [Type B — Practical Design]

### What is it?
A system allowing users to send real-time text messages to each other (1-on-1 or group chats) with extremely low latency.

### Requirements
- 1-on-1 and Group chats.
- Real-time delivery (<100ms latency).
- Online/Offline presence indicator.
- Message history.

### Architecture / Raw Diagram
```text
[ Client A ] <--(WebSocket)--> [ Chat Server 1 ]
                                     |
                                [ Redis Pub/Sub ] ──> [ Database (Cassandra) ]
                                     |
[ Client B ] <--(WebSocket)--> [ Chat Server 2 ]
```

### Data Flow
1. Client A sends message to Chat Server 1 via open WebSocket.
2. Chat Server 1 validates and saves to a NoSQL DB (Cassandra) for message history.
3. Chat Server 1 publishes message to Redis Pub/Sub on channel `user_B_channel`.
4. Chat Server 2 (which holds Client B's WebSocket) is subscribed to `user_B_channel`.
5. Chat Server 2 receives the message from Redis and pushes it down the WebSocket to Client B.

### When Would I Use It?
- Building real-time messaging, customer support chats, or live game lobbies.

### Trade-offs
- **Stateful vs Stateless:** Chat servers holding WebSockets are stateful (they must track which user holds which socket). Standard stateless horizontal scaling doesn't work out-of-the-box. You *must* use a central state store (Redis Pub/Sub) to connect the isolated chat servers together.

### If I had to code an MVP
- Node.js + `Socket.io`.
- Use the Redis Adapter for Socket.io to allow multi-server scaling (so Server 1 can talk to Server 2).
- MongoDB or PostgreSQL for storing chat history.

### Interview Question
"Design a 1-on-1 chat application like WhatsApp. How do messages get routed between two users connected to different servers?"

### How to Answer
**The 'Think' Process:** WebSockets are stateful. Explain the Pub/Sub backplane.
**The Answer:** "I would use WebSockets for real-time, bi-directional communication. The challenge is that User A might be connected to Server 1, and User B to Server 2. Because WebSockets are stateful, Server 1 doesn't know how to reach User B. To solve this, I would introduce Redis Pub/Sub as a message backplane. When User A sends a message, Server 1 publishes it to a Redis channel dedicated to User B. Server 2, knowing it holds User B's open socket, is subscribed to that channel. It receives the message from Redis and instantly pushes it to User B."

### Follow-up 1:
"How do you handle delivering a message to a user who is currently offline?"

### How to Answer (Follow-up)
**The 'Think' Process:** The DB acts as the source of truth for history.
**The Answer:** "When Server 1 receives the message, it always writes it to a persistent database like Cassandra before pushing it to Redis. If User B is offline, they aren't subscribed to Redis, so the live push is ignored. When User B eventually comes online and opens the app, the client makes a standard HTTP request to fetch all missed messages from the database since their 'last_seen_timestamp'."

### Common Mistake
Designing it with pure HTTP polling. Polling the database every 1 second from millions of phones will instantly destroy the database and drain mobile batteries. You must use WebSockets or SSE.

---

## #64. Design a Feed System (Twitter/Instagram) [Type B — Practical Design]

### What is it?
A system that aggregates posts from people a user follows and displays them in reverse chronological (or algorithmic) order.

### Requirements
- Post a tweet (fast write).
- View timeline (fast read).
- Support celebrities with millions of followers.

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
2. The API fetches all of Aditya's followers from the Graph DB.
3. The API explicitly inserts the new Tweet ID into every follower's pre-computed timeline cache in Redis.
4. When a follower opens the app, they just read their Redis list. Extremely fast reads.

### When Would I Use It?
- Social media, news aggregators, activity logs.

### Trade-offs
- **Push (Fan-out on Write) vs Pull (Fan-out on Read):** Push is amazing for fast reads, but fails for celebrities. If Justin Bieber tweets, writing to 100 million Redis lists will crash the cache. Pull saves writes, but is slow on read (requires DB JOINs). 

### If I had to code an MVP
- Use a SQL DB for Tweets. Query the timeline dynamically on read: `SELECT * FROM tweets WHERE user_id IN (SELECT following_id FROM follows WHERE follower_id = Me) ORDER BY date DESC LIMIT 20`. (This MVP fails at scale but is the logical starting point).

### Interview Question
"Design the Twitter timeline architecture. How do you ensure the timeline loads in under 200ms?"

### How to Answer
**The 'Think' Process:** Don't do heavy SQL JOINs on read. Pre-compute the feeds using the Push model.
**The Answer:** "To achieve sub-200ms reads, we cannot query a massive relational database with JOINs every time a user refreshes their feed. Instead, we use a 'Fan-out on Write' architecture. Every user has a pre-computed timeline stored in an in-memory cache like Redis. When a user posts a tweet, a background worker takes that tweet and explicitly 'pushes' it into the Redis timeline of every person who follows them. When a follower opens the app, we simply return their pre-computed Redis list, making the read operation O(1)."

### Follow-up 1:
"How do you handle the 'Celebrity Problem' in a Fan-out-on-write architecture?"

### How to Answer (Follow-up)
**The 'Think' Process:** You can't push to 100M lists. Use a Hybrid approach.
**The Answer:** "Pushing a celebrity's tweet to 100 million Redis lists would cause a massive spike in write latency and cache exhaustion. To solve this, we use a Hybrid approach. We classify users with millions of followers as 'Celebrities'. When a celebrity tweets, we do NOT push it to their followers. Instead, when a follower loads their feed, the system pulls the follower's standard Redis timeline (Push data), pulls the recent tweets from any Celebrities they follow (Pull data), and merges them together dynamically in memory before returning the result."

### Common Mistake
Suggesting the MVP SQL `JOIN` approach for a system at Twitter's scale. The DB will freeze trying to join tables with billions of rows for every page refresh.

---

## #65. Design a Ride-Booking System (Uber) [Type B — Practical Design]

### What is it?
A system matching riders with nearby drivers in real-time, handling massive amounts of spatial data continuously.

### Requirements
- Drivers continuously broadcast location.
- Riders can see nearby drivers and request a ride.
- Match rider and driver reliably without double-booking.

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
2. Location Service updates the driver's coordinates in a fast in-memory store (Redis GeoHash).
3. Rider opens app. Requests nearby drivers. Location Svc queries Redis for drivers within a 3km radius.
4. Rider requests ride. Matching Service creates a lock (Redis Mutex) on a specific Driver to prevent double-booking.
5. Sends WebSocket push to Driver. If Driver accepts, ride starts and DB is updated.

### When Would I Use It?
- Delivery apps (Doordash), ride-sharing, dating apps.

### Trade-offs
- **Storing GPS in RAM vs Disk:** Writing GPS data to PostgreSQL every 4 seconds will crash it. GPS data must be held in memory (Redis) for real-time radius queries, and asynchronously batched into a NoSQL DB (Cassandra) for historical analytics and billing.

### If I had to code an MVP
- Use PostgreSQL with the `PostGIS` extension for spatial queries (perfect for a few thousand drivers, but chokes at global scale).

### Interview Question
"Design a backend system for Uber. How do you efficiently store and query the live locations of 1 million active drivers?"

### How to Answer
**The 'Think' Process:** Mention fast writes (Redis) and spatial indexing (Geohashing).
**The Answer:** "Because drivers update their location every 4 seconds, writing directly to a standard relational database would instantly exhaust its write capacity. I would use an in-memory datastore like Redis to hold the active locations. To query efficiently, we cannot scan every driver; we must use spatial indexing. Redis supports GeoHashes out of the box, which converts 2D coordinates into 1D strings. This allows us to instantly query all drivers within a 3km radius of the rider's coordinates entirely in RAM."

### Follow-up 1:
"If two riders request the exact same driver at the same millisecond, how do you prevent the driver from being double-booked?"

### How to Answer (Follow-up)
**The 'Think' Process:** Distributed locking.
**The Answer:** "We must use a distributed locking mechanism. When the Matching Service selects Driver A for Rider 1, it attempts to acquire a Mutex lock in Redis (e.g., using Redlock) with the Driver's ID as the key. If it gets the lock, it sends the request to the driver. If the thread processing Rider 2's request tries to acquire the same lock, it will fail, and the system will immediately fallback to finding the next best driver for Rider 2."

---

## #66. Design a Search System (Autocomplete) [Type B — Practical Design]

### What is it?
A system that predicts search queries as the user types (Typeahead), requiring absolute minimal latency.

### Requirements
- Sub-50ms latency.
- Return top 5 suggestions based on historical popularity.

### Architecture / Raw Diagram
```text
Typing: "sys"
[ Client ] ─> [ API ] ─> [ Redis Trie ] (Returns: "system design", "syslog")

Async Data Gathering:
[ Search Logs ] ─> [ Hadoop/Spark ] ─> [ Trie Builder ] ─> [ Redis ]
```

### Data Flow
1. User types `s`. Frontend debounces (waits 50ms) then sends request.
2. API queries an in-memory Trie data structure (often stored in Redis or custom C++ microservice).
3. Trie returns top 5 cached results for prefix `s`.
4. Background analytics jobs parse daily search logs to update the Trie weights every few hours.

### When Would I Use It?
- Search bars on E-commerce sites, Google search.

### Trade-offs
- **Real-time updates vs Pre-computation:** Updating the autocomplete dictionary in real-time as users search is too computationally expensive. It must be decoupled. The Trie is pre-computed offline via batch jobs (Hadoop/Spark) and updated in Redis periodically.

### If I had to code an MVP
- Use Elasticsearch's built-in Completion Suggester, or store a basic Trie structure in Redis.

### Interview Question
"Design the autocomplete/typeahead system for Google Search. What underlying data structure makes this extremely fast?"

### How to Answer
**The 'Think' Process:** Name the Trie (Prefix Tree) and explain how it caches top results.
**The Answer:** "Autocomplete requires sub-50ms latency, which rules out SQL `LIKE` queries. The fundamental data structure used is a Trie, or Prefix Tree, kept entirely in memory. As the user types 'A-P-P', the API traverses down the 'A', 'P', 'P' nodes of the tree. To ensure we don't have to traverse the entire subtree to find the most popular words starting with 'APP', we heavily optimize the Trie by storing the Top 5 most popular search terms at every single node. This makes retrieving the suggestions an O(1) operation once the prefix node is reached."

### Follow-up 1:
"If a new major news event happens, but your background Hadoop jobs only update the Trie once a day, how do you inject the new trending search term into the autocomplete instantly?"

### How to Answer (Follow-up)
**The 'Think' Process:** Real-time stream processing bypassing the batch job.
**The Answer:** "We would need a secondary, real-time streaming pipeline. We could pipe live search queries through Apache Kafka and process them with Apache Flink over a 5-minute sliding window. If a new term suddenly spikes in frequency, the stream processor bypasses the Hadoop batch job and injects the new term directly into a fast, temporary 'Trending' cache in Redis. The API merges suggestions from the main Trie and this Trending cache before returning them to the user."

---

## #67. Design an E-commerce Checkout/Payment System [Type B — Practical Design]

### What is it?
A system that processes user shopping carts, manages inventory, and interfaces with Payment Gateways (Stripe) securely without overselling.

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
1. User clicks Pay. API generates a UUID (Idempotency Key).
2. DB Transaction starts: `UPDATE inventory SET count = count - 1 WHERE id = X AND count > 0`. (If 0, abort).
3. API calls Stripe with the UUID.
4. If Stripe succeeds, Commit DB transaction. Emit `OrderSuccess` event to Kafka.
5. If Stripe fails, Rollback DB transaction (restoring the inventory count).

### When Would I Use It?
- Any financial transaction or inventory-bound system.

### Trade-offs
- **Distributed Transactions (Saga):** If Inventory and Payments are strictly separated into two different microservices with their own databases, you cannot use a simple SQL transaction. You must use the **Saga Pattern** (compensating transactions) to revert inventory via an API call if the payment service fails.

### If I had to code an MVP
- A single Monolith with PostgreSQL. Use strict `BEGIN; COMMIT; ROLLBACK;` blocks. Use Stripe's official SDK.

### Interview Question
"How do you design a checkout system to guarantee you don't sell an item if only 1 is left and 2 users click buy simultaneously?"

### How to Answer
**The 'Think' Process:** Prevent race conditions using database locks or atomic updates.
**The Answer:** "To prevent overselling, we must rely on the ACID properties of a relational database like PostgreSQL. When both users click buy, we cannot read the count, check it in Node.js, and then update it, because that causes a race condition. Instead, we use an atomic update query: `UPDATE inventory SET count = count - 1 WHERE id = 123 AND count > 0 RETURNING count`. The database inherently locks the row during the update. The first transaction succeeds and decrements the count to 0. The second transaction attempts the update, but because the `WHERE count > 0` condition fails, it updates 0 rows, and we safely return an 'Out of Stock' error to the second user."

### Follow-up 1:
"What happens if your server crashes exactly after Stripe processes the payment successfully, but before you can save the 'Order Confirmed' status to your database?"

### How to Answer (Follow-up)
**The 'Think' Process:** Asynchronous reconciliation via Webhooks.
**The Answer:** "This is a critical failure state where the user is charged but gets no product. To mitigate this, we must rely on Webhooks. We configure Stripe to send a webhook (`POST /webhook/stripe-success`) to our server whenever a payment clears. Even if our server crashed during the initial checkout request, when it reboots, it will receive the webhook from Stripe. The webhook endpoint will look up the transaction via its Idempotency Key, confirm the order in the database, and send the user their receipt, ensuring eventual consistency."

---

## #68. Design a Ticket Booking System (BookMyShow) [Type B — Practical Design]

### What is it?
A system for booking highly contested, limited inventory (movie seats, concert tickets) with temporary holds.

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
5. API writes final booking to Relational DB and manually releases the Redis lock.
6. If the user never pays, Redis automatically deletes the lock after 5 minutes (300 seconds), freeing the seat for others.

### When Would I Use It?
- Flash sales, ticketing, flight booking, limited edition drops.

### Trade-offs
- **Database Locks vs Redis Locks:** You could use `SELECT ... FOR UPDATE` in PostgreSQL to lock the row. BUT holding a database connection/transaction open for a 5-minute payment window will exhaust the connection pool instantly under load. Redis locks are fast and stateless, perfect for temporary holds.

### If I had to code an MVP
- Use a Redis Mutex for the temporary reservation, and PostgreSQL for the durable, final booking record.

### Interview Question
"Design a system to sell 100,000 Taylor Swift tickets. How do you allow a user to hold a specific seat for 5 minutes to enter their credit card without locking up your database?"

### How to Answer
**The 'Think' Process:** Database locks are bad for long holds. Use Redis expiration keys.
**The Answer:** "Holding a SQL database transaction open for 5 minutes while a user types their credit card would exhaust the connection pool and crash the database. Instead, I would decouple the temporary hold from the persistent database using Redis. When a user selects a seat, the API attempts to set a key in Redis like `seat_123_lock` with an expiration time (TTL) of 5 minutes. If successful, the seat is held. When the user successfully pays, we write the permanent ticket to PostgreSQL and clear the Redis lock. If they close the browser and don't pay, Redis automatically deletes the key after 5 minutes, returning the seat to the available pool."

### Follow-up 1:
"If you use Redis to lock the seat for 5 minutes, how do you handle the edge case where the user pays at exactly 4:59, but the payment processing takes 3 seconds, meaning the Redis lock expired before payment completed?"

### How to Answer (Follow-up)
**The 'Think' Process:** Grace periods or optimistic locking.
**The Answer:** "This introduces a race condition where the seat might be stolen during payment. To solve this, the frontend UI timer should expire at 4:30, forcing the user to submit early, while the actual Redis lock is set for 5:00, creating a safe buffer. Additionally, before executing the final charge against the credit card, the backend must do a final check to verify the Redis lock is still owned by that specific user. If it expired, the backend rejects the payment and informs the user."

---

## #69. Design a Distributed Job Queue [Type B — Practical Design]

### What is it?
A system that accepts long-running tasks from the web server and executes them asynchronously across a pool of worker nodes (e.g., Celery, Sidekiq, BullMQ).

### Requirements
- Execute tasks asynchronously (e.g., generating a massive PDF).
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
4. Worker finishes task, uploads result to S3, updates DB (Status: Done, URL: s3...).
5. Client polls API `GET /job/123` to check status, or receives a WebSocket push.

### When Would I Use It?
- Any API endpoint that takes longer than 1-2 seconds to execute.

### Trade-offs
- **Polling vs WebSockets:** To notify the client it's done, the client can poll every 3 seconds (easy, but wasteful), or the server can push via WebSockets (complex, but instant and efficient).

### If I had to code an MVP
- Node.js + BullMQ (which uses Redis under the hood). Keep payloads small.

### Interview Question
"Users are complaining that the 'Export to CSV' button causes the webpage to freeze for 30 seconds and sometimes crash with a 504 Timeout. How do you fix this architecture?"

### How to Answer
**The 'Think' Process:** The task is synchronous and blocking. Make it asynchronous using a Job Queue.
**The Answer:** "The system is currently trying to generate the CSV synchronously. The HTTP connection is held open, and if it takes longer than the load balancer's timeout limit (usually 30 seconds), it drops the connection. I would fix this by implementing a Distributed Job Queue. When the user clicks Export, the API instantly pushes a job payload to a queue (like RabbitMQ) and returns an HTTP 202 Accepted with a Job ID. The browser immediately unfreezes. A background worker picks up the job, generates the CSV, and saves it to S3. The frontend can then poll the server with the Job ID to get the download link when it's ready."

### Follow-up 1:
"How do you ensure a worker doesn't get stuck processing the same CSV forever if the CSV library freezes?"

### How to Answer (Follow-up)
**The 'Think' Process:** Queues use Visibility Timeouts. Workers should send heartbeats.
**The Answer:** "This is mitigated using Queue Visibility Timeouts and Heartbeats. When a worker pulls a job, it is given a timeout (e.g., 5 minutes) to complete it. If the worker's thread freezes due to a bad library, it stops sending heartbeats back to the queue. Once the 5-minute timeout is reached, the queue assumes the worker died, un-hides the message, and allows another healthy worker to pull it and try again."

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
- **Storage Cost vs Compute Cost:** Storing 10 different resolutions of a video costs massive S3 space, but saves compute because you don't encode on-the-fly. Because storage (S3) is vastly cheaper than GPU compute, pre-transcoding into multiple formats is always the correct trade-off.

### If I had to code an MVP
- Upload to S3. Trigger AWS Elastic Transcoder to generate an HLS manifest (`.m3u8`) and transport stream files (`.ts`). Serve via CloudFront.

### Interview Question
"How does Netflix ensure your video doesn't buffer when your home WiFi suddenly gets slow?"

### How to Answer
**The 'Think' Process:** Don't send one massive file. Send chunks using HLS/DASH (Adaptive Bitrate).
**The Answer:** "Netflix doesn't stream a single massive MP4 file. They use Adaptive Bitrate Streaming protocols like HLS or DASH. During the backend ingestion process, the video is split into tiny 5-to-10 second chunks, and each chunk is transcoded into multiple resolutions (4K, 1080p, 480p). The client device downloads a manifest file listing all these chunks. As the video plays, the client constantly measures the user's WiFi speed. If the bandwidth drops, the client simply requests the next 10-second chunk in 480p instead of 1080p, lowering the quality to prevent buffering."

### Follow-up 1:
"Why are videos split into tiny chunks instead of just serving one massive file and limiting the download speed?"

### How to Answer (Follow-up)
**The 'Think' Process:** Caching efficiency on CDNs.
**The Answer:** "Aside from allowing Adaptive Bitrate Switching, splitting into chunks makes caching infinitely more efficient. Content Delivery Networks (CDNs) are optimized for caching and serving small, discrete HTTP files. If a user stops watching a movie halfway through, the CDN hasn't wasted bandwidth trying to download the entire 2GB file. Furthermore, if a specific chunk is highly popular (like a jump scare scene that everyone rewinds to), that specific tiny chunk is heavily cached at the edge node."

---

## #71. Design a Leaderboard System (Gaming/Duolingo) [Type B — Practical Design]

### What is it?
A system that tracks points for millions of users and can instantly answer "What is User X's rank?" and "Who are the Top 10?".

### Requirements
- Highly concurrent score updates.
- Real-time ranking lookups.

### Architecture / Raw Diagram
```text
[ Client ] ─(Win Game: +10 pts)─> [ API ]
                                     │
                                     v
                        [ Redis Sorted Set (ZSET) ]
```

### Data Flow
1. User earns points in game.
2. API calls `ZINCRBY leaderboard_global 10 user_123`.
3. To get Top 10, API calls `ZREVRANGE leaderboard_global 0 9`. (O(log(N)) complexity).
4. To get User Rank, API calls `ZREVRANK leaderboard_global user_123`.

### When Would I Use It?
- Gaming leaderboards, sales dashboards, reddit-style upvote rankings.

### Trade-offs
- **Memory Cost vs SQL Slowness:** Redis stores this structure entirely in RAM. For 100 million users, this requires gigabytes of expensive RAM. However, using a SQL database with `ORDER BY score` requires scanning massive indexes for exact rankings, which is too slow for real-time leaderboards.

### If I had to code an MVP
- Use a Redis `ZSET` (Sorted Set). It is a specialized data structure tailor-made for this exact system design problem.

### Interview Question
"Design a real-time global leaderboard for a mobile game with 10 million daily active players."

### How to Answer
**The 'Think' Process:** SQL fails at exact rank lookups. Propose Redis Sorted Sets.
**The Answer:** "Using a standard relational database is a poor choice here. While finding the top 10 players is fast with an index (`LIMIT 10`), finding the exact rank of player #5,432,109 requires the database to count every row with a higher score, which is incredibly slow for real-time. Instead, I would use Redis, specifically the Sorted Set (ZSET) data structure. It keeps elements continuously sorted in memory using a Skip List. Updating a score, fetching the top 10, or fetching the exact rank of any specific user are all logarithmic O(log N) operations, making it lightning fast even for 10 million users."

### Follow-up 1:
"How do you handle a system crash? Redis is in-memory, so if it restarts, does the leaderboard reset?"

### How to Answer (Follow-up)
**The 'Think' Process:** Redis persistence, or using SQL as the durable source of truth.
**The Answer:** "To prevent data loss, we have two options. First, we can configure Redis persistence using AOF (Append Only File) to save state to disk. Alternatively, a more robust architecture is to use a SQL database as the ultimate source of truth. When a user scores points, we write the durable record to SQL, and simultaneously push an asynchronous update to the Redis Sorted Set. If Redis crashes and wipes, we can write a script to rebuild the entire ZSET from the SQL database on boot."

---

## #72. Design a Proximity Service (Yelp/Tinder) [Type B — Practical Design]

### What is it?
A system that can quickly answer "Find all restaurants/users within a 5km radius of my current GPS coordinates."

### Requirements
- Store millions of locations (Lat/Long).
- Query by radius efficiently (sub-second).

### Architecture / Raw Diagram
```text
[ Client (Lat, Lng) ] ─> [ API ] ─> [ Redis Geo / PostgreSQL PostGIS ]
                                       │
                              (Returns places in radius)
```

### Data Flow
1. Business registers address. Backend converts to Lat/Long and stores in DB.
2. User opens app at (40.71, -74.00).
3. API queries database using spatial indexing (e.g., `ST_DWithin`).
4. Returns list of businesses.

### When Would I Use It?
- Local search, dating apps, delivery radius tracking.

### Trade-offs
- **Geohashing vs QuadTrees:**
  - **Geohash** converts 2D coordinates into a 1D string (e.g., `dr5ru`). If two places share a prefix (`dr5`), they are close. Very easy to implement in standard databases.
  - **QuadTree** recursively divides a map into 4 quadrants. Highly efficient for areas with dense data (cities) vs sparse data (oceans), but harder to build and maintain dynamically.

### If I had to code an MVP
- Use PostgreSQL with the **PostGIS** extension. It natively supports `ST_Distance` and bounding box queries perfectly out of the box using R-Tree indexes.

### Interview Question
"Design the backend for Yelp to show restaurants near a user. How do you find nearby locations without calculating the distance to every single restaurant in the database?"

### How to Answer
**The 'Think' Process:** Brute force math (Haversine) is O(N). Spatial indexing is required.
**The Answer:** "Calculating the distance between the user and every restaurant in the database using the Haversine formula requires a full table scan (O(N)), which is far too slow. Instead, we must use Spatial Indexing. I would use PostgreSQL with the PostGIS extension, or a Geohashing strategy. Geohashing converts 2D latitude and longitude into a 1D string. Locations that are physically close share the same string prefix. We can index this string using a standard B-Tree, allowing the database to instantly filter down to restaurants in the user's specific grid before calculating the exact math, making it highly scalable."

### Follow-up 1:
"If you are searching a massive SQL database for restaurants within 5km, why can't you just use standard indexes on the Latitude and Longitude columns?"

### How to Answer (Follow-up)
**The 'Think' Process:** B-Trees are 1-dimensional. Maps are 2-dimensional.
**The Answer:** "Standard B-Tree indexes are one-dimensional. If you query `WHERE lat BETWEEN X AND Y AND lng BETWEEN A AND B`, the database can use the index on Latitude to find all restaurants in a horizontal slice of the earth. But to filter that slice by Longitude, it has to scan through all those results manually. It cannot use both indexes efficiently for a bounding box. Specialized spatial indexes (like R-Trees used in PostGIS) are designed to index two-dimensional bounding boxes efficiently."

---

## #73. Design a Multi-Tenant SaaS Architecture [Type B — Practical Design]

### What is it?
A single software application serving multiple distinct customers (Tenants) while keeping their data completely isolated (e.g., Salesforce, Slack, Shopify).

### Requirements
- Tenant A cannot see Tenant B's data under any circumstance.
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
- B2B Software as a Service (SaaS).

### Trade-offs
- **Silo vs Pool:**
  - **Silo (Database per tenant):** Ultimate security, easy backups. BUT incredibly expensive and hard to manage schema migrations across 1,000 databases.
  - **Pool (Shared Database with `tenant_id` column):** Cheap, easy schema migrations. BUT high risk of data leakage if a developer forgets a `WHERE` clause, and "Noisy Neighbor" problems (Tenant A's heavy query slows down Tenant B).

### If I had to code an MVP
- Use a Pooled database. Add a `tenant_id` column to every table. Use Postgres **Row-Level Security (RLS)** to enforce isolation at the database engine level so application bugs can't leak data.

### Interview Question
"How do you design a B2B SaaS database to ensure data privacy between different companies while keeping infrastructure costs low?"

### How to Answer
**The 'Think' Process:** Propose the Pooled model for cost, but address the security risk using engine-level isolation.
**The Answer:** "To keep costs low, I would use a Pooled architecture, where all tenants share the same physical database, distinguished by a `tenant_id` column on every table. The massive risk here is a developer forgetting to add `WHERE tenant_id = X`, exposing Company A's data to Company B. To prevent this, I wouldn't rely on application code. I would implement Row-Level Security (RLS) directly in PostgreSQL. The application passes the authenticated tenant's ID to the database session, and the database engine physically prevents the query from accessing any rows that don't belong to that tenant, guaranteeing absolute privacy."

### Follow-up 1:
"What is the 'Noisy Neighbor' problem, and how do you mitigate it in a shared database?"

### How to Answer (Follow-up)
**The 'Think' Process:** Resource exhaustion by one tenant affecting others.
**The Answer:** "The Noisy Neighbor problem occurs when one massive tenant runs a heavy analytics query that hogs the shared database's CPU, causing latency for all other smaller tenants. To mitigate this, we can implement rate limiting per tenant at the API Gateway. At the database level, if a specific enterprise tenant grows too large, we would use a Hybrid model—migrating that specific massive tenant out of the shared pool and into their own dedicated Database Silo, protecting the rest of the pool."

---

## #74. Polling vs Webhooks [Type D — Trade-off Scenario]
*(This is deeply integrated into practical event architectures, previously covered in Concept 15, but applied specifically to integrations).*

### What is it?
How two different systems communicate state changes across the internet.
- **Polling:** Client asks the Server every 5 minutes, "Is the report done?"
- **Webhooks:** Client tells the Server, "When the report is done, send an HTTP POST to `https://my-api.com/webhook`."

### Architecture / Raw Diagram
```text
POLLING:
[ Your API ] ─(Is it done?)─> [ Stripe ] (No)
[ Your API ] ─(Is it done?)─> [ Stripe ] (No)

WEBHOOK:
[ Stripe ] ──(Payment Success)──> [ Your API (/webhook) ]
```

### When Would I Use It?
- **Webhooks:** Integrating with third-party SaaS (Stripe, GitHub, Twilio) for asynchronous events.

### Interview Question
"You are integrating with a payment provider. Should you schedule a cron job to poll their API to check payment statuses, or use webhooks?"

### How to Answer
**The 'Think' Process:** Focus on efficiency and real-time response.
**The Answer:** "I would strongly prefer Webhooks. Polling a payment provider every minute generates massive amounts of wasteful HTTP requests, 99% of which return 'No change'. Furthermore, it introduces artificial latency; if a payment clears at 12:01, and our cron polls at 12:05, the user waits 4 minutes. With a Webhook, the provider makes an outbound POST to our server the exact millisecond the payment clears, which is perfectly efficient and provides a real-time UX."

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
4. If A and B type simultaneously, the server uses OT or CRDT algorithms to mathematically merge the keystrokes so both screens look identical without locking the document.

### When Would I Use It?
- Figma, Google Docs, multiplayer whiteboards.

### Trade-offs
- **OT (Operational Transformation) vs CRDT (Conflict-free Replicated Data Type):** 
  - OT requires a central server to dictate the true chronological order of events. Hard to scale, but well-understood.
  - CRDTs allow peer-to-peer merging without needing a central server to dictate order, but consume more memory and are mathematically complex.

### If I had to code an MVP
- Use WebSockets to broadcast changes. The actual implementation of OT/CRDT is heavily mathematical; rely on existing open-source libraries (like `Yjs` or `ShareDB`) rather than writing the merge algorithm from scratch.

### Interview Question
"How does Google Docs handle the conflict if two users edit the exact same word at the exact same millisecond?"

### How to Answer
**The 'Think' Process:** Standard DB locks are impossible here. Propose OT or CRDT.
**The Answer:** "Standard database locks cannot be used because they would freeze the document and prevent real-time collaboration. Instead, systems like Google Docs use an algorithm called Operational Transformation (OT) or Conflict-free Replicated Data Types (CRDT). Instead of sending the entire document text to the server, clients send tiny operations (e.g., 'Insert character A at index 5'). The central server acts as the source of truth, receives these concurrent operations, mathematically transforms the indexes so they don't overwrite each other, and broadcasts the resolved operations back to all clients so everyone's screen matches perfectly."

### Follow-up 1:
"Why not just have the clients send the entire contents of the text box to the server every time a key is pressed?"

### How to Answer (Follow-up)
**The 'Think' Process:** Bandwidth and overwriting logic.
**The Answer:** "Sending the entire document state on every keystroke would consume massive amounts of bandwidth for large documents. More importantly, it makes conflict resolution impossible. If User A sends the whole document with 'Hello', and User B sends the whole document with 'World', the server only has the choice to overwrite A with B or vice versa. By sending discrete operations (insertions/deletions at specific indexes), the server can merge them to result in 'Hello World'."

---
*(End of Part 2 for Module 2. Next part covers Advanced topics, AI Systems, Security, and Observability).*
