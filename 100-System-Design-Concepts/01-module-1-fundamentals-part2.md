# MODULE 1 — CONCEPTS 1–50 (PART 2: 13-25)

## #13. Load Balancer [Type A — Concept]

### What is it?
A server or service that sits in front of your application servers and distributes incoming network traffic across multiple backend servers to ensure no single server is overwhelmed.

### Mental Model
A traffic cop at a busy intersection. Instead of letting all cars jam into one lane, the cop directs cars evenly across 5 different lanes so traffic flows smoothly.

### Why does it exist?
If you horizontally scale your API (Concept #3) to have 10 servers, the client (mobile app) needs to know which server to talk to. A load balancer provides a single public IP address, taking the request and forwarding it to one of the 10 private servers.

### Real-World Example
**AWS Application Load Balancer (ALB):** Routes HTTP/HTTPS traffic. If an EC2 instance crashes, the ALB detects the failure via a "Health Check" and stops sending traffic to that dead instance, preventing user errors.

### Architecture / Raw Diagram
```text
           [ Client ]
               │
               v
       [ Load Balancer ]
       /       |       \
      v        v        v
  [ API 1 ] [ API 2 ] [ API 3 ]
```

### Data Flow
1. Client sends request to `api.myapp.com`.
2. DNS resolves to the Load Balancer's IP.
3. Load Balancer receives request, checks its routing algorithm (e.g., Round Robin).
4. Forwards request to API 2.
5. API 2 replies to Load Balancer, which replies to Client.

### When Would I Use It?
- Any system with more than one application server.
- To terminate SSL/HTTPS (so backend servers don't have to waste CPU decrypting traffic).

### When Would I NOT Use It?
- Local development or single-server MVPs.

### Trade-offs
- **What do I gain?** Scalability, high availability (removes API SPOF), and security (hides internal IPs).
- **What do I sacrifice?** Introduces a new SPOF if the load balancer itself isn't redundant. Minor network latency (one extra hop).

### Implementation Idea
For a small project, install **Nginx** or **HAProxy** on a server and configure it to reverse-proxy traffic to your Node apps.
For production, use managed cloud services: **AWS ALB**, **Cloudflare**, or **GCP Load Balancer**.

### Interview Question
"How does a load balancer decide which server to send traffic to?"

### How to Answer
**The 'Think' Process:** Mention the most common algorithm first (Round Robin), then detail more advanced algorithms based on specific needs.
**The Answer:** "There are several routing algorithms. The simplest is **Round Robin**, which just goes down the list of servers sequentially. If servers have different hardware capabilities, we use **Weighted Round Robin** to send more traffic to the stronger machines. For stateful applications, we might use **IP Hashing** to ensure a specific user's IP is always routed to the same server. Finally, **Least Connections** is used to route traffic to the server that currently has the fewest active requests."

### Follow-up
"What happens if one of the backend servers suddenly crashes?"

### How to Answer (Follow-up)
**The 'Think' Process:** Explain Health Checks.
**The Answer:** "The Load Balancer continuously pings the backend servers using a Health Check (e.g., requesting `/api/health` every 5 seconds). If a server crashes and fails the health check, the Load Balancer temporarily removes it from the routing pool. Future traffic is instantly redistributed among the surviving servers."

### Common Mistake
Thinking a load balancer automatically scales your backend servers. A load balancer only *routes* traffic. An Auto Scaling Group is what physically adds more servers.

---

## #14. API Gateway [Type A — Concept]

### What is it?
An API Gateway is a specific type of reverse proxy/load balancer sitting between clients and backend microservices. It acts as the single entry point and handles cross-cutting concerns like Auth, Rate Limiting, and Request Routing.

### Mental Model
The front desk receptionist at a massive corporate office building. You can't just walk into the HR department (a microservice). You give your ID to the receptionist (Gateway). They verify who you are, check if you have an appointment, and then point you to the right elevator.

### Why does it exist?
In a microservices architecture, you don't want to write JWT validation, CORS, and Rate Limiting code 50 times for 50 different microservices. You extract that logic into the Gateway.

### Real-World Example
**Netflix Zuul / AWS API Gateway:** Takes a request for `/movies/123`, validates the user's subscription token, and routes the request internally to the Movie Microservice.

### Architecture / Raw Diagram
```text
           [ Client ]
               │
               v
       [ API Gateway ] (Auth, Rate Limiting, Logging)
       /       |       \
      v        v        v
   [ Auth ] [ Billing ] [ Feed ] (Microservices)
```

### Data Flow
1. Client requests `/billing/invoice`.
2. API Gateway intercepts. Validates JWT. Checks Rate Limit in Redis.
3. If valid, routes request to internal Billing Service via gRPC or HTTP.
4. Billing Service returns data. Gateway forwards it back to client.

### When Would I Use It?
- When migrating from a monolith to microservices.
- When exposing multiple microservices to public external clients.

### When Would I NOT Use It?
- In a simple monolith architecture. A standard Load Balancer (Nginx) is sufficient.

### Trade-offs
- **What do I gain?** Centralized security and simplified microservice code.
- **What do I sacrifice?** It can become a monolithic bottleneck. If the Gateway is slow or goes down, the entire microservice ecosystem is unreachable.

### Implementation Idea
Use **Kong**, **Tyk**, or **AWS API Gateway**. Configure rules: if route matches `/users/*`, forward to `internal-users-svc:8080`.

### Interview Question
"In a microservices architecture, how do you handle authentication without duplicating logic in every service?"

### How to Answer
**The 'Think' Process:** Explain the extraction of cross-cutting concerns into a gateway layer.
**The Answer:** "I would use an API Gateway. Instead of having the Billing service, the Inventory service, and the User service all independently validate JWTs, I would centralize that logic at the Gateway. The API Gateway intercepts every incoming request, validates the token against the Auth service or the JWT signature, and only if valid, routes the request to the downstream microservice. We can also centralize rate limiting and SSL termination here."

### Follow-up
"What is the 'BFF' (Backend-for-Frontend) pattern in relation to API Gateways?"

### How to Answer (Follow-up)
**The 'Think' Process:** Explain that different clients (Mobile vs Web) need different data.
**The Answer:** "The BFF pattern is where we create a specific API Gateway for a specific type of client. For example, a Mobile app might need smaller, aggregated data payloads to save battery and network bandwidth, while a Web dashboard needs heavy, detailed payloads. Instead of one massive API Gateway trying to serve everyone, we create a 'Mobile BFF Gateway' and a 'Web BFF Gateway' tailored to those specific clients."

### Common Mistake
Confusing a Load Balancer with an API Gateway. An L4 Load Balancer just moves TCP packets. An API Gateway (L7) actually reads the HTTP path, checks headers, transforms JSON, and executes logic.

---

## #15. Webhooks vs Polling [Type D — Trade-off Scenario]

### What is it?
Methods for a client to know when a long-running background task on a server is finished.
- **Polling:** The client asks the server repeatedly (every 3 seconds), "Are you done yet?"
- **Webhooks:** The client tells the server, "I'll wait here. Call my API endpoint when you are done."

### Mental Model
Polling = Kids in the backseat asking "Are we there yet?" every 5 minutes.
Webhook = Parents telling the kids, "Go to sleep, I will wake you up when we arrive."

### Why does it exist?
HTTP is synchronous (request -> wait -> response). If a task takes 10 minutes (like generating a massive PDF report), the HTTP connection will timeout. You need async communication.

### Real-World Example
**Stripe Payments:** When a customer pays, it might take their bank a minute to confirm. Instead of your server polling Stripe forever, you give Stripe a Webhook URL (`https://your-api.com/stripe-webhook`). Stripe sends an HTTP POST there when the payment clears.

### Architecture / Raw Diagram
```text
POLLING:
Client ─(Done?)─> Server (No)
Client ─(Done?)─> Server (No)
Client ─(Done?)─> Server (Yes)

WEBHOOK:
Server ──(Task Done Event)──> POST /client/webhook
```

### Data Flow
**Webhook Flow:**
1. You register `https://api.com/hook` in a 3rd party dashboard.
2. 3rd party background task finishes.
3. 3rd party makes an outbound POST request to your URL containing the event data (e.g., `{"status": "paid"}`).

### When Would I Use It?
- **Webhooks:** When integrating with third-party SaaS platforms (GitHub, Stripe, Twilio).
- **Polling:** When integrating with legacy systems that cannot make outbound HTTP requests, or simple frontend progress bars.

### When Would I NOT Use It?
- Do not use polling if you have thousands of clients; it will accidentally DDoS your server.

### Trade-offs
- **Polling:** Extremely easy to implement. BUT wastes massive amounts of server CPU and network bandwidth answering "No."
- **Webhooks:** Instantaneous and highly efficient. BUT your webhook endpoint must be highly available on the public internet and secured against fake requests.

### Implementation Idea
Build an Express endpoint: `app.post('/webhook', (req, res) => { ... })`. Ensure you return `200 OK` immediately to the provider, then process the webhook payload asynchronously.

### Interview Question
"You need to integrate with a background video encoding service. Should your server poll them for the status, or use a webhook?"

### How to Answer
**The 'Think' Process:** Highlight efficiency and resource waste.
**The Answer:** "I would definitely use a Webhook. Video encoding takes an unpredictable amount of time, often minutes. If we use polling, our server wastes CPU and network bandwidth constantly asking for updates, and if we poll too slowly, we introduce artificial latency. By exposing a Webhook endpoint, the encoding service will simply POST the final video URL to our server the exact millisecond it finishes, which is perfectly efficient."

### Follow-up
"How do you secure a webhook endpoint so a malicious user doesn't just POST fake 'Payment Success' events to your server?"

### How to Answer (Follow-up)
**The 'Think' Process:** Explain cryptographic signatures.
**The Answer:** "We cannot rely on just hiding the URL. Instead, we use cryptographic signatures. The provider (like Stripe) generates a signature using a shared secret key and places it in the HTTP headers. When our webhook endpoint receives the payload, it hashes the payload with our secret key. If our generated hash matches their header signature, we know the request legitimately came from the provider and wasn't tampered with."

### Common Mistake
Processing heavy logic synchronously inside the webhook endpoint. If you take 10 seconds to respond to the webhook, the provider thinks you crashed and will retry, causing duplicate processing.

---

## #16. Synchronous vs Asynchronous Processing [Type A — Concept]

### What is it?
- **Synchronous:** The client waits blocking the thread until the server finishes the task and returns a response.
- **Asynchronous:** The server accepts the request, returns a generic "Accepted" response immediately, and processes the heavy task in the background.

### Mental Model
Synchronous = Ordering coffee. You stand at the counter and wait until the barista hands you the cup.
Asynchronous = Ordering a customized pizza. You pay, get a receipt with an order number, and go sit down. They call your number later when it's done.

### Why does it exist?
Web servers are optimized for fast response times (< 200ms). Heavy tasks (image processing, sending emails, generating reports) take seconds or minutes. If done synchronously, the server thread freezes and connections drop.

### Real-World Example
**Email Registration:** When a user signs up, returning the webpage instantly is critical. Sending the "Welcome Email" is done asynchronously by a background worker so the user doesn't wait 3 seconds for the SMTP server.

### Architecture / Raw Diagram
```text
SYNC:
[ Client ] ─> (Waits 5 secs) ─> [ API ] (Generates PDF)

ASYNC:
[ Client ] ─> (Returns 202 instantly) ─> [ API ] ─> [ Queue ]
                                                       │
                                                   [ Worker ] (Generates PDF)
```

### Data Flow
**Async Flow:**
1. Client HTTP POSTs a task.
2. API validates data, writes task to a DB or Kafka Queue, and immediately returns `HTTP 202 Accepted` with a JobID.
3. Background worker pulls from Queue, runs the heavy task.

### When Would I Use It?
- Any task that takes longer than ~500ms to execute.
- Interacting with slow third-party APIs.

### When Would I NOT Use It?
- When the client absolutely needs the data right now to render the next UI screen (e.g., logging in to get a JWT).

### Trade-offs
- **Async:** Incredible UI responsiveness and backend scalability. BUT introduces significant architectural complexity (Queues, Workers, WebSockets for notification).

### Implementation Idea
Use **RabbitMQ** or **BullMQ** (Redis). The API pushes a JSON payload to the queue. A separate Node.js worker process consumes the queue.

### Interview Question
"Users are complaining that when they click 'Export 50-page PDF', the website freezes for a minute and sometimes crashes. How do you fix this?"

### How to Answer
**The 'Think' Process:** Identify the blocking nature of the heavy task and propose decoupling via a message queue.
**The Answer:** "The API is trying to generate the PDF synchronously. If it takes a minute, the browser connection times out, and the server thread is blocked from serving other users. I would refactor this to an Asynchronous architecture. When the user clicks Export, the API will push a task to a Message Queue like RabbitMQ and immediately return a 'Job Started' status. A separate pool of background Worker nodes will pick up the task, generate the PDF, and save it to S3. We can then notify the user via WebSockets or email when the file is ready."

### Follow-up
"What HTTP status code should the API return when it pushes the task to the queue?"

### How to Answer (Follow-up)
**The 'Think' Process:** Know your REST status codes. It's not 200 OK because the work isn't done.
**The Answer:** "The API should return HTTP 202 Accepted. This specifically indicates that the request was received and understood, but the processing has not yet been completed."

---

## #17. Authentication vs Authorization (AuthN vs AuthZ) [Type A — Concept]

### What is it?
- **Authentication (AuthN):** Verifying *who* you are.
- **Authorization (AuthZ):** Verifying *what you are allowed to do*.

### Mental Model
Authentication = Presenting your passport to the TSA agent to prove you are John Doe.
Authorization = Presenting your boarding pass at the gate. It proves you are allowed to get on Flight 101, but does not allow you to board Flight 202.

### Why does it exist?
Security requires both. You can be a fully authenticated user of AWS, but you are not authorized to delete someone else's EC2 instance.

### Real-World Example
Logging into a company dashboard verifies your identity (AuthN). Trying to click the "Delete User" button checks if your role is 'Admin' (AuthZ).

### Architecture / Raw Diagram
```text
           [ Client ]
               │
(1. Logs in)   v
           [ AuthN API ] ─> (Verifies Password) ─> Returns JWT
               │
(2. DELETE /)  v
           [ AuthZ API ] ─> (Verifies 'Admin' Role) ─> Executes Delete
```

### Data Flow
1. AuthN: Client sends Email/Password. Server checks DB hash. Returns JWT.
2. AuthZ: Client sends JWT to DELETE endpoint.
3. Server decodes JWT, checks if `role === 'admin'`.

### When Would I Use It?
- Every secure system on the internet.

### When Would I NOT Use It?
- Completely public read-only APIs (like a public weather API).

### Trade-offs
- Implementing granular AuthZ (like Role-Based Access Control or Attribute-Based Access Control) requires complex database modeling.

### Implementation Idea
**AuthN:** Use bcrypt to hash passwords. Never store plain text.
**AuthZ:** Middleware in Express: `function requireAdmin(req, res, next) { if(req.user.role !== 'admin') return res.status(403); next(); }`

### Interview Question
"What is the difference between Authentication and Authorization?"

### How to Answer
**The 'Think' Process:** Use a simple analogy (like airports or buildings) to make the distinction clear.
**The Answer:** "Authentication (AuthN) is verifying the identity of a user—proving *who* they are, usually via a username and password or MFA. Authorization (AuthZ) happens after authentication; it determines *what* that specific user is allowed to do within the system based on their roles or permissions. For example, a standard user is authenticated to log into an app, but they are not authorized to access the admin billing dashboard."

### Follow-up
"What HTTP status codes correspond to a failure in AuthN versus AuthZ?"

### How to Answer (Follow-up)
**The 'Think' Process:** Know the 400-level errors. 401 vs 403.
**The Answer:** "If a user fails Authentication (e.g., bad password or expired token), the server returns HTTP 401 Unauthorized. If they pass Authentication but fail Authorization (e.g., trying to access a restricted admin page), the server returns HTTP 403 Forbidden."

---

## #18. JWT (JSON Web Tokens) [Type B — Practical Design]

### What is it?
A compact, URL-safe string used to represent claims securely between two parties. It allows the server to verify the user without looking them up in a database.

### Mental Model
A JWT is like a driver's license. The police officer (API) doesn't need to call the DMV (Database) to verify you. They just look at the holographic seal (Cryptographic Signature) on the card to know it hasn't been forged.

### Why does it exist?
Traditional sessions require the server to store a Session ID in RAM. In horizontal scaling, Server A doesn't know about Server B's RAM (Stateful problem). JWTs contain the user data inside the token itself, making the API purely Stateless.

### Real-World Example
An API Gateway decodes the JWT `eyJhb...`, reads `{"user_id": 5, "role": "admin"}`, and verifies the cryptographic signature using its secret key. It routes the request without ever querying the database.

### Architecture / Raw Diagram
```text
           [ Client ]
               │
  (POST /login)│
               v
       [ Auth Server ] ─> (Signs JSON with Secret Key) ─> Returns JWT
               │
  (GET /data)  │
      + JWT    v
         [ API Node ] ─> (Verifies Signature using Secret Key) ─> Serves Data
```

### Data Flow
1. Login succeeds. Server creates JSON: `{id: 1}`.
2. Server hashes JSON using a Secret Key (e.g., "my_secret").
3. Server returns Token (Header.Payload.Signature).
4. Client sends Token in `Authorization: Bearer <token>` header.
5. Server recalculates hash using "my_secret". If it matches the signature, it's valid.

### When Would I Use It?
- Standard stateless authentication for microservices and SPAs (React/Vue).

### When Would I NOT Use It?
- Do not use JWTs for highly sensitive sessions that you might need to revoke instantly (like a banking session). Because JWTs are stateless, you cannot "delete" a JWT from the server; it remains valid until its expiration time.

### Trade-offs
- **What do I gain?** Pure stateless scaling. Database is spared from authentication read queries.
- **What do I sacrifice?** Revocation is very difficult. If a hacker steals a JWT, they have access until it expires. (Requires complex "Token Blacklists" to mitigate).

### Implementation Idea
Use the `jsonwebtoken` npm package. Keep expiration times short (e.g., 15 minutes) and issue a "Refresh Token" (stored securely in an HttpOnly cookie) to get new JWTs.

### Interview Question
"Why are JWTs preferred over traditional session cookies for microservice architectures?"

### How to Answer
**The 'Think' Process:** Contrast Stateful sessions with Stateless JWTs in a distributed environment.
**The Answer:** "Traditional sessions are stateful. The server stores the session ID in memory or a database. In a microservices architecture with hundreds of servers, looking up that session in a central database on every single API request creates a massive bottleneck. JWTs are stateless. The user's identity and roles are embedded directly into the token's payload, and it is cryptographically signed. Any microservice can verify the signature using a shared secret or public key without ever querying the database, allowing for infinite horizontal scaling."

### Follow-up
"If a user clicks 'Log out', how do you invalidate their JWT on the server side?"

### How to Answer (Follow-up)
**The 'Think' Process:** Highlight the primary flaw of JWTs: they cannot be easily revoked.
**The Answer:** "Because JWTs are stateless, you cannot simply 'delete' them on the server. The token will technically remain mathematically valid until its expiration time. The best practice is to use very short expiration times (e.g., 15 minutes). For immediate revocation, we would have to build a Token Blacklist in a fast cache like Redis, and the API gateway would have to check the blacklist on every request—which somewhat defeats the stateless advantage of the JWT."

---

## #19. OAuth 2.0 & SSO [Type A — Concept]

### What is it?
- **OAuth 2.0:** An industry-standard authorization framework allowing a user to grant a third-party application limited access to their resources on another site without sharing their password.
- **SSO (Single Sign-On):** Allows a user to log in once (e.g., using Google) and access multiple independent applications.

### Mental Model
OAuth = Giving the valet the "Valet Key" to your car. They can drive it to park, but the key cannot open the trunk or glovebox. You granted limited access without giving them your master key.

### Why does it exist?
Users hate making new accounts. And as a developer, you don't want the liability of storing thousands of passwords securely. Let Google/Microsoft handle it.

### Real-World Example
**"Log in with Google":** When you use this on a random website, you are redirected to Google. You tell Google, "Yes, this website can see my email address." Google sends the website an Access Token. The website never sees your Google password.

### Architecture / Raw Diagram
```text
(1) User clicks "Login with GitHub"
    Client ────────> [ GitHub Auth Page ]
                         │
(2) User approves ───────┘
                         │ (3. Returns Authorization Code)
    Client <─────────────┘
      │
(4) Client sends Code to Backend
      v
 [ Backend ] ──(5. Exchanges Code for Access Token)──> [ GitHub API ]
```

### Data Flow
1. Client redirects to OAuth Provider.
2. User approves. Provider redirects back with an Auth Code in URL.
3. Your Backend takes the code, calls the Provider server-to-server, and gets a Token.
4. Your Backend uses the Token to fetch the user's email.
5. Your Backend generates its own JWT for the user.

### When Would I Use It?
- Corporate internal tools (Okta/SSO).
- Consumer apps to reduce login friction.

### When Would I NOT Use It?
- Systems restricted by government compliance that require entirely isolated, proprietary auth systems.

### Trade-offs
- **What do I gain?** High security, zero password liability, high user conversion rate.
- **What do I sacrifice?** Reliance on a third party. If Google Auth goes down, nobody can log into your app.

### Implementation Idea
Use **Passport.js** in Node, or managed services like **Auth0**, **Clerk**, or **AWS Cognito** which abstract the entire OAuth flow into a few lines of code.

### Interview Question
"Explain how the OAuth 2.0 Authorization Code flow works when a user clicks 'Log in with Google'."

### How to Answer
**The 'Think' Process:** Break it down into three parties: the User, your App, and Google. Walk through the redirect flow.
**The Answer:** "First, our app redirects the user to Google's consent page. The user logs into Google and approves the permissions. Google then redirects the user back to our app with a temporary 'Authorization Code' in the URL. Our frontend sends this code to our backend. Our backend then makes a secure, server-to-server request to Google, exchanging the code and our client secret for an Access Token. Finally, our backend uses that Access Token to fetch the user's email from Google's API, creates a local account if needed, and issues our own JWT to the client."

### Follow-up
"Why doesn't Google just return the Access Token in the initial redirect URL directly to the browser?"

### How to Answer (Follow-up)
**The 'Think' Process:** Explain security against browser-level theft.
**The Answer:** "That would be the 'Implicit Flow', which is deprecated because it is insecure. If the Access Token is returned directly in the URL to the frontend, it can be easily stolen via browser history, malicious browser extensions, or XSS attacks. By returning a temporary code instead, the actual Access Token is securely retrieved back-channel by our backend server, keeping it hidden from the browser."

---

## #20. Long Polling vs WebSockets vs SSE [Type D — Trade-off Scenario]

### What is it?
Different network protocols for real-time communication between client and server.
- **Long Polling:** Client asks server for data. Server holds the connection open until data is ready, then responds. Client immediately asks again.
- **WebSockets:** A persistent, bi-directional TCP connection. Both client and server can send data at any time.
- **SSE (Server-Sent Events):** A persistent, one-directional HTTP connection where the server streams updates to the client.

### Mental Model
Long Polling = Keeping the phone ringing until someone answers.
WebSockets = A walkie-talkie channel left open. Both sides can speak anytime.
SSE = A radio broadcast. You tune in and listen to the DJ, but you can't talk back.

### Why does it exist?
Standard HTTP is request/response. It cannot handle a server needing to push a notification to a client unprompted (like a new chat message).

### Real-World Example
**Multiplayer Game (WebSockets):** Bi-directional, sub-10ms latency needed.
**Stock Ticker (SSE):** The server streams stock prices down to the browser. The browser doesn't need to send data back.

### Architecture / Raw Diagram
```text
WEBSOCKETS (Bi-directional)
[ Client ] <════════════════> [ Server ]

SSE (Uni-directional stream)
[ Client ] <───────────────── [ Server ]
```

### Data Flow
**WebSocket Flow:**
1. Client sends HTTP Upgrade request.
2. Server accepts, upgrades to `ws://`.
3. Connection stays open. Server pushes "New Message". Client pushes "Typing...".

### When Would I Use It?
- **WebSockets:** Chat apps, live multiplayer games, collaborative editing.
- **SSE:** Live news feeds, LLM text streaming (ChatGPT typing effect).
- **Long Polling:** Legacy environments where firewalls block WebSockets.

### When Would I NOT Use It?
- Don't use WebSockets for standard CRUD operations; it breaks standard REST routing and caching.

### Trade-offs
- **WebSockets:** True real-time bi-directional. BUT highly stateful. Load balancing WebSockets requires sticky sessions and complex infrastructure.
- **SSE:** Uses standard HTTP, easy to load balance. BUT strictly one-way (server to client).

### Implementation Idea
Use `Socket.io` for WebSockets. Use the native `EventSource` browser API for SSE.

### Interview Question
"You are building a live dashboard displaying server metrics. Do you use WebSockets or Server-Sent Events (SSE)?"

### How to Answer
**The 'Think' Process:** Identify if the data needs to go both ways or just one way.
**The Answer:** "I would use Server-Sent Events (SSE). A metrics dashboard is primarily a one-way data flow: the server is constantly streaming updates down to the client. SSE is perfectly suited for this because it works over standard HTTP, making it easier to cache, load-balance, and scale than WebSockets. WebSockets are bi-directional and introduce unnecessary stateful complexity when the client doesn't need to send high-frequency messages back to the server."

### Follow-up
"How do you handle scaling WebSockets across multiple servers? If User A is connected to Server 1, and User B is connected to Server 2, how do they chat?"

### How to Answer (Follow-up)
**The 'Think' Process:** You need a centralized message broker to connect the stateful servers.
**The Answer:** "Because WebSockets are stateful, Server 1 has no idea that User B exists on Server 2. To solve this, we introduce a Pub/Sub message broker like Redis. When User A sends a message to User B, Server 1 publishes that message to the Redis channel. Server 2, which is subscribed to that channel, receives the message and pushes it down the open WebSocket connection to User B."

---

## #21. TCP vs UDP [Type A — Concept]

### What is it?
The two foundational network protocols of the internet (Layer 4).
- **TCP:** Reliable, ordered, checks for errors (requires a handshake).
- **UDP:** Unreliable, unordered, fire-and-forget (no handshake).

### Mental Model
TCP = Sending a registered letter. The postman makes them sign for it. If it gets lost, you send another.
UDP = Throwing a paper airplane. It might hit the target, it might crash. You don't care, you just throw fast.

### Why does it exist?
Not all data needs 100% perfection. If you are downloading a bank statement, losing 1 byte ruins the file (use TCP). If you are on a Zoom call, losing 1 frame of video doesn't matter (use UDP).

### Real-World Example
**HTTP/Web Browsing:** Uses TCP.
**Voice over IP (Discord) / Video Streaming:** Uses UDP to avoid lag.

### Architecture / Raw Diagram
```text
TCP: 
Client ─(SYN)─> Server
Client <─(ACK)─ Server (Handshake complete, safe to send data)

UDP:
Client ─(Data!)─> Server
Client ─(Data!)─> Server (No handshake, no confirmation)
```

### Data Flow
N/A (Fundamental networking concept).

### When Would I Use It?
- **TCP:** File transfers, database connections, REST APIs, emails.
- **UDP:** Live video streaming, fast-paced multiplayer gaming (FPS), DNS lookups.

### When Would I NOT Use It?
- Do not use UDP for anything requiring guaranteed delivery (like a payment request).

### Trade-offs
- **TCP:** Guarantees delivery and order. BUT the 3-way handshake adds latency, and if a packet drops, it stops everything to resend it (Head-of-line blocking).
- **UDP:** Lightning fast, zero overhead. BUT packets can arrive out of order, corrupted, or not at all.

### Implementation Idea
As a high-level software engineer, you rarely write raw TCP/UDP sockets. You use HTTP (built on TCP) or WebRTC (built on UDP).

### Interview Question
"Why do multiplayer games like Call of Duty use UDP instead of TCP?"

### How to Answer
**The 'Think' Process:** Highlight the negative impact of TCP's reliability on real-time performance.
**The Answer:** "Fast-paced multiplayer games require the absolute lowest latency possible. TCP guarantees delivery; if a packet containing a player's coordinates is dropped, TCP pauses all subsequent data to wait for the dropped packet to be re-transmitted. In a shooter game, by the time the dropped packet arrives, the data is useless because the player has already moved. UDP simply fires the data without checking for receipt. If a packet drops, the game just interpolates the movement and uses the next UDP packet, preventing game-breaking lag."

### Follow-up
"HTTP/3 is actually built on UDP instead of TCP. Why?"

### How to Answer (Follow-up)
**The 'Think' Process:** Mention TCP's specific flaw: Head-of-line blocking.
**The Answer:** "HTTP/3 uses a protocol called QUIC, which runs over UDP, to solve TCP's 'Head-of-Line Blocking' problem. In TCP, if you are downloading 5 images simultaneously over one connection and a packet for Image 1 drops, TCP halts the delivery of Images 2, 3, 4, and 5 until Image 1 is retransmitted. QUIC implements its own reliability on top of UDP, so if Image 1 drops, the other images continue downloading without interruption, massively speeding up page loads on poor networks."

---

## #22. DNS (Domain Name System) [Type A — Concept]

### What is it?
The phonebook of the internet. It translates human-readable domain names (google.com) into machine-readable IP addresses (142.250.190.46).

### Mental Model
Like a Contacts app on your phone. You don't memorize your friend's 10-digit phone number; you just tap their name. The app does the translation behind the scenes.

### Why does it exist?
Humans cannot remember IP addresses. Furthermore, IPs change constantly as servers scale or migrate. DNS allows the IP to change while the domain remains the same.

### Real-World Example
When you type `netflix.com`, your browser asks a DNS server for the IP. The DNS server checks its records and returns `54.23.11.90`. Your browser then makes the actual TCP connection to that IP.

### Architecture / Raw Diagram
```text
[ Browser ] ─(1. What is API.com?)─> [ DNS Resolver ]
                                           │
                                     (2. It is 1.2.3.4)
                                           v
[ Browser ] ───(3. HTTP GET)───────> [ Load Balancer (1.2.3.4) ]
```

### Data Flow
1. Browser checks local cache.
2. If miss, asks ISP's DNS resolver.
3. Resolver asks Root Server -> TLD Server (.com) -> Authoritative Server.
4. Authoritative Server returns the IP.

### When Would I Use It?
- Understanding how traffic reaches your Load Balancer.
- Setting up Geo-Routing (routing European users to an EU data center).

### When Would I NOT Use It?
- Internal microservice-to-microservice communication within the same Kubernetes cluster often bypasses external DNS in favor of internal service discovery.

### Trade-offs
- **DNS Caching:** DNS records have a TTL (Time to Live). High TTL means faster resolving (cached), but if your server crashes and you change your IP, it will take hours for the world's caches to update.

### Implementation Idea
Use **AWS Route 53** or **Cloudflare**. You can configure "Weighted Routing" in DNS to send 90% of traffic to your old server and 10% to your new server for a Canary Deployment.

### Interview Question
"What happens between typing www.google.com in your browser and the page appearing? Focus on the network layer."

### How to Answer
**The 'Think' Process:** Summarize the DNS resolution, then the TCP handshake, then the HTTP request.
**The Answer:** "First, the browser checks its local cache to see if it knows the IP for google.com. If not, it makes a UDP request to a DNS resolver to fetch the IP address. Once the IP is resolved, the browser initiates a TCP 3-way handshake with Google's Load Balancer. Since it's HTTPS, a TLS handshake also occurs to establish a secure connection. Finally, the browser sends the HTTP GET request over this secure connection, the server processes it, and returns the HTML payload."

### Follow-up
"How can DNS be used to improve latency for global users?"

### How to Answer (Follow-up)
**The 'Think' Process:** Explain Geo-DNS.
**The Answer:** "We can use Geo-Routing at the DNS level (like AWS Route 53). When a user in Tokyo asks the DNS server for the IP of our application, the DNS server looks at the origin of the request and returns the IP address of our Tokyo data center, rather than our New York data center. This ensures the user's traffic takes the shortest physical path, drastically reducing latency."

---

## #23. SSL/TLS (HTTPS) [Type A — Concept]

### What is it?
Cryptographic protocols designed to provide secure communication over a computer network. It encrypts data so interceptors cannot read it (ensuring Privacy) and verifies the server is who it claims to be (ensuring Authenticity).

### Mental Model
Like sending a letter in an unbreakable steel lockbox. Only you and the recipient have the key. Even if the mailman steals the box, they can't see the letter inside.

### Why does it exist?
Standard HTTP sends data in plain text. If you log in at a coffee shop on public Wi-Fi, anyone can sniff the airwaves and read your password. TLS encrypts it into gibberish.

### Real-World Example
**E-commerce:** When sending credit card details to Stripe, TLS ensures that intermediate routers between your computer and Stripe's servers cannot steal the card number.

### Architecture / Raw Diagram
```text
           [ Hacker (Packet Sniffer) ] -> Sees: "aj8f39qhf..." (Encrypted)
                      │
Client ────────(TLS Encrypted)────────> Load Balancer
```

### Data Flow
1. **Handshake:** Client and Server exchange public keys and agree on a shared encryption algorithm.
2. **Key Generation:** They establish a shared symmetric "Session Key" for fast encryption.
3. **Transmission:** All HTTP data is encrypted using this Session Key.

### When Would I Use It?
- Every public-facing API or website. It is non-negotiable today.

### When Would I NOT Use It?
- High-performance, secure internal networks (e.g., between your Load Balancer and your API server inside a private AWS VPC), though Zero Trust architectures increasingly require TLS even internally.

### Trade-offs
- **What do I gain?** Absolute data privacy and security.
- **What do I sacrifice?** CPU overhead. Encrypting and decrypting packets consumes CPU power.

### Implementation Idea
**TLS Termination:** Don't write code in Node.js to handle SSL. Let your Load Balancer (AWS ALB, Cloudflare, Nginx) handle the certificates and decryption. The LB receives HTTPS, decrypts it, and forwards plain HTTP to your internal Node API to save CPU.

### Interview Question
"What is SSL Termination and why is it implemented at the Load Balancer level?"

### How to Answer
**The 'Think' Process:** Explain the CPU overhead of cryptography and the benefit of centralization.
**The Answer:** "SSL Termination is the process of decrypting incoming HTTPS traffic at the Load Balancer or API Gateway. We do this for two reasons. First, cryptography is CPU-intensive. By terminating SSL at the Load Balancer, we free up our backend application servers to focus purely on business logic rather than burning CPU cycles decrypting packets. Second, it centralizes certificate management; we only have to install and renew our SSL certificates in one place (the Load Balancer) rather than on 50 different microservices."

### Follow-up
"How does the client actually know the server is truly Google, and not a hacker intercepting the connection?"

### How to Answer (Follow-up)
**The 'Think' Process:** Mention Certificate Authorities (CAs).
**The Answer:** "This is solved by Certificate Authorities (CAs). During the TLS handshake, the server sends a digital certificate. This certificate is cryptographically signed by a trusted CA (like Let's Encrypt or DigiCert). Browsers come pre-installed with the public keys of these trusted CAs. The browser uses the CA's key to verify the signature on the server's certificate. If it matches, the browser trusts that the server is authentic."

---

## #24. Circuit Breaker Pattern [Type E — Implementation Scenario]

### What is it?
A design pattern used in microservices. If a downstream service fails repeatedly, the circuit breaker "trips" and stops sending traffic to it for a cooldown period, failing fast instead of waiting for timeouts.

### Mental Model
Like the electrical circuit breaker in your house. If a microwave draws too much power, the breaker flips to cut the power instantly, preventing the entire house from burning down.

### Why does it exist?
To prevent **Cascading Failures**. If the Payment Service is slow and taking 10 seconds to respond, and the Order Service keeps waiting for it, the Order Service will soon run out of threads/memory and crash too, taking down the whole app.

### Real-World Example
If Netflix's "Recommendation Engine" microservice crashes, the API Gateway's Circuit Breaker trips. Instead of letting the Netflix homepage timeout and break, it instantly returns a hardcoded default list of "Trending Now" movies.

### Architecture / Raw Diagram
```text
           [ Order API ] 
                 │
           (Circuit Breaker)
          Closed (Normal) -> Opens if 50% errors in 10s
                 │
           [ Payment API ] (Crashing / Slow)
```

### Data Flow
1. Order API calls Payment API.
2. Payment API times out 5 times in a row.
3. Circuit Breaker changes state to "OPEN".
4. Next request comes in. Circuit Breaker immediately returns an Error or Fallback data, without even attempting to call Payment API.
5. After 30 seconds, it changes to "HALF-OPEN", allows 1 test request through. If success, it closes.

### When Would I Use It?
- Whenever Service A calls Service B over a network.

### When Would I NOT Use It?
- Monoliths (local function calls don't need circuit breakers).

### Trade-offs
- **What do I gain?** Prevents cascading system collapse and improves UX (failing fast is better than hanging forever).
- **What do I sacrifice?** Complexity in configuring the right thresholds (e.g., how many failures should trip the breaker?).

### Implementation Idea
Use libraries like **Opossum** (Node.js) or **Resilience4j** (Java). Wrap your `fetch` calls in the breaker.

### Interview Question
"In a microservices architecture, Service A calls Service B. Service B starts taking 30 seconds to respond. What happens to Service A, and how do you prevent it?"

### How to Answer
**The 'Think' Process:** Identify the "Cascading Failure" and propose the Circuit Breaker.
**The Answer:** "Because Service B is hanging for 30 seconds, Service A's threads will stay open waiting for a response. Eventually, Service A will exhaust its connection pool and memory, causing it to crash as well—this is a classic cascading failure. To prevent this, I would implement the Circuit Breaker pattern. If Service B times out a certain number of times, the circuit breaker 'opens' and stops sending traffic to Service B. Instead of waiting 30 seconds, Service A will instantly fail fast or return fallback data, protecting its own resources."

### Follow-up
"How does the circuit breaker know when it is safe to start sending traffic to Service B again?"

### How to Answer (Follow-up)
**The 'Think' Process:** Explain the Half-Open state.
**The Answer:** "The circuit breaker uses a 'Half-Open' state. After a configured cooldown period (say, 30 seconds), the circuit breaker transitions from Open to Half-Open. In this state, it allows a single test request to pass through to Service B. If that request succeeds, the breaker assumes Service B has recovered, transitions back to 'Closed', and resumes normal traffic. If it fails, it resets the cooldown timer and stays Open."

---

## #25. Idempotency [Type B — Practical Design]

### What is it?
An operation is idempotent if executing it multiple times produces the same result as executing it exactly once.

### Mental Model
Idempotent = Pressing an elevator button. Whether you press it once or 10 times, the elevator is only called once.
Not Idempotent = Paying at a cash register. If you swipe your card 10 times, you get charged 10 times.

### Why does it exist?
Networks are unreliable. If a mobile app sends a "Pay $10" request, and the server processes it but the Wi-Fi drops before the server can reply "Success", the app will retry. Without idempotency, the user gets charged $20.

### Real-World Example
**Stripe API:** When creating a charge, you must pass an `Idempotency-Key` header (e.g., a UUID). If Stripe sees a request with an `Idempotency-Key` it has already processed in the last 24 hours, it ignores the new request and returns the cached success response.

### Architecture / Raw Diagram
```text
(1) Client POSTs /charge with UUID: 123
        ↓
    [ API ] ─(2) Checks DB: UUID 123 exists? 
        ↓ (No)
    Charge Card
        ↓
    Save UUID 123 to DB

(Network drops. Client Retries)

(3) Client POSTs /charge with UUID: 123
        ↓
    [ API ] ─(4) Checks DB: UUID 123 exists?
        ↓ (Yes)
    Return Success immediately (DO NOT charge card)
```

### Data Flow
1. Client generates a unique UUID for the transaction.
2. Server receives request. Looks up UUID in an `Idempotency` DB table.
3. If not found, process transaction, save UUID to table with result, return success.
4. If found, skip processing, return the cached result.

### When Would I Use It?
- Payment gateways, financial transactions, order creation.
- Any API endpoint using `POST` that mutates state. (`GET`, `PUT`, and `DELETE` are naturally idempotent by HTTP definition).

### When Would I NOT Use It?
- Non-critical tracking APIs (e.g., logging a page view). If it's counted twice, it doesn't matter enough to justify the DB overhead.

### Trade-offs
- **What do I gain?** Ironclad data correctness during network retries.
- **What do I sacrifice?** Every write operation now requires an extra read operation to check the idempotency table, adding latency.

### Implementation Idea
Create an `idempotency_keys` table in PostgreSQL. When processing a payment, use a transaction block. Try to `INSERT` the key. If it violates a unique constraint (already exists), abort the payment logic.

### Interview Question
"A user's mobile app loses connection right after hitting 'Submit Order'. The app automatically retries the request, resulting in the user being charged twice. How do you architect a fix for this?"

### How to Answer
**The 'Think' Process:** The core issue is network retries causing duplicate mutations. Idempotency Keys are the industry standard fix.
**The Answer:** "This is a classic problem caused by a lack of API idempotency. To fix it, the mobile client must generate a unique UUID (an Idempotency Key) for the order and send it in the HTTP header. On the backend, before processing the payment, we query a database or Redis to see if we have already successfully processed that specific UUID. If we have, we simply return the cached success response without touching the payment gateway. If we haven't, we process the payment and store the UUID."

### Follow-up
"Where should the Idempotency Key be generated? On the client or the server?"

### How to Answer (Follow-up)
**The 'Think' Process:** If the server generates it, a network failure preventing the key from reaching the client ruins the whole system.
**The Answer:** "It must be generated on the Client. If the server generated it, and the network dropped before the client received it, the client would have to make a brand new request, which the server would treat as a brand new order. By generating it on the client, the UUID is tied to the user's specific action, ensuring that no matter how many times the client retries that action, the server knows it's the exact same intent."

---
*(End of Part 2. I will provide the database concepts next).*
