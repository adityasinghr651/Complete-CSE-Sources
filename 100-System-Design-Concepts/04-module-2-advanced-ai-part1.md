# MODULE 2 — CONCEPTS + APPLICATIONS 51–100 (PART 3: 76-88)

# OBSERVABILITY & OPERATIONS

## #76. Metrics, Monitoring & Alerting [Type A — Concept]

### What is it?
- **Metrics:** Numeric data measured over time (e.g., CPU %, Requests/sec).
- **Monitoring:** The dashboards (Grafana, Datadog) used to visualize those metrics.
- **Alerting:** Automated rules that page a developer (via Slack or PagerDuty) if a metric breaches a threshold (e.g., Error rate > 5%).

### Mental Model
Metrics = The speedometer and fuel gauge in your car.
Monitoring = Looking at the dashboard while driving.
Alerting = The "Check Engine" light turning on and beeping when something is actually broken.

### Why does it exist?
Without it, you are flying blind. The only way you know your system crashed is when angry users tweet about it.

### Real-World Example
**Prometheus & Grafana:** Prometheus scrapes metrics from your microservices every 10 seconds. Grafana visualizes them. If memory usage on the Payments API hits 95%, Prometheus triggers Alertmanager, which calls the on-call engineer's phone.

### Architecture / Raw Diagram
```text
[ API Server ] (Exposes /metrics)
       ^
       │ (Pulls data)
[ Prometheus (Metrics DB) ] ──> [ Grafana (Dashboards) ]
       │
    (Threshold Breached)
       v
[ Alertmanager ] ──> [ PagerDuty / Slack ]
```

### Data Flow
N/A

### When Would I Use It?
- Mandatory for any production system.

### Trade-offs
- **Alert Fatigue:** If you set alerts for non-critical things (like CPU hitting 70% momentarily), developers will ignore the alerts, and eventually miss a real outage. Alerts must be actionable.

### Implementation Idea
Expose a `/metrics` endpoint on your Node.js app using the `prom-client` library. It automatically tracks HTTP request latency and memory usage.

### Interview Question
"Your system went down for 30 minutes before anyone noticed. How do you prevent this from happening again?"

### Follow-up 1:
"What are the 'Four Golden Signals' of monitoring?" (Answer: Latency, Traffic/RPS, Errors, and Saturation/CPU).

### Common Mistake
Monitoring the server, but not the business metrics. If CPU is at 20% (looks healthy) but "Successful checkouts per minute" drops to zero, the system is broken but standard infrastructure alerts won't catch it.

---

## #77. Distributed Tracing [Type A — Concept]

### What is it?
A method to track a single request as it travels across multiple independent microservices.

### Mental Model
Attaching a GPS tracker to a package. As the package moves from the sorting facility, to a truck, to a local post office, the tracker records exactly how much time it spent at each stop.

### Why does it exist?
In a monolith, finding a slow function is easy (use a profiler). In microservices, if a request takes 5 seconds, you have no idea if the delay was in the API Gateway, the Auth Service, the Database, or the network.

### Real-World Example
**Jaeger / Zipkin:** When a user clicks "Buy", the Gateway generates a `Trace-ID`. It passes this ID in the HTTP headers to the Order Service, which passes it to the Payment Service. Jaeger visualizes a waterfall chart showing exactly how many milliseconds the request spent in each service.

### Architecture / Raw Diagram
```text
[ Client ] ─> (Generates TraceID: X123)
                 │
           [ API Gateway ] (Logs: X123 took 10ms)
                 │ (Passes TraceID in Header)
           [ Order Svc ] (Logs: X123 took 100ms)
                 │
           [ Payment Svc ] (Logs: X123 took 4000ms!) -> BOTTLENECK FOUND
```

### Data Flow
N/A

### When Would I Use It?
- Any microservice architecture with more than 3-4 services.

### Trade-offs
- **Overhead:** Generating and shipping trace data adds network overhead. Systems at scale (like Uber) use "sampling"—they only trace 1% of requests to save bandwidth.

### Implementation Idea
Use **OpenTelemetry** in your code. It automatically injects trace IDs into standard HTTP and gRPC libraries.

### Interview Question
"In a system with 20 microservices, a user reports a specific action is very slow. How do you find the bottleneck?"

### Follow-up 1:
"How does a downstream service (like the Database) know which trace a query belongs to?" (Answer: The upstream service must explicitly pass the Trace ID, usually in HTTP headers or gRPC metadata, often called "context propagation").

### Common Mistake
Confusing Logging (recording discrete events) with Tracing (recording the full journey of a request across boundaries).

---

## #78. SLA / SLO / SLI [Type A — Concept]

### What is it?
- **SLI (Service Level Indicator):** A direct measurement (e.g., 99.5% of requests succeed).
- **SLO (Service Level Objective):** Your internal goal (e.g., We *want* 99.9% of requests to succeed).
- **SLA (Service Level Agreement):** The legal contract with customers (e.g., If we drop below 99.0%, we owe you a refund).

### Mental Model
SLI = The speedometer reading 65 mph.
SLO = Your personal goal to drive at 60 mph.
SLA = The legal speed limit; if you go over 70 mph, you get a ticket (financial penalty).

### Why does it exist?
To define what "reliable" actually means. 100% uptime is impossible. Establishing SLOs gives engineering teams an "Error Budget" (acceptable downtime) to ship new features without fearing perfection.

### Real-World Example
**AWS S3 SLA:** Amazon guarantees 99.9% uptime for S3. If uptime drops to 99.0%, you can claim a 10% service credit.

### Architecture / Raw Diagram
N/A

### Data Flow
N/A

### When Would I Use It?
- Defining non-functional requirements at the start of a system design interview (e.g., "What is our target availability SLO?").

### Trade-offs
- Targeting 99.999% (Five Nines) allows only 5 minutes of downtime per *year*. Achieving this requires extreme engineering (multi-region active-active deployments) and costs exponentially more than 99.9% (Three Nines - 8 hours of downtime per year).

### Implementation Idea
Use a monitoring tool to track SLIs (e.g., `(Successful HTTP GETs) / (Total HTTP GETs)`). Set an alert if the SLI drops below the SLO.

### Interview Question
"The product manager asks for 100% availability for a new video streaming service. How do you respond?"

### Follow-up 1:
"What is an Error Budget, and how does it relate to feature deployments?" (Answer: If your SLO is 99.9%, you have a 0.1% error budget. If you haven't used it up this month, you can deploy risky new features. If you have used it up due to outages, you freeze deployments and focus on stability).

### Common Mistake
Over-promising. Never design a system for 100% availability; it forces impossible architectural choices.

---

# REAL-TIME SYSTEMS & DATA PROCESSING

## #79. Batch vs Stream Processing [Type A — Concept]

### What is it?
- **Batch Processing:** Processing a massive volume of static data all at once, usually on a schedule (e.g., a nightly job calculating total daily sales).
- **Stream Processing:** Processing data continuously in real-time as it arrives (e.g., detecting credit card fraud the millisecond the card is swiped).

### Mental Model
Batch = Doing all your laundry on Sunday night.
Stream = Washing each shirt individually the exact second you take it off.

### Why does it exist?
Different business needs. Batch is highly efficient for massive historical data analytics. Stream is necessary when milliseconds matter.

### Real-World Example
**Netflix Analytics:**
Batch: Running a Hadoop job at 3 AM to calculate which shows to recommend to you tomorrow.
Stream: Running Apache Flink on live viewing data to instantly pause a stream if your account is being used in 5 different countries simultaneously.

### Architecture / Raw Diagram
```text
BATCH:
[ DB ] ──(Midnight)──> [ Hadoop/Spark Cluster ] ──> [ Report DB ]

STREAM:
[ Kafka (Live Events) ] ──(Instantly)──> [ Flink/Spark Streaming ] ──> [ Live Dashboard ]
```

### Data Flow
N/A

### When Would I Use It?
- **Batch:** Billing cycles, daily reports, training machine learning models.
- **Stream:** Fraud detection, live dashboards, real-time alerting.

### Trade-offs
- **Batch:** Easy to implement, cheaper compute. BUT data is always hours old.
- **Stream:** Real-time insights. BUT highly complex infrastructure (handling late-arriving data, stateful stream joins).

### Implementation Idea
For stream processing MVP, just use a Node.js worker consuming from a Kafka topic. At massive scale, use **Apache Flink**.

### Interview Question
"Design a system to detect credit card fraud. Should you use batch or stream processing?"

### Follow-up 1:
"What happens if an event in a stream processing pipeline arrives out of order due to network latency?"

### Common Mistake
Using batch processing for a system that requires instant action. A batch job running every hour cannot prevent a fraudulent transaction happening *right now*.

---

## #80. MapReduce / Spark [Type A — Concept]

### What is it?
Programming models for processing massive datasets across a distributed cluster of computers.
- **Map:** Filters/sorts data (e.g., count words in each document).
- **Reduce:** Summarizes results (e.g., aggregate total counts).

### Mental Model
You want to count the number of red M&Ms in 1,000 jars.
Map: You hire 1,000 kids. Each kid counts the red M&Ms in *one* jar.
Reduce: You take the final number from each kid and add them all together to get the total.

### Why does it exist?
A single SQL database cannot run a `GROUP BY` query on 500 Terabytes of log files. MapReduce distributes the computation to the servers where the data is actually stored, processing it in parallel.

### Real-World Example
**Google Search Indexing:** Uses MapReduce to crawl the web, parse the HTML, and build the massive inverted index used for searching.

### Architecture / Raw Diagram
```text
           [ Massive Dataset (HDFS) ]
           /           |            \
       [ Map 1 ]   [ Map 2 ]    [ Map 3 ]  (Filter & format)
           \           |            /
             \         |          /
               [ Reduce Node ]             (Aggregate)
                     │
               [ Final Result ]
```

### Data Flow
N/A

### When Would I Use It?
- Big Data analytics (Data Lakes).
- Generating search indexes.

### Trade-offs
- **Hadoop (MapReduce) vs Spark:** Spark is the modern evolution. Hadoop writes intermediate Map results to disk (slow). Spark keeps intermediate results in RAM (100x faster, but more expensive).

### Implementation Idea
As a backend engineer, you don't usually write raw MapReduce. You write SQL queries in tools like **Snowflake**, **BigQuery**, or **Hive**, which compile your SQL into MapReduce/Spark jobs under the hood.

### Interview Question
"How would you count the frequency of every word in a 10 Petabyte text file?"

### Follow-up 1:
"Why is Apache Spark generally preferred over traditional Hadoop MapReduce?"

### Common Mistake
Suggesting a standard PostgreSQL database or a simple Node.js `for` loop to process Terabytes of analytical data.

---

# L. AI SYSTEM DESIGN (Core)

## #81. LLM APIs (OpenAI/Anthropic) & Inference [Type A — Concept]

### What is it?
Integrating Large Language Models (LLMs) into an application via API calls, or hosting open-source models (like Llama 3) for inference on your own hardware.

### Mental Model
API = Hiring a world-class consultancy (OpenAI) by sending them an email and paying per word they write back.
Local Inference = Buying the books and hiring an in-house expert (GPUs) to do the work securely in your own office.

### Why does it exist?
To give systems reasoning, summarization, and natural language capabilities without writing complex logic rules.

### Real-World Example
**Customer Support AI:** Instead of hardcoding `if (msg == "refund")`, the backend passes the user's message to the OpenAI API, which understands the semantic intent and generates a polite response.

### Architecture / Raw Diagram
```text
[ Client ] ─> [ Your Backend ] ─(HTTP POST)─> [ OpenAI API ]
                     │                               │
            (DB/Business Logic)               (GPU Inference)
```

### Data Flow
1. User sends prompt.
2. Backend constructs a larger prompt (injecting system instructions).
3. Backend calls `POST https://api.openai.com/v1/chat/completions`.
4. Wait 1-5 seconds.
5. OpenAI returns JSON. Backend returns to user.

### When Would I Use It?
- Any modern AI application.

### Trade-offs
- **API (OpenAI):** Easy, state-of-the-art models, zero infrastructure. BUT high latency, data privacy concerns (sending customer data to a 3rd party), and high cost at scale.
- **Local Inference (vLLM/Ollama):** Total privacy, no API costs. BUT requires expensive GPU infrastructure (AWS `g4dn` instances) and heavy operational management.

### Implementation Idea
Use the official SDKs (e.g., `openai` npm package). For production, ensure you implement **Streaming** (Server-Sent Events) so the user sees the text typing out, masking the high latency of LLM generation.

### Interview Question
"What are the trade-offs of using the OpenAI API versus hosting Llama 3 on your own AWS EC2 instances?"

### Follow-up 1:
"How do you handle the high latency (often 2-5 seconds) of an LLM API call so the user doesn't think the app froze?" (Answer: Use HTTP streaming / Server-Sent Events to stream tokens as they are generated).

### Common Mistake
Treating an LLM API call like a fast database query. LLM calls are incredibly slow. They must be handled asynchronously or streamed.

---

## #82. Embeddings & Vector Databases [Type A — Concept]

### What is it?
- **Embeddings:** Converting text (or images) into an array of numbers (a vector) that captures semantic meaning. (e.g., "Dog" and "Puppy" have similar vectors).
- **Vector Database:** A database (Pinecone, Milvus, pgvector) designed to store these number arrays and quickly find the "nearest neighbors" (most similar vectors).

### Mental Model
Embedding = Plotting words on a 3D map. "King" and "Queen" are placed close together. "Apple" is placed far away.
Vector DB = A fast search engine for that map. "Show me the 5 closest points to 'Puppy'."

### Why does it exist?
Traditional SQL databases search by exact keyword match (e.g., `WHERE text LIKE '%dog%'`). If the text says "canine", SQL fails. Vector databases search by *meaning*, enabling semantic search and RAG.

### Real-World Example
**Spotify Recommendations:** Songs are converted into embeddings (vectors). If you like Song A, Spotify queries the Vector DB for the 10 songs geometrically closest to Song A's vector and recommends them.

### Architecture / Raw Diagram
```text
1. INGESTION (Save data):
[ Document ] ─> [ Embedding Model ] ─> [ Vector (0.1, 0.4, 0.9) ] ─> [ Vector DB ]

2. SEARCH (Query):
"Find pets" ─> [ Embedding Model ] ─> [ Vector (0.2, 0.5, 0.8) ] 
                                             │
                                   (Cosine Similarity Search)
                                             v
                                        [ Vector DB ] (Returns: "Dog", "Cat")
```

### Data Flow
N/A

### When Would I Use It?
- AI Search, Recommendation Engines, Retrieval-Augmented Generation (RAG).

### Trade-offs
- **Standalone Vector DB (Pinecone) vs PostgreSQL (pgvector):**
  - Pinecone is fully managed and ultra-fast for billions of vectors.
  - `pgvector` allows you to keep vectors in your existing relational database, allowing JOINs with normal data (e.g., "Find similar documents WHERE author = 'Aditya'").

### Implementation Idea
Use OpenAI's `text-embedding-3-small` API to convert text to arrays. Store them in PostgreSQL using the `pgvector` extension. Search using Cosine Similarity (`<=>`).

### Interview Question
"Explain how semantic search differs from traditional keyword search in a database."

### Follow-up 1:
"What mathematical operation is commonly used by Vector Databases to determine how similar two embeddings are?" (Answer: Cosine Similarity or Euclidean Distance).

### Common Mistake
Using LLMs to search large documents directly (e.g., pasting a 1,000-page book into ChatGPT and asking a question). This exceeds context windows and is extremely expensive. You must use Embeddings + Vector DBs to search first.

---

## #83. RAG (Retrieval-Augmented Generation) [Type B — Practical Design]

### What is it?
An AI architecture where you **Retrieve** relevant facts from a database (using a Vector DB), **Augment** the user's prompt with those facts, and then ask the LLM to **Generate** an answer.

### Mental Model
Taking an open-book test.
User asks a question. Instead of answering from memory (which causes hallucinations), the AI first searches the textbook (Retrieval), reads the relevant paragraphs (Augment), and then writes the final answer (Generation).

### Requirements
- Answer questions based on proprietary company data not present in the LLM's training data.
- Prevent LLM hallucinations.

### Architecture / Raw Diagram
```text
[ User Prompt: "What is our refund policy?" ]
             │
             v
(1) Embed Prompt ──> [ Vector DB ] (Searches company handbook)
             │
(2) Retrieve Data ──> "Policy: Refunds allowed within 30 days."
             │
(3) Augment Prompt ──> "Context: Refunds allowed within 30 days. Question: What is our refund policy?"
             │
(4) Generate ────────> [ LLM (OpenAI) ]
             │
             v
[ Final Accurate Response ]
```

### Data Flow
1. User asks a question.
2. Backend embeds the question.
3. Backend searches Vector DB for the top 3 most relevant chunks of company documents.
4. Backend combines the user's question + the 3 document chunks into one massive prompt.
5. Backend sends prompt to LLM.
6. LLM generates an accurate answer based strictly on the provided context.

### When Would I Use It?
- Customer support bots querying internal knowledge bases.
- AI features in SaaS products (e.g., "Chat with your codebase").

### Trade-offs
- **RAG vs Fine-tuning:** RAG is cheap, factual, and allows access control (you can filter the Vector DB by user ID). Fine-tuning an LLM teaches it a *style*, but is terrible at teaching it new *facts*, and is very expensive.

### If I had to code an MVP
- Use **LangChain** or **LlamaIndex** frameworks in Python/TypeScript.
- PostgreSQL (`pgvector`) for storage.

### Interview Question
"Your company wants to build a chatbot that answers questions based on a massive internal Wiki. How do you design this system?"

### Follow-up 1:
"How do you ensure the LLM doesn't hallucinate an answer if the relevant information is NOT found in the Vector Database?" (Answer: Hardcode instructions in the system prompt: "If the answer is not contained in the Context provided, say 'I don't know'.").

### Common Mistake
Suggesting Fine-Tuning the LLM on the company Wiki to make it memorize the data. This is an industry-wide anti-pattern. Always use RAG for factual knowledge retrieval.

---

## #84. AI Agents & Tool Calling [Type B — Practical Design]

### What is it?
An architecture where an LLM is not just a chatbot, but an "Agent" that can decide to execute functions (Tools) in your backend code (e.g., querying a database, calling an external API, running Python code).

### Mental Model
Chatbot = A consultant you ask for advice.
Agent = An employee you give a corporate credit card and permission to execute trades.

### Why does it exist?
LLMs are locked in a text box. To make them useful, they need hands to interact with the real world (read emails, update databases, trigger workflows).

### Real-World Example
**ChatGPT Plugins / Custom GPTs:** You ask ChatGPT "What is the weather in Tokyo?" It knows it cannot answer this from memory. It pauses, generates a JSON command to call a Weather API, reads the response, and then formats the answer for you.

### Architecture / Raw Diagram
```text
[ User: "Cancel my last order" ]
             │
             v
         [ LLM ] ──(Decides to use Tool)──> Returns JSON: {"action": "cancel_order", "id": 123}
             │
[ Backend API ] ──(Executes Code)──> DELETE FROM orders WHERE id=123
             │
         [ LLM ] <──(Result: Success)
             │
             v
[ Response: "I have cancelled order 123." ]
```

### Data Flow
1. Backend sends prompt + a list of available tool schemas (JSON definitions of functions).
2. LLM decides a tool is needed. It outputs a "Tool Call" JSON instead of text.
3. Backend intercepts the JSON, executes the actual Node.js/Python function (`cancelOrder()`).
4. Backend sends the result of the function back to the LLM.
5. LLM reads the result and generates a human-readable summary.

### When Would I Use It?
- Building AI assistants that take action (e.g., scheduling a meeting, updating CRM records).

### Trade-offs
- **Autonomy vs Safety:** Agents can cause massive damage if given write-access to production databases without human-in-the-loop validation.

### If I had to code an MVP
- Use OpenAI's API with the `tools` parameter.
- Never give an agent direct SQL access. Give it tightly scoped REST API endpoints (e.g., `/api/orders/cancel`).

### Interview Question
"Design an AI system that can read a user's natural language request and actually book a flight for them."

### Follow-up 1:
"How do you prevent a malicious user from telling the agent to 'Delete all users in the database'?" (Answer: Principle of Least Privilege. The Agent's backend tools should run using the authentication token of the specific user interacting with it, restricting its authorization).

### Common Mistake
Thinking the LLM actually executes code. The LLM only generates text/JSON. Your backend application is what actually executes the code.

---

## #85. Caching LLM Responses (Semantic Caching) [Type D — Trade-off Scenario]

### What is it?
Using a cache (like Redis) to store the answers to previously asked LLM prompts to save money and reduce latency. **Semantic Caching** uses embeddings to match *similar* questions, not just exact string matches.

### Mental Model
Standard Cache: Only matches if User B asks "How tall is the Eiffel Tower?" exactly like User A.
Semantic Cache: Recognizes that "What is the height of the Eiffel Tower?" means the exact same thing, and returns the cached answer without calling the LLM.

### Why does it exist?
LLM API calls are expensive (costs cents per query) and slow (takes seconds). If 1,000 users ask the same popular question, routing them all to OpenAI is a massive waste of resources.

### Real-World Example
**Perplexity AI:** Heavily caches trending queries. If a major news event happens, the first user pays the latency penalty for the LLM generation. The next 10,000 users get an instant response from the semantic cache.

### Architecture / Raw Diagram
```text
"What is Docker?" ─> [ Embed Prompt ] ─> [ Vector DB Cache ]
                                              │
                     (High similarity found to "Explain Docker")
                                              │
                             (Returns Cached LLM Response Instantly)
```

### Data Flow
1. Embed the incoming prompt.
2. Search Vector DB for embeddings with > 0.95 similarity.
3. If Hit: Return cached text response.
4. If Miss: Call LLM API -> Save Prompt Embedding + Response Text to Vector DB -> Return.

### When Would I Use It?
- High-traffic AI applications (Customer support FAQs).

### When Would I NOT Use It?
- Systems where the context changes dynamically every minute (e.g., live stock analysis), or highly personalized conversational AI where every prompt is unique to the user's specific history.

### Trade-offs
- **What do I gain?** 95% cost reduction and 10x faster responses for common queries.
- **What do I sacrifice?** Semantic caches can be overly aggressive (returning the answer for "How to install Python on Windows" when the user asked "How to install Python on Mac" if the similarity threshold is too low).

### Implementation Idea
Use **RedisVL** (Redis Vector Library) or **GPTCache** (Python library) to easily implement a caching layer in front of OpenAI calls.

### Interview Question
"Your LLM-powered customer support bot is costing $10,000 a month in OpenAI API fees because users keep asking the same 50 basic questions. How do you re-architect it?"

### Follow-up 1:
"How do you handle cache invalidation in an LLM semantic cache if company policy changes?" (Answer: Just like standard caches, apply a TTL to the cached responses, or explicitly flush the Vector DB when the underlying Knowledge Base is updated).

### Common Mistake
Setting the vector similarity threshold too low (e.g., 0.70). This causes the cache to return wildly incorrect, tangentially related answers to user questions.

---

## #86. Context Window Management [Type E — Implementation Scenario]

### What is it?
Techniques to manage the memory of an LLM. An LLM's "Context Window" is the maximum amount of text (tokens) it can process in a single request (e.g., 128k tokens).

### Mental Model
The context window is a whiteboard. You can write all the user's chat history on it so the AI remembers. But eventually, the whiteboard gets full. You have to erase the oldest or least important stuff to make room for new messages.

### Why does it exist?
LLMs are stateless (Concept #9). To have a long conversation, you must send the *entire chat history* to the API on *every single request*. If the history grows larger than the model's limit (or your budget), it crashes.

### Real-World Example
**ChatGPT UI:** When you have a massive, month-long chat thread, OpenAI doesn't send the entire month of text to the model every time you type. They compress, summarize, and selectively drop old messages before making the API call.

### Architecture / Raw Diagram
```text
[ Chat Database ]
       │
[ Fetch last 20 messages ] ──> [ Context Manager ]
                                      │ (If > token limit)
                                [ Summarize oldest 10 ]
                                      │
[ Prompt: (Summary) + (10 Recent Msgs) + (New Msg) ] ──> [ LLM API ]
```

### Data Flow
N/A

### When Would I Use It?
- Building any conversational AI or chatbot.

### Trade-offs
- **Truncation vs Summarization:**
  - Truncation (dropping the oldest messages) is cheap and fast, but the AI suddenly forgets things discussed 20 minutes ago.
  - Summarization (using a smaller, cheaper LLM to summarize the old history) preserves memory, but adds latency and compute cost.

### Implementation Idea
In code, implement a Sliding Window.
```javascript
let history = db.getMessages();
if (countTokens(history) > 8000) {
  // Keep system prompt + last 5 messages, discard the rest
  history = [history[0], ...history.slice(-5)]; 
}
const response = await openai.chat.completions.create({ messages: history });
```

### Interview Question
"How do you implement a long-running chatbot given that LLM APIs are stateless and have strict token limits?"

### Follow-up 1:
"How could you use a Vector Database to solve the context window limit for long conversations?" (Answer: Instead of sending all history, embed every past message into a Vector DB. When the user asks a question, retrieve only the top 3 most relevant past messages and inject them into the prompt).

### Common Mistake
Failing to track token usage on the backend. Just sending the array of messages and letting the API throw a `400 Token Limit Exceeded` error results in a terrible user experience.

---
*(End of Part 3 for Module 2. Next part covers the final practical systems and the start of the Final Master Revision).*
