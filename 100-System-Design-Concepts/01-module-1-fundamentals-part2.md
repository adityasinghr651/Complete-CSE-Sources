# MODULE 1 — CONCEPTS 1–50 (PART 2: 13-25)

## #13. API Gateways [Type A — Concept]

### What is it?
An API Gateway is a server that acts as an API front-end, receiving API requests, enforcing throttling and security policies, passing requests to the back-end service, and then passing the response back to the requester.

### Mental Model
It’s like the receptionist at a massive corporate office. Instead of trying to find the exact desk of the HR manager or IT guy (microservices), you go to the receptionist, tell them what you need, and they check your ID, route your request, and return the result.

### Why does it exist?
In a microservices architecture, a single client request might require data from 5 different services. An API Gateway handles routing, rate limiting, authentication, and aggregation in one place so each microservice doesn't have to rewrite that code.

### Real-World Example
**Netflix** uses an API gateway (Zuul) to handle requests from diverse devices (TVs, phones, web). The Gateway knows that a mobile device needs smaller images and a different data payload than a 4K Smart TV, and routes the request accordingly.

### Architecture / Raw Diagram
```text
      ┌─────────┐
      │  Client │
      └────┬────┘
           │ /api/v1/dashboard
           v
   ┌────────────────┐ (Rate Limits, Auth, Logging)
   │  API GATEWAY   │
   └─┬────────────┬─┘
     │            │
     v            v
┌────────┐    ┌────────┐
│User Svc│    │Feed Svc│
└────────┘    └────────┘
```

### Data Flow
1. Client requests `/dashboard`.
2. Gateway verifies JWT token.
3. Gateway fetches user data from User Service.
4. Gateway fetches feed data from Feed Service.
5. Gateway combines both JSONs.
6. Gateway returns combined response to Client.

### When Would I Use It?
- Any microservice architecture.
- When you need a single point of entry for rate-limiting, SSL termination, or authentication.

### When Would I NOT Use It?
- In a simple Monolith. The monolith itself already acts as the single point of entry.

### Trade-offs
- **What do I gain?** Centralized security, monitoring, and simplified client routing.
- **What do I sacrifice?** It adds a network hop (slight latency) and creates a potential SPOF if not highly available.

### Implementation Idea
Use **Kong** or **AWS API Gateway**. Or code a simple MVP gateway using Node.js/Express that proxies requests using a library like `http-proxy-middleware`, adding an authentication middleware before forwarding requests.

### Interview Question
"In a microservices architecture, how do you handle authentication without forcing every single service to connect to the User database?"

### Follow-up
"What is API Aggregation, and why is it useful for mobile clients?" (Answer: Combining multiple microservice responses into one payload at the gateway to save battery and network round-trips for the mobile device).

### Common Mistake
Putting heavy business logic inside the API Gateway. The Gateway should only do routing and cross-cutting concerns (auth/rate-limiting), not compute taxes or generate reports.

---

## #14. Idempotency [Type E — Implementation Scenario]

### What is it?
Idempotency means that making multiple identical requests has the same effect as making a single request. 

### Mental Model
Pressing a crosswalk button. Pressing it once turns on the "wait" signal. Pressing it 10 more times doesn't change anything—the signal is still just "wait".

### Why does it exist?
Networks are unreliable. A client might send a "Charge $10" request, the server charges the card, but the response drops due to bad WiFi. The client retries. Without idempotency, the user is charged $20.

### Real-World Example
**Stripe API:** When creating a charge, Stripe allows you to pass an `Idempotency-Key` header. If Stripe sees a request with an `Idempotency-Key` it has already processed, it simply returns the cached success response instead of charging the card again.

### Architecture / Raw Diagram
```text
Request (Charge $10, ID_KEY: X123)
      ↓
[ API Server ]
      ↓
Check DB/Redis: "Have I seen X123?"
      ├──> Yes: Return previous saved response
      └──> No: Process payment, save response under X123, return.
```

### Data Flow
1. Client generates UUID (`X123`).
2. API checks Redis for `key: X123`.
3. If found, return cached response.
4. If not found, process transaction, save result to Redis, return.

### When Would I Use It?
- Financial transactions, order creations, and any API where a duplicate `POST` would corrupt data or cost money.

### When Would I NOT Use It?
- `GET` requests are naturally idempotent. `PUT` and `DELETE` usually are. Only `POST` generally requires explicit idempotency engineering.

### Trade-offs
- **What do I gain?** Extreme reliability and protection against duplicate bugs.
- **What do I sacrifice?** Storage overhead (must save all recent idempotency keys and their responses) and added latency on writes.

### Implementation Idea
Generate a UUID on the frontend and send it in the header.
In Node.js:
```javascript
const cached = await redis.get(`idempotency:${req.headers['idempotency-key']}`);
if (cached) return res.json(JSON.parse(cached));
// ... process payment ...
await redis.setex(`idempotency:${key}`, 86400, JSON.stringify(result));
```

### Interview Question
"How would you ensure a user is not double-charged if they click the 'Pay' button twice quickly?"

### Follow-up
"How long should you store the idempotency keys in your database/cache?"

### Common Mistake
Relying only on frontend UI disabling (disabling the button after one click). A bad network connection or a malicious API call easily bypasses UI restrictions.

---

## #15. Retries & Exponential Backoff [Type D — Trade-off Scenario]

### What is it?
When a network request fails, the system automatically tries again. **Exponential backoff** means waiting progressively longer between retries (e.g., 1s, 2s, 4s, 8s) to avoid overwhelming a struggling server.

### Mental Model
Calling a friend who isn't answering. If you call every 1 second, their phone crashes. If you wait 5 mins, then 10 mins, then 20 mins, you give them time to finish what they are doing and answer.

### Why does it exist?
Many distributed system errors are transient (temporary). A database might be locked for 500ms. Retrying instantly fixes 99% of these issues. But if the server is truly down, a million clients retrying simultaneously causes a DDoS attack (Thundering Herd).

### Real-World Example
**AWS SDKs:** Every AWS library (Boto3, AWS SDK for Node) implements exponential backoff by default when talking to AWS services like DynamoDB or S3.

### Architecture / Raw Diagram
```text
Client ──(Attempt 1)──> Server (Fails: 503)
[Wait 1s]
Client ──(Attempt 2)──> Server (Fails: 503)
[Wait 2s]
Client ──(Attempt 3)──> Server (Fails: 503)
[Wait 4s + Jitter]
Client ──(Attempt 4)──> Server (Success: 200)
```

### Data Flow
N/A

### When Would I Use It?
- Any inter-service communication (microservices).
- Connecting to databases, caches, or external third-party APIs.

### When Would I NOT Use It?
- Do not retry on `400 Bad Request` or `401 Unauthorized` (the request is malformed and will *never* succeed). Only retry on `5xx` Server Errors or `429 Too Many Requests`.

### Trade-offs
- **What do I gain?** Self-healing systems that survive temporary blips.
- **What do I sacrifice?** Worst-case latency. A request that retries 4 times might take 15 seconds to finally fail, holding up resources.

### Implementation Idea
Add "Jitter" (randomness). Instead of strictly 2s, 4s, 8s, wait 2.1s, 3.8s, 8.4s. This prevents all failing clients from retrying at the exact same millisecond.

### Interview Question
"Your microservice calls an external API that is randomly throwing 500 errors. How do you handle this?"

### Follow-up
"What is Jitter, and why is it important alongside exponential backoff?"

### Common Mistake
Implementing infinite retries. Always set a `max_retries` limit (usually 3 to 5) and then fall back to a dead-letter queue or fail gracefully.

---

## #16. WebSockets vs Long Polling [Type D — Trade-off Scenario]

### What is it?
Two ways to achieve real-time data updates.
- **Long Polling:** Client asks server "Any updates?". Server holds the request open until there is an update, then replies. Client immediately asks again.
- **WebSockets:** A persistent, bi-directional TCP connection where client or server can send data at any time without HTTP headers overhead.

### Mental Model
Long Polling = Kids in a car asking "Are we there yet?". Dad ignores them until they arrive, says "Yes", then the trip is over.
WebSockets = A phone call. Both people stay on the line and can talk whenever they want.

### Why does it exist?
Standard HTTP is strictly request-response. The server cannot "push" data to the client. Real-time apps need a way to push data.

### Real-World Example
**WhatsApp Web:** Uses WebSockets to maintain a persistent connection so incoming messages appear instantly.
**Older Notification Systems:** Used Long Polling before WebSockets were widely supported in browsers.

### Architecture / Raw Diagram
```text
LONG POLLING                    WEBSOCKETS
Client        Server            Client        Server
  │──Request───>│ (Waits)          │<───Connected──>│
  │             │                  │                │
  │<──Response──│ (Data ready)     │<───Message─────│
  │──Request───>│ (Waits again)    │────Message────>│
```

### Data Flow
**WebSockets:**
1. Client sends HTTP GET with `Upgrade: websocket` header.
2. Server accepts. TCP connection stays open.
3. Frames of data pass back and forth.

### When Would I Use It?
- **WebSockets:** Chat apps, real-time multiplayer games, live stock tickers.
- **Long Polling:** When strict corporate firewalls block WebSocket protocols, or for very infrequent updates.

### When Would I NOT Use It?
- Do not use WebSockets for a simple blog or E-commerce site. Standard HTTP is vastly simpler and infinitely easier to scale.

### Trade-offs
- **WebSockets:** Truly real-time, low bandwidth overhead. BUT connection management is hard (scaling requires sticky sessions or a Pub/Sub backplane).
- **Long Polling:** Easy to implement, works over standard HTTP. BUT high overhead (re-establishing HTTP connections repeatedly).

### Implementation Idea
For WebSockets in Node.js, use `Socket.io`.
**MVP:** Single server holding WebSocket connections in memory.
**Scaled:** Multiple API servers, with a Redis Pub/Sub instance so if Server A receives a chat message, it broadcasts to Server B to push down its WebSocket.

### Interview Question
"Design a real-time live-updating dashboard for sports scores. Do you use HTTP polling or WebSockets?"

### Follow-up
"How do load balancers affect WebSocket connections?" (Answer: The LB must support long-lived connections and not timeout).

### Common Mistake
Assuming WebSockets magically scale like HTTP. A server might run out of open file descriptors (ports/sockets) if 100,000 users connect simultaneously.

---

# E. LOAD BALANCING + TRAFFIC

## #17. Load Balancer [Type A — Concept]

### What is it?
A device or software that distributes network or application traffic across a cluster of servers to improve responsiveness and availability.

### Mental Model
A traffic cop at a busy intersection routing cars evenly to 5 different toll booths so no single booth gets overwhelmed.

### Why does it exist?
Because a single server can only handle so much traffic (e.g., 10,000 requests/sec). To handle 50,000, you need 5 servers and a way to fairly divide the incoming traffic between them.

### Real-World Example
**Amazon ALB (Application Load Balancer):** Sits in front of EC2 instances. If an instance crashes, the ALB stops sending traffic to it.

### Architecture / Raw Diagram
```text
                  ┌───────┐
                  │Client │
                  └───┬───┘
                      │
               ┌──────v──────┐
               │Load Balancer│
               └──────┬──────┘
         ┌────────────┼────────────┐
         v            v            v
     ┌───────┐    ┌───────┐    ┌───────┐
     │ App 1 │    │ App 2 │    │ App 3 │
     └───────┘    └───────┘    └───────┘
```

### Data Flow
1. DNS resolves domain to Load Balancer IP.
2. Request hits LB.
3. LB uses an algorithm (e.g., Round Robin) to pick an App server.
4. LB forwards request to App server.
5. App server replies to LB, LB replies to Client.

### When Would I Use It?
- In *every* scaled backend architecture immediately behind the DNS/CDN.

### When Would I NOT Use It?
- A local development environment or a tiny hobby project running on a single VPS.

### Trade-offs
- **What do I gain?** Scale, redundancy, SSL termination, and protection against single-node failures.
- **What do I sacrifice?** The LB itself becomes a Single Point of Failure (SPOF) if not configured redundantly.

### Implementation Idea
Use **Nginx** as a software load balancer.
```nginx
upstream backend {
    server 10.0.0.1;
    server 10.0.0.2;
}
server {
    listen 80;
    location / {
        proxy_pass http://backend;
    }
}
```

### Interview Question
"What happens if one of the application servers behind a load balancer dies?"

### Follow-up
"How does a load balancer know that a server has died?" (Answer: Health Checks).

### Common Mistake
Forgetting that Load Balancers also need to scale. A single Nginx instance might choke at 50,000 RPS. At massive scale, you use DNS Round Robin to point to multiple Load Balancers.

---

## #18. Reverse Proxy [Type A — Concept]

### What is it?
A server that sits in front of web servers and forwards client requests to those web servers. While a load balancer distributes traffic to *many* servers, a reverse proxy can sit in front of just *one* server to provide security, caching, and compression.

### Mental Model
A Forward Proxy (like a VPN) protects the *Client* (hides your IP from the internet).
A Reverse Proxy protects the *Server* (hides your backend IP from the internet).

### Why does it exist?
To shield application servers (like Node or Python) from the direct chaos of the internet. Reverse proxies handle SSL certificates (HTTPS), gzip compression, and block malicious requests, letting the backend focus purely on business logic.

### Real-World Example
**Cloudflare:** Acts as a massive, globally distributed reverse proxy. It absorbs DDoS attacks and caches static assets before traffic ever reaches your actual server.

### Architecture / Raw Diagram
```text
Client ──> Internet ──> [ Reverse Proxy ] ──> [ Internal Network / Web Server ]
                          (Handles SSL)         (Handles Logic)
```

### Data Flow
Request -> Reverse Proxy decrypts HTTPS -> Forwards plain HTTP to internal server -> Internal server responds -> Proxy encrypts and sends back.

### When Would I Use It?
- To expose a backend application securely to the internet.
- To terminate SSL (SSL offloading) so backend apps don't waste CPU on cryptography.

### When Would I NOT Use It?
- Rarely. Almost all modern deployments put apps behind a reverse proxy (Nginx, HAProxy, Envoy).

### Trade-offs
- **What do I gain?** Security (hides internal IPs), performance (caching/compression), and simplified backend code.
- **What do I sacrifice?** Slight network hop latency.

### Implementation Idea
Deploying an Express.js app. Express is bad at handling slow clients and serving static files. Put Nginx (Reverse Proxy) in front of Express. Nginx serves the images and proxies only the API calls to Express.

### Interview Question
"Why shouldn't you expose a Node.js web server directly to port 80/443 on the public internet?"

### Follow-up
"What is SSL Termination and where is it typically done?"

### Common Mistake
Confusing a Load Balancer with a Reverse Proxy. (They often use the exact same software—Nginx—but have conceptually different primary goals: distribution vs shielding/routing).

---

## #19. L4 vs L7 Load Balancing [Type A — Concept]

### What is it?
Refers to the OSI model layers where routing decisions are made.
- **Layer 4 (Transport):** Routes traffic based purely on IP addresses and TCP/UDP ports. It doesn't look at the data payload.
- **Layer 7 (Application):** Routes traffic based on HTTP headers, URLs, cookies, and actual message content.

### Mental Model
L4 is a post office sorter looking only at the Zip Code (IP address) and tossing the package in a bin. Extremely fast.
L7 is a sorter opening the letter, reading "Attention: Billing Dept" (URL Path), and routing it appropriately. Slower, but smarter.

### Why does it exist?
L4 provides raw, lightning-fast throughput. L7 provides intelligent routing required for modern microservices (e.g., routing `/api/users` to one server and `/api/payments` to another).

### Real-World Example
**AWS:** Offers both Network Load Balancer (NLB - Layer 4) for extreme performance and low latency, and Application Load Balancer (ALB - Layer 7) for path-based routing.

### Architecture / Raw Diagram
```text
L4 (IP/Port only):
Traffic ──> LB ──> Random App Server (Fast)

L7 (Path-based):
Traffic (GET /images) ──> LB ──> Image Service
Traffic (GET /api)    ──> LB ──> API Service
```

### Data Flow
N/A

### When Would I Use It?
- **L7:** Microservices routing, A/B testing (routing by cookie), and SSL termination.
- **L4:** Massive volume, low-latency gaming traffic, or database connection balancing.

### When Would I NOT Use It?
- Don't use L7 if you need to route millions of UDP packets per second for a video game.

### Trade-offs
- **L4:** Faster, less CPU intensive. BUT blind to the application data (can't route by URL).
- **L7:** Smart routing, SSL termination. BUT slightly slower due to packet inspection and decryption.

### Implementation Idea
If using Kubernetes, the `Ingress` controller is a Layer 7 load balancer (routes by domain and path). The Kubernetes `Service` of type `LoadBalancer` provisions a Layer 4 load balancer.

### Interview Question
"You need to route traffic for `/video` to an expensive GPU cluster, and `/text` to a cheap CPU cluster. Do you use an L4 or L7 load balancer?"

### Follow-up
"Can an L4 load balancer perform SSL termination?" (Answer: No, it cannot read the encrypted payload, so it just passes the TCP stream through).

### Common Mistake
Using L4 load balancing for web microservices and trying to figure out how to route URLs.

---

## #20. Sticky Sessions [Type C — Debugging Scenario]

### What is it?
A load balancer configuration that forces a user's requests to always be routed to the *exact same* backend server for the duration of a session.

### Mental Model
Calling customer support, speaking to "Dave", hanging up, and demanding the operator route your next call back to "Dave" because he already knows your story.

### Why does it exist?
If a backend application is **stateful** (stores user login or shopping cart data in local server RAM), routing the user's second request to a different server will result in a "You are not logged in" error.

### Real-World Example
Legacy Java enterprise applications often store HTTP sessions in RAM. They require sticky sessions (usually tracked by injecting a cookie like `AWSELB`) to function properly behind a load balancer.

### Architecture / Raw Diagram
```text
Without Sticky Sessions:
Req 1 ─> LB ─> Server A (Cart: 1 item)
Req 2 ─> LB ─> Server B (Cart: Empty!)

With Sticky Sessions:
Req 1 ─> LB ─> Server A (Cart: 1 item)
Req 2 ─> LB ─> Server A (Cart: 2 items)
```

### Data Flow
1. Client makes first request. LB routes to Server A.
2. LB adds a cookie to the response: `SERVER_ID=A`.
3. Next request includes cookie. LB reads cookie, routes to Server A.

### When Would I Use It?
- When migrating legacy stateful applications to the cloud without rewriting their session management.

### When Would I NOT Use It?
- **Any modern cloud-native system.** Do not design a new system to require sticky sessions.

### Trade-offs
- **What do I gain?** Avoids rewriting legacy code to use external caches.
- **What do I sacrifice?** Load distribution becomes uneven. If Server A has 10 "heavy" users stuck to it, it might crash while Server B sits idle. Furthermore, if Server A crashes, all users attached to it lose their state immediately.

### Implementation Idea
If an interviewer asks how to fix a system relying on sticky sessions:
Move the session state out of the local server RAM and into a centralized **Redis** cache. Then the servers become stateless, and the load balancer can use standard Round Robin.

### Interview Question
"Your application works perfectly with one server, but users report getting logged out randomly when you added a load balancer. What is happening?"

### Follow-up
"How would you re-architect the application so you don't need sticky sessions?"

### Common Mistake
Suggesting sticky sessions as a good design choice for a new application. It is generally considered an anti-pattern for modern scalable systems.

---

## #21. Health Checks [Type B — Practical Design]

### What is it?
A mechanism where a Load Balancer or orchestrator (like Kubernetes) periodically sends a request to a backend server to see if it is alive and functioning.

### Mental Model
A boss walking by your desk every 5 minutes and asking "Are you awake?". If you don't answer, they stop giving you work.

### Why does it exist?
Servers crash, run out of memory, or get stuck in infinite loops. If a load balancer doesn't know a server is broken, it will keep sending user traffic to it, resulting in failed requests.

### Real-World Example
**Kubernetes:** Uses `livenessProbes` to restart a crashed container, and `readinessProbes` to stop sending traffic to a container that is alive but too busy to handle new requests.

### Architecture / Raw Diagram
```text
           [ Load Balancer ]
            /      |      \
        (Ping)  (Ping)  (Ping)
          v        v        v
        [Srv A]  [Srv B]  [Srv C]
        (200 OK) (Timeout) (200 OK)
            \      |      /
     (Traffic sent only to A & C)
```

### Data Flow
1. LB sends `GET /health` to Server B.
2. Server B doesn't reply within 2 seconds.
3. LB marks Server B as "Unhealthy".
4. LB removes B from the routing pool.
5. User traffic is only sent to A and C.

### When Would I Use It?
- Absolutely required in any load-balanced or orchestrated environment.

### When Would I NOT Use It?
- N/A.

### Trade-offs
- **What do I gain?** Automated failover and high availability.
- **What do I sacrifice?** Tiny amount of overhead for the pinging. Complex health checks (checking DB connections) can accidentally take down the whole cluster if the DB stutters.

### Implementation Idea
In an Express/Node.js app:
```javascript
app.get('/health', async (req, res) => {
  try {
    await db.query('SELECT 1'); // verify DB connection is alive
    res.status(200).send('OK');
  } catch (e) {
    res.status(500).send('ERROR');
  }
});
```

### Interview Question
"How does a load balancer prevent routing traffic to a dead server?"

### Follow-up
"What is the danger of having your `/health` endpoint query the database to check status?" (Answer: If the DB gets slightly slow, all API servers fail their health check simultaneously, the Load Balancer removes ALL servers, and your entire app goes down due to a brief DB hiccup).

### Common Mistake
Creating "shallow" health checks that just return `200 OK` even if the app has lost connection to the database, meaning the LB keeps routing traffic to an app that can't actually serve data.

---

# J. SECURITY (Basic Fundamentals)

## #22. Authentication vs Authorization [Type A — Concept]

### What is it?
- **Authentication (AuthN):** Verifying *who* you are (Identity).
- **Authorization (AuthZ):** Verifying *what you are allowed to do* (Permissions).

### Mental Model
Authentication = Checking your ID at the airport security gate. (Yes, you are John).
Authorization = Checking your ticket at the First Class lounge. (John is allowed in Terminal A, but not the First Class lounge).

### Why does it exist?
To separate identity verification from access control. A user can be perfectly authenticated (logged in), but not authorized to delete the database.

### Real-World Example
**AWS IAM:** 
AuthN: Logging in with a username/password or MFA.
AuthZ: IAM Policies dictating that this logged-in user can Read from S3 but cannot Write.

### Architecture / Raw Diagram
```text
User ──> Login (/login) ──> AuthN (Validates Password, issues Token)

User ──> Delete Post ──> AuthZ (Reads Token, Checks "isAdmin == true")
```

### Data Flow
N/A

### When Would I Use It?
- In every system that has users.

### When Would I NOT Use It?
- Completely open public data APIs (like a public weather API), though they still usually use API keys for rate limiting.

### Trade-offs
- N/A

### Implementation Idea
**AuthN:** Compare hashed passwords using `bcrypt`.
**AuthZ:** Middleware that checks roles.
```javascript
// AuthZ Middleware
function requireAdmin(req, res, next) {
  if (req.user.role !== 'ADMIN') return res.status(403).send('Forbidden');
  next();
}
```

### Interview Question
"Explain the difference between a 401 Unauthorized and a 403 Forbidden HTTP status code." (Answer: 401 means AuthN failed / not logged in. 403 means AuthN succeeded, but AuthZ failed / you lack permissions).

### Follow-up
"How would you implement authorization in a microservices architecture where permissions change frequently?"

### Common Mistake
Mixing them up in conversation, or writing API gateways that do Authentication but trusting downstream microservices to blindly assume the user is Authorized without checking.

---

## #23. JWT vs Session-based Auth [Type D — Trade-off Scenario]

### What is it?
Two distinct ways to manage user identity across HTTP requests.
- **Session-based:** The server stores the user's state in memory or a database, and gives the client a random ID (cookie) to look it up.
- **JWT (JSON Web Token):** The server cryptographically signs a JSON payload containing the user's data and gives it to the client. The server stores *nothing*.

### Mental Model
Session = A coat check ticket. The ticket is just a random number. The club stores your coat.
JWT = A driver's license. All the info is printed on the card itself, protected by a tamper-proof hologram (signature). The bouncer doesn't need to look you up in a database.

### Why does it exist?
Sessions are hard to scale horizontally (requires centralized databases like Redis). JWTs scale perfectly because any server can verify the token mathematically without database lookups.

### Real-World Example
**GitHub:** Uses session cookies for its main website UI.
**OAuth/Mobile APIs:** Uses JWTs to pass stateless identity claims between decoupled backend services.

### Architecture / Raw Diagram
```text
SESSION:
Client (Cookie: abc) ──> Server (Looks up 'abc' in Redis -> User 1)

JWT:
Client (Token: eyJ...) ──> Server (Validates signature math -> User 1) -> No DB required!
```

### Data Flow
**JWT:**
1. User logs in.
2. Server creates JSON: `{"userId": 1, "role": "admin", "exp": 12345}`.
3. Server signs it with a secret key.
4. Client sends JWT in `Authorization` header on next request.
5. Server validates signature.

### When Would I Use It?
- **JWT:** Microservices, mobile apps, or when extreme horizontal scaling is needed.
- **Session:** Traditional server-rendered web apps, or when you need strict control to revoke a user's access instantly.

### When Would I NOT Use It?
- Don't use JWT if you need the ability to instantly ban/logout a specific user. (JWTs cannot be easily revoked until they expire, without creating a centralized "blacklist," which defeats the stateless purpose).

### Trade-offs
- **JWT:** Stateless, infinitely scalable. BUT hard to revoke, and payload sizes can get large.
- **Session:** Easy to revoke, small cookie size. BUT requires a stateful backend store (Redis) adding latency and complexity.

### Implementation Idea
For a modern React + Node.js setup, use JWTs with a short expiration time (15 minutes) and a longer-lived Refresh Token stored securely in a database to mitigate the revocation issue.

### Interview Question
"Why are JSON Web Tokens considered stateless, and what is the primary security drawback of this statelessness?"

### Follow-up
"If a user clicks 'Log out from all devices', how do you invalidate their active JWTs?"

### Common Mistake
Putting sensitive data (like passwords or social security numbers) inside a JWT. JWTs are encoded (Base64), *not* encrypted. Anyone can read the contents.

---

## #24. Secure File Uploads [Type E — Implementation Scenario]

### What is it?
The architecture pattern for allowing users to upload large files (images, videos) safely without crashing the backend servers or opening security vulnerabilities.

### Mental Model
Instead of having Amazon deliver a huge package to your small apartment (API Server), you give the delivery driver a direct key to a storage locker (S3) so they drop it off there directly.

### Why does it exist?
Routing a 500MB video file through a Node.js API server will consume all the server's RAM and block other API requests. Furthermore, user uploads often contain malware or scripts.

### Real-World Example
**YouTube:** Does not stream your video upload through a standard web backend. It gives your browser a direct, secure connection to cloud storage to handle the heavy lifting.

### Architecture / Raw Diagram
```text
(1) Client requests Upload URL
      ↓
[ API Server ] (Checks Auth, generates Pre-signed S3 URL)
      ↓
(2) Returns Pre-signed URL to Client
      ↓
(3) Client uploads file DIRECTLY to S3
Client ───────────────> [ Amazon S3 / Blob Storage ]
```

### Data Flow
1. Client: "I want to upload `cat.mp4`."
2. Backend validates user, generates an S3 Pre-signed URL (valid for 5 mins), and returns it.
3. Client uses `PUT` to upload directly to S3.
4. S3 fires an event (webhook/queue) to tell Backend the upload finished.

### When Would I Use It?
- Any application that handles user-generated media, profile pictures, or document uploads.

### When Would I NOT Use It?
- If the file is tiny (like a 2KB CSV file), you can just POST it to the backend directly to keep things simple.

### Trade-offs
- **What do I gain?** API servers remain fast, stateless, and immune to out-of-memory crashes from large files.
- **What do I sacrifice?** Increased frontend complexity to handle direct uploads and background syncing.

### Implementation Idea
In AWS, use `boto3` or AWS SDK to call `s3.getSignedUrl('putObject', ...)`. The frontend uses standard `fetch` or `axios` to PUT the file to that URL.

### Interview Question
"Design a system to allow users to upload high-res images to an Instagram clone. How does the upload process work?"

### Follow-up
"How do you prevent malicious users from uploading executable scripts (.php / .exe) disguised as images?"

### Common Mistake
Designing the system so the client uploads the file to the web server, which then uploads it to S3. This doubles the network bandwidth used and severely bottlenecks the web server.

---

## #25. SQL Injection & Threat Modeling [Type C — Debugging Scenario]

### What is it?
SQL Injection is a vulnerability where an attacker manipulates application input to execute malicious SQL statements. Threat Modeling is the structural process of identifying these types of risks during system design.

### Mental Model
It's like writing a check to "Cash" and hoping no one steals it. The attacker intercepts the form and changes the instructions from "View My Profile" to "View Everyone's Passwords."

### Why does it exist?
Because of string concatenation. If code builds queries by blindly combining strings (`"SELECT * FROM users WHERE email = '" + input + "'"`), the input can alter the structure of the SQL command.

### Real-World Example
An attacker enters `admin@mail.com' OR '1'='1` in a login field.
The query becomes: `SELECT * FROM users WHERE email = 'admin@mail.com' OR '1'='1'`.
Since `1=1` is always true, the attacker logs in as the admin without a password.

### Architecture / Raw Diagram
```text
MALICIOUS INPUT 
      ↓
[ API Server ] (Naive String Concatenation)
      ↓
[ Database ] (Executes malicious logic -> Drops Table)
```

### Data Flow
N/A

### When Would I Use It?
- Security MUST be considered in every system design interview, specifically when discussing database interactions and API input.

### When Would I NOT Use It?
- N/A.

### Trade-offs
- **What do I gain (by fixing it)?** Total immunity to SQL injection.
- **What do I sacrifice?** Nothing. Using prepared statements is faster and more secure.

### Implementation Idea
**Never use string concatenation.** Always use Parameterized Queries (Prepared Statements) or an ORM.
Bad: `db.query(`SELECT * FROM users WHERE id = ${req.params.id}`)`
Good: `db.query('SELECT * FROM users WHERE id = $1', [req.params.id])`
The database engine treats `$1` strictly as a literal value, never as executable code.

### Interview Question
"In your system architecture, how do you ensure the database is protected against SQL injection?"

### Follow-up
"Does input validation (like regex checking for emails) completely prevent SQL injection? Why or why not?" (Answer: No, it is a secondary defense. An attacker can often bypass regex. Parameterized queries are the only primary defense).

### Common Mistake
Believing that escaping quotes or sanitizing input is the best way to prevent SQL injection. It is deeply flawed. Parameterized queries are the industry standard.

---
*(End of Part 2)*
