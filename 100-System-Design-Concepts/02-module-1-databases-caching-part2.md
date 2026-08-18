# MODULE 1 — CONCEPTS 1–50 (PART 4: 39-50)

## #39. Distributed Cache (Redis) [Type A — Concept]

### What is it?
A caching system that pools the RAM of multiple networked servers together, creating a massive, highly available in-memory data store. **Redis** and **Memcached** are the most common implementations.

### Mental Model
Instead of relying on the limited short-term memory of one genius (local memory on a single API server), you have a room of 100 people sharing information instantly via headsets. If one person forgets, another remembers.

### Why does it exist?
Local memory caching (like saving to a variable in Node.js) doesn't scale. If you have 5 API servers behind a load balancer, and Server A caches a database query, Server B doesn't know about it. A distributed cache sits behind the servers, acting as a shared brain for all of them.

### Real-World Example
**Twitter:** Uses a massive Redis cluster to cache the social graph. When you load a profile, the API servers don't calculate your follower count from the database; they fetch it instantly from the distributed Redis cluster.

### Architecture / Raw Diagram
```text
           [ API 1 ]
         /           \
[Client]-  [ API 2 ] -- [ Distributed Redis Cluster ]
         \           /
           [ API 3 ]
```

### Data Flow
1. Request hits API 2.
2. API 2 makes a network request to the Redis Cluster for a key.
3. Redis returns the value in <1ms.
4. API 2 responds to client.

### When Would I Use It?
- Any application scaled beyond a single API server that needs caching, rate limiting, or session storage.

### When Would I NOT Use It?
- Single-server MVP applications where local memory (like an LRU Cache library) is perfectly fine and avoids network hops.

### Trade-offs
- **What do I gain?** Shared state across stateless microservices and incredible read throughput.
- **What do I sacrifice?** Operational complexity. You now have another piece of infrastructure to monitor, scale, and secure.

### Implementation Idea
Use **Amazon ElastiCache** or **Redis Cloud**. In code, use a Redis client library instead of a standard local variable map.

### Interview Question
"Why shouldn't you just use in-memory maps (`const cache = {}`) to cache database queries in a production Node.js application?"

### Follow-up
"If a distributed cache goes down entirely, what happens to your application?" (Answer: All requests fall back to the database, causing a massive traffic spike that will likely crash the database).

### Common Mistake
Treating Redis like a durable database. While Redis *can* persist data to disk, it is primarily volatile RAM. Do not use it as the sole source of truth for critical data like financial records.

---

## #40. Hot Keys [Type C — Debugging Scenario]

### What is it?
A failure mode in distributed caching/databases where a single piece of data (a key) becomes so popular that it overwhelms the specific server (node) responsible for storing it, while all other servers sit idle.

### Mental Model
A grocery store has 10 checkout lanes (Servers). But 99% of customers are trying to buy one specific limited-edition item (Hot Key) located only in Lane 1. Lane 1 is crushed, while Lanes 2-10 are empty.

### Why does it exist?
Distributed systems route traffic using a hashing algorithm based on the key (e.g., `hash("user:123") % 10`). This means a specific key always goes to the exact same physical server to maintain consistency.

### Real-World Example
**Instagram:** When a normal user posts a photo, the traffic is minimal. When Cristiano Ronaldo posts a photo, tens of millions of users request the exact same cache key (`post:987654`) within minutes, crushing the single Redis node holding that key.

### Architecture / Raw Diagram
```text
[ Load Balancer / Hash Router ]
      |         |          |
   (Idle)    (Idle)     (Crushed)
[ Node 1 ] [ Node 2 ] [ Node 3 ]
                      "Ronaldo's Post"
```

### Data Flow
N/A

### When Would I Use It?
- When discussing scaling social media feeds, live sporting events, or viral news articles.

### When Would I NOT Use It?
- N/A

### Trade-offs
- N/A (It is a problem to solve, not a feature).

### Implementation Idea (Solutions)
1. **Key Duplication:** Create 10 copies of the hot key (`post:123_1`, `post:123_2`) and have the client randomly pick one to spread the read load across the cluster.
2. **Local Caching:** For ultra-hot keys, have the API server briefly cache the value in its local RAM (e.g., for 1 second) to shield the Redis cluster from the massive traffic.

### Interview Question
"Your Redis cluster has 10 nodes, but Node 3 is at 100% CPU while the rest are at 5%. The system is crashing. What is happening?"

### Follow-up
"How would you resolve a hot key issue for a viral celebrity post without buying larger servers?"

### Common Mistake
Assuming that simply adding more Redis nodes will fix a hot key. It won't. The hashing algorithm will still route 100% of traffic for that *specific key* to a single node, regardless of how many nodes you add.

---

## #41. Cache Consistency [Type D — Trade-off Scenario]

### What is it?
The challenge of keeping the data in the cache synchronized with the data in the primary database.

### Mental Model
The database is the CEO's actual schedule. The cache is the printed itinerary given to the staff. If the CEO changes a meeting, but the staff's itinerary isn't reprinted, they are out of sync.

### Why does it exist?
Because caches and databases are separate physical systems. Updates to one do not automatically reflect in the other unless you write code to enforce it.

### Real-World Example
**E-Commerce Prices:** If an admin updates a product price in the database from $10 to $20, but the frontend cache still shows $10, users will check out expecting to pay $10, causing massive customer service issues.

### Architecture / Raw Diagram
```text
(1) UPDATE DB: Price = $20
      │
      v 
  [ Database ] ---> (Out of sync!) <--- [ Cache ] (Still shows $10)
```

### Data Flow
N/A

### When Would I Use It?
- Always, when designing a caching strategy. You must explicitly state what level of consistency the application requires.

### When Would I NOT Use It?
- Pure append-only log systems usually don't cache data, thus avoiding consistency issues.

### Trade-offs
- **Strong Consistency:** Guarantees cache and DB perfectly match (Write-through cache, explicit invalidation). BUT adds write latency and complex error handling (What if the DB updates but the Redis network call fails?).
- **Eventual Consistency:** Cache uses a TTL (e.g., 5 mins) and will eventually refresh. Simple, fast. BUT users will see slightly stale data.

### Implementation Idea
For Strong Consistency on product prices:
When a price is updated, use a database transaction. After the commit succeeds, explicitly delete the cache key. If the cache delete fails, retry it via a background queue.

### Interview Question
"You use a Cache-aside pattern for an e-commerce catalog. How do you ensure users don't see an outdated price when it changes in the database?"

### Follow-up
"If you explicitly delete the cache key after updating the database, what happens if the network drops right after the DB update but before the cache delete?" (Answer: The cache remains stale until its TTL expires. To fix, use Change Data Capture (CDC) like Debezium to read DB transaction logs and guarantee cache updates).

### Common Mistake
Assuming standard Cache-Aside pattern provides strong consistency. It only provides eventual consistency unless strictly managed.

---

# F. DISTRIBUTED SYSTEM FUNDAMENTALS (Basic)

## #42. Distributed Systems [Type A — Concept]

### What is it?
A collection of independent computers (nodes) that appear to the end-user as a single coherent system. They communicate and coordinate actions by passing messages over a network.

### Mental Model
A single-node system is a one-man band playing every instrument.
A distributed system is an orchestra. Each musician (server) plays a specific part, and they must coordinate perfectly to make music, even if they can't physically see every other musician.

### Why does it exist?
A single machine has hard limits on CPU, memory, and disk. At a certain scale, it is cheaper and more reliable to use 100 small computers rather than trying to build 1 impossible supercomputer.

### Real-World Example
**Google Search:** When you search, the query doesn't go to one giant server. It hits a load balancer, routes to a frontend service, which queries hundreds of index servers in parallel, which aggregate the results and return them in milliseconds.

### Architecture / Raw Diagram
```text
                   [ Master Node ]
                 /        |        \
 [ Worker Node A ] [ Worker Node B ] [ Worker Node C ]
```

### Data Flow
N/A

### When Would I Use It?
- When you reach the limits of Vertical Scaling.
- When you require high availability (if one machine dies, the system survives).

### When Would I NOT Use It?
- Small applications. Distributed systems introduce massive complexity: network failures, clock synchronization issues, and data consistency headaches.

### Trade-offs
- **What do I gain?** Infinite scalability, fault tolerance, and geographic distribution.
- **What do I sacrifice?** Operational simplicity. Debugging a bug that spans 5 different servers is incredibly difficult.

### Implementation Idea
Deploying multiple Docker containers orchestrated by **Kubernetes** is the standard modern way to build distributed applications.

### Interview Question
"Why do modern tech companies prefer distributed systems over single, massive mainframes?"

### Follow-up
"What are the 'Fallacies of Distributed Computing'?" (Answer: Assuming the network is reliable, latency is zero, bandwidth is infinite, and topology doesn't change).

### Common Mistake
Designing a distributed system without accounting for network failures. In distributed systems, a network call *will* eventually fail, and your code must handle it.

---

## #43. Strong vs Eventual Consistency [Type A — Concept]

### What is it?
Defines how quickly updates to data propagate across a distributed system.
- **Strong Consistency:** Once a write is acknowledged, *every* subsequent read from *any* node will return that updated value.
- **Eventual Consistency:** A write might be acknowledged, but for a short period, reading from different nodes might return older data. "Eventually," all nodes will sync up.

### Mental Model
Strong Consistency = Depositing cash at a bank teller. The moment the receipt prints, your balance on the ATM outside perfectly reflects the new amount.
Eventual Consistency = Updating your profile picture on Facebook. For a few minutes, your phone shows the new pic, but your friend's laptop shows the old one. Eventually, they sync.

### Why does it exist?
Achieving Strong Consistency across a distributed system requires locking nodes and slowing down writes (consensus protocols). Eventual Consistency abandons locks, allowing lightning-fast writes, at the cost of temporary data mismatch.

### Real-World Example
**Banking Ledger:** Must be Strongly Consistent. You cannot allow a user to withdraw money from Node A while Node B thinks they still have a full balance.
**YouTube View Count:** Eventual Consistency is fine. If a video has 1,000,000 views, but an edge server shows 999,995 for five minutes, no one cares.

### Architecture / Raw Diagram
```text
STRONG: (Write must propagate to all nodes before returning Success)
Client ─> [Node A] ─(Wait)─> [Node B]
                        <──(Ack)───
       <─ (Success)

EVENTUAL: (Return Success immediately, sync in background)
Client ─> [Node A] ──(Success)──> Client
             |
          (Async)
             v
          [Node B]
```

### Data Flow
N/A

### When Would I Use It?
- **Strong:** Financial data, inventory for e-commerce checkouts, password updates.
- **Eventual:** Social media feeds, view counts, product recommendations.

### When Would I NOT Use It?
- Do not use Strong Consistency for highly available global systems where speed is the primary factor (e.g., a globally distributed cache).

### Trade-offs
- **Strong:** Perfect data correctness. BUT high latency and poor availability during network splits (CAP Theorem).
- **Eventual:** Extremely fast, highly available. BUT application code must handle the logic of seeing stale data.

### Implementation Idea
In AWS DynamoDB, you can explicitly request this at query time: `GetItem` can take a parameter `ConsistentRead: true` (slower, accurate) or `false` (faster, eventually consistent).

### Interview Question
"For an e-commerce shopping cart, would you prioritize strong or eventual consistency?"

### Follow-up
"Explain how asynchronous read replicas inherently create an eventually consistent system."

### Common Mistake
Believing Eventual Consistency means the data might be lost. It won't be lost; it is safely written. It just takes time to travel to the read nodes.

---

## #44. Consensus (Leader/Follower) [Type A — Concept]

### What is it?
In a distributed system, nodes must agree on the state of the data. The **Leader/Follower** (Master/Slave) architecture assigns one node (the Leader) to handle all writes and dictate the truth, while Followers copy the Leader. If the Leader dies, the Followers vote to elect a new Leader.

### Mental Model
A teacher (Leader) writing on a chalkboard. The students (Followers) copy notes into their notebooks. If the teacher leaves, the class votes for a student to step up to the chalkboard and take over.

### Why does it exist?
If 5 database nodes all accept writes simultaneously (Multi-Leader), they will inevitably overwrite each other's data (conflict resolution). Having a single Leader eliminates write conflicts.

### Real-World Example
**Kafka:** Every topic partition has one Leader broker that handles reads/writes. If it crashes, ZooKeeper (or KRaft) runs a consensus algorithm to promote a follower to Leader within seconds.

### Architecture / Raw Diagram
```text
             [ LEADER ]  <--- All Writes go here
            /          \
   (Streams log)    (Streams log)
          v              v
    [ FOLLOWER ]     [ FOLLOWER ]  <--- Can serve Reads
```

### Data Flow
1. Client writes to Leader.
2. Leader writes to its local log.
3. Leader broadcasts log to Followers.
4. Followers acknowledge receipt.
5. Leader tells client "Success".

### When Would I Use It?
- Almost all relational database clusters (PostgreSQL, MySQL).
- Distributed message queues (Kafka, RabbitMQ).

### When Would I NOT Use It?
- Multi-region global databases where forcing all writes to travel to a single Leader in New York from Tokyo would cause terrible latency (Leaderless systems like Cassandra are used instead).

### Trade-offs
- **What do I gain?** Simple conflict resolution (no conflicts).
- **What do I sacrifice?** The Leader becomes a bottleneck for writes. (Write throughput cannot scale horizontally in a single-leader setup).

### Implementation Idea
Algorithms like **Raft** or **Paxos** handle the complicated math of leader election. As a backend engineer, you don't write this; you utilize tools like **ZooKeeper** or **etcd** which implement it.

### Interview Question
"In a primary-replica database setup, what happens when the primary node crashes?"

### Follow-up
"What is a 'Split Brain' problem?" (Answer: A network failure causes half the cluster to elect a new Leader, while the old Leader is still alive and accepting writes, resulting in two conflicting truths).

### Common Mistake
Assuming a Leader/Follower setup solves horizontal write scaling. Followers only scale *reads*. Writes are still constrained by the single Leader.

---

## #45. Distributed Locks [Type E — Implementation Scenario]

### What is it?
A mechanism to ensure that across a distributed system (multiple servers), only *one* server can perform a specific action at a time.

### Mental Model
A restroom key in a coffee shop. Even if there are 50 customers (servers), there is only one key. You must acquire the key to use the restroom, and return it when done.

### Why does it exist?
If you have a cron job that runs a daily billing script, and you deploy 5 instances of your Node.js app, all 5 instances might run the script at midnight, charging users 5 times. A distributed lock ensures only the first instance to wake up gets to run it.

### Real-World Example
**Ticketmaster:** When you click "Buy" on a concert ticket, a distributed lock is placed on that specific seat ID in Redis. No other server can sell that seat to another user for 5 minutes while you enter your credit card.

### Architecture / Raw Diagram
```text
App 1 ─> "Lock Seat 5" ─> [ Redis ] (Returns Success)
App 2 ─> "Lock Seat 5" ─> [ Redis ] (Returns Failed/Locked)
```

### Data Flow
1. Server A requests lock: `SETNX my_lock 1`.
2. Redis returns 1 (Success). Server A proceeds.
3. Server B requests lock: `SETNX my_lock 1`.
4. Redis returns 0 (Failed). Server B aborts.
5. Server A finishes, deletes `my_lock`.

### When Would I Use It?
- Background cron jobs running in clustered environments.
- Managing limited inventory (booking a flight, buying a ticket).

### When Would I NOT Use It?
- If the operation can be handled natively by atomic database queries (e.g., `UPDATE inventory SET count = count - 1 WHERE count > 0`).

### Trade-offs
- **What do I gain?** Prevents race conditions across distributed microservices.
- **What do I sacrifice?** Complexity and risk of deadlocks (if an app acquires a lock and crashes before releasing it).

### Implementation Idea
Use Redis with a TTL (Time To Live).
```javascript
// Using Redis
const lock = await redis.set('billing_lock', 'locked', 'NX', 'EX', 30);
if (lock) {
   // I have the lock for 30 seconds. Do work.
   await runBilling();
   await redis.del('billing_lock');
} else {
   // Someone else is running billing. Do nothing.
}
```

### Interview Question
"You have 3 worker nodes that pick up tasks from a queue. How do you ensure two workers don't process the exact same task?"

### Follow-up
"In your Redis lock implementation, why is it critical to set an Expiration (TTL) on the lock?" (Answer: If the server crashes while holding the lock, the TTL ensures the lock eventually auto-releases, preventing a permanent deadlock).

### Common Mistake
Relying on application-level locks (like a JavaScript mutex or Java `synchronized` block) in a cloud environment. Local locks only work on one machine; they do nothing when you have 5 instances running behind a load balancer.

---

## #46. Circuit Breakers [Type B — Practical Design]

### What is it?
A design pattern that monitors for failures in external service calls. If a service is failing repeatedly, the circuit breaker "trips" (opens) and immediately returns an error for all future requests, giving the failing service time to recover.

### Mental Model
Like an electrical circuit breaker in your house. If too much current flows (or errors happen), it flips to stop the flow, preventing the house from catching fire. You reset it later when it's safe.

### Why does it exist?
If Service A calls Service B, and Service B is down, Service A will wait for a timeout (e.g., 5 seconds) on every request. Service A's threads will fill up waiting, causing Service A to crash. A Circuit Breaker fails fast, protecting Service A.

### Real-World Example
**Netflix (Hystrix):** If the Recommendation Service goes down, the Circuit Breaker trips. The frontend API immediately returns a default list of popular movies instead of waiting 5 seconds for a timeout, keeping the app snappy.

### Architecture / Raw Diagram
```text
Normal (Closed):
App ──> Circuit Breaker ──> External API

Failing (Open):
App ──> Circuit Breaker (Tripped! Returns Error instantly)
             X (Blocks call)
        External API (Given time to recover)
```

### Data Flow
1. App calls API. API times out.
2. App calls API. API times out.
3. Threshold reached (e.g., 5 errors in 10s). Breaker trips OPEN.
4. Next App call is instantly rejected by Breaker (no network call made).
5. After 30s, Breaker goes HALF-OPEN, allows 1 test ping. If success, CLOSES. If fail, re-OPENS.

### When Would I Use It?
- Any time your microservice makes a synchronous network call to another microservice or a third-party API (Stripe, Twilio).

### When Would I NOT Use It?
- Asynchronous message queues (Kafka) inherently buffer failures, making circuit breakers less necessary for those flows.

### Trade-offs
- **What do I gain?** System resilience, prevents cascading failures across microservices.
- **What do I sacrifice?** Briefly blocking traffic that *might* have succeeded if the external service recovered exactly at that moment.

### Implementation Idea
Use libraries like **Opossum** (Node.js) or **Resilience4j** (Java).
```javascript
const CircuitBreaker = require('opossum');
const breaker = new CircuitBreaker(callExternalAPI, {
  timeout: 3000, // If call takes longer than 3s, trigger a failure
  errorThresholdPercentage: 50, // Trip if 50% of requests fail
  resetTimeout: 30000 // Wait 30s before trying again
});

breaker.fire().catch(fallbackFunction);
```

### Interview Question
"In a microservices architecture, Service A depends on Service B. If Service B becomes unresponsive, how do you prevent Service A from running out of resources?"

### Follow-up
"What is a 'fallback' in the context of a circuit breaker?" (Answer: A default action taken when the breaker is open, like returning cached data instead of a live response).

### Common Mistake
Confusing a Circuit Breaker with a Retry mechanism. Retries push *more* load onto a failing system. Circuit Breakers *stop* load on a failing system. (They are often used together: Retry 3 times -> if fail, Trip Circuit Breaker).

---

# H. STORAGE + FILE SYSTEMS

## #47. Object Storage (S3) vs Block Storage [Type A — Concept]

### What is it?
- **Object Storage (Amazon S3):** Stores data as discrete objects in a flat structure, accessed via REST API over HTTP. Excellent for massive scale, images, and backups.
- **Block Storage (Amazon EBS):** Stores data in fixed-sized raw blocks, attached to a single server like a physical hard drive. Excellent for databases and OS files.

### Mental Model
Object Storage = A massive valet parking lot. You give them a car (file) and they give you a ticket (URL). You don't know exactly where they parked it.
Block Storage = A personal garage attached to your house. You control exactly where the car is, and you can swap out the engine (modify parts of a file rapidly).

### Why does it exist?
Block storage is extremely fast but cannot easily be shared across thousands of servers. Object storage is infinitely scalable and shareable globally via HTTP, but cannot be modified piece-by-piece (you must overwrite the whole object).

### Real-World Example
**Netflix:** Video files are stored in Object Storage (S3) so they can be delivered globally. But the actual PostgreSQL database running the user accounts is stored on Block Storage (EBS) attached to an EC2 instance.

### Architecture / Raw Diagram
```text
BLOCK STORAGE (Fast, Local):
[ EC2 Server ] <=== Direct Connection ===> [ EBS Hard Drive ]

OBJECT STORAGE (Scalable, HTTP):
[ Client A ] ──(HTTP)──> [ Amazon S3 ] <──(HTTP)── [ Server B ]
```

### Data Flow
N/A

### When Would I Use It?
- **Object Storage:** Profile pictures, videos, PDF documents, database backups.
- **Block Storage:** Running a database, running an operating system, or high-I/O caching.

### When Would I NOT Use It?
- Do not run a database on Object Storage (S3). It is too slow and doesn't support partial file modifications.

### Trade-offs
- **Object:** Infinitely scalable, cheap, built-in redundancy. BUT higher latency (HTTP overhead) and immutable (cannot edit a single line of a text file, must re-upload).
- **Block:** Ultra-fast, modifiable. BUT expensive, hard to share across servers.

### Implementation Idea
For a file upload feature, use AWS S3. Generate a pre-signed URL (see Concept 50) and have the frontend upload the object directly.

### Interview Question
"Where would you store 500 terabytes of user-uploaded videos, and why?"

### Follow-up
"Why can't you just attach a massive 500TB Block Storage volume to your web server and store the videos there?"

### Common Mistake
Trying to use a database (like PostgreSQL) to store large binary files (BLOBs). Always store large files in Object Storage, and only store the URL to the file in the database.

---

## #48. Content Delivery Network (CDN) [Type A — Concept]

### What is it?
A globally distributed network of proxy servers that cache static content (images, HTML, videos) closer to the end user to reduce latency and save backend bandwidth.

### Mental Model
Instead of ordering a pizza from a kitchen in New York and waiting 3 days for it to arrive in London, a CDN is like opening a local branch in London that stores pre-made frozen pizzas.

### Why does it exist?
Light travels fast, but not instantly. A user in Tokyo requesting an image from a server in New York experiences ~200ms of latency just from geography. Caching the image on a Tokyo CDN edge node reduces latency to 10ms.

### Real-World Example
**Cloudflare:** Sits in front of a website. When a user in India requests `logo.png`, Cloudflare serves it from a data center in Mumbai, completely shielding the origin server in AWS US-East from the traffic.

### Architecture / Raw Diagram
```text
[ Client (Japan) ] ─> [ CDN Edge Node (Tokyo) ] -> (Cache Hit: Fast!)
                               │
                          (Cache Miss)
                               v
                       [ Origin Server (New York) ]
```

### Data Flow
1. Client requests `image.jpg`.
2. Request routed (via DNS) to geographically closest CDN node.
3. If node has it, return immediately.
4. If not, node requests it from Origin Server, caches it, and returns it.

### When Would I Use It?
- Serving static assets: Images, Videos, CSS, JS bundles.
- Protecting against DDoS attacks (CDNs absorb massive traffic).

### When Would I NOT Use It?
- Dynamic, user-specific data (e.g., a bank account balance). Caching private data on a public CDN is a major security breach.

### Trade-offs
- **What do I gain?** Drastically lower latency for users worldwide and massive reduction in origin server traffic.
- **What do I sacrifice?** Cache invalidation complexity (updating a CSS file requires purging the CDN cache, which takes time to propagate globally).

### Implementation Idea
Put **Amazon CloudFront** in front of an S3 bucket containing your React frontend build and user images.

### Interview Question
"Users in Asia are complaining that your US-hosted website takes 5 seconds to load images. How do you solve this?"

### Follow-up
"How do you ensure a CDN serves the latest version of your JavaScript file when you push an update?" (Answer: File versioning or cache busting, e.g., `app_v2.js`).

### Common Mistake
Forgetting to configure CORS headers properly on the CDN, causing cross-origin fetching errors on the frontend.

---

## #49. Large File Handling [Type E — Implementation Scenario]

### What is it?
Techniques required to upload and download massive files (gigabytes) without exhausting server memory or timing out HTTP connections.

### Mental Model
You don't swallow a whole pizza in one bite. You cut it into slices, chew each slice, and swallow sequentially.

### Why does it exist?
If a user uploads a 5GB video via a standard HTTP POST request, the API server will try to buffer it into RAM, crash with an Out-of-Memory error, or the load balancer will kill the connection because it took longer than 60 seconds.

### Real-World Example
**Google Drive:** Uses "Chunked Uploads". A 5GB file is split into 50MB chunks by the browser. Each chunk is uploaded individually. If chunk #45 fails, only chunk #45 is retried, not the whole 5GB file.

### Architecture / Raw Diagram
```text
Frontend (Browser)
   |-- (Chunk 1) --> [ Storage API ]
   |-- (Chunk 2) --> [ Storage API ]
   |-- (Chunk 3) --> [ Storage API ]
           (Reassembled on server/S3)
```

### Data Flow
1. Frontend File API slices the file into chunks.
2. Initiates a multipart upload with S3 to get an Upload ID.
3. Uploads each chunk concurrently or sequentially.
4. Tells S3 to complete the upload, stitching the chunks together.

### When Would I Use It?
- Video sharing platforms, cloud storage apps, heavy data-processing pipelines.

### When Would I NOT Use It?
- Profile pictures or small PDF documents (< 10MB) can just be standard POST requests.

### Trade-offs
- **What do I gain?** Reliable uploads for massive files, resumable uploads (if WiFi drops, resume from the last chunk).
- **What do I sacrifice?** Complex frontend logic and tracking chunk states.

### Implementation Idea
Use AWS S3 **Multipart Upload**.
In Javascript: Use `Blob.slice(start, end)` to create chunks of the file before uploading.

### Interview Question
"Design a system to allow users to upload 10GB 4K videos. How does the upload process work reliably over unstable mobile networks?"

### Follow-up
"If a user's phone dies at 90% uploaded, how do they resume without starting over?"

### Common Mistake
Trying to process the large file synchronously on the API server. Always offload the actual file stream directly to Object Storage (S3).

---

## #50. Pre-signed URLs [Type B — Practical Design]

### What is it?
A URL that grants temporary, time-limited access to upload or download a specific object in private cloud storage without exposing cloud credentials or requiring the file to pass through the backend server.

### Mental Model
Giving a delivery driver a digital barcode that opens your specific locker door, but the barcode expires in exactly 15 minutes.

### Why does it exist?
To achieve Secure File Uploads (Concept #24) and Downloads. Without it, you either make the entire S3 bucket public (massive security flaw) or stream every file through your API server (terrible performance).

### Real-World Example
**Slack:** When you upload an image to Slack, the Slack API quickly generates a Pre-signed URL. Your Slack client then uploads the image directly to Amazon S3 using that URL.

### Architecture / Raw Diagram
```text
(1) GET /generate-upload-link
Client ───────────────> API Server
                        (Validates user, signs URL with AWS Secret Key)
Client <─────────────── API Server
(2) Returns https://s3.../?signature=xyz&Expires=12345

(3) PUT file directly
Client ───────────────> Amazon S3
```

### Data Flow
1. Client asks API for permission to upload.
2. API verifies AuthZ, asks AWS SDK to generate a signed URL.
3. API returns URL to client.
4. Client executes HTTP PUT with the file binary to the URL.
5. S3 accepts it because the cryptographically signed URL is valid.

### When Would I Use It?
- User-generated media uploads.
- Allowing users to download private, paid content (like a purchased eBook).

### When Would I NOT Use It?
- For public static assets like a website logo (just make the logo public in the bucket/CDN).

### Trade-offs
- **What do I gain?** Extreme offloading of bandwidth from API servers while maintaining strict security.
- **What do I sacrifice?** The API server doesn't "see" the file. If you need to resize an image, you must trigger an asynchronous AWS Lambda function on the S3 bucket *after* the upload finishes.

### Implementation Idea
Node.js with AWS SDK:
```javascript
const s3 = new AWS.S3();
const url = s3.getSignedUrl('putObject', {
  Bucket: 'my-bucket',
  Key: 'user-123/profile.jpg',
  Expires: 300 // Valid for 5 minutes
});
res.json({ uploadUrl: url });
```

### Interview Question
"How do you allow a logged-in user to download a private PDF file without streaming the file data through your Node.js application?"

### Follow-up
"How do you prevent a user from generating an upload URL and uploading a 50GB file instead of a 2MB image?" (Answer: The pre-signed URL policy can strictly enforce a `content-length-range` parameter).

### Common Mistake
Generating the URL but forgetting to enforce file type and size limits, allowing attackers to dump massive garbage files directly into your expensive S3 bucket.

---
*(End of Module 1. Module 2 begins next, focusing on Practical Systems and AI).*
