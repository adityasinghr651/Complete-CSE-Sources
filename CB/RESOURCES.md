# 📚 Backend Engineering Resources

To complement the curriculum in this handbook, we highly recommend exploring the following resources. A Principal Engineer is constantly learning from industry standards, official documentation, and real-world system failures.

## 📖 Essential Books

1. **Designing Data-Intensive Applications** *(by Martin Kleppmann)*
   > The "Bible" of backend engineering. Must-read for understanding distributed systems, databases, caching, and message queues.
2. **System Design Interview – An Insider's Guide** *(by Alex Xu)*
   > Excellent for visual learners to understand how massive systems (like YouTube, Chat Apps, and Rate Limiters) are architected.
3. **Clean Architecture** *(by Robert C. Martin)*
   > Helps you structure your backend code to be decoupled, testable, and maintainable.
4. **Site Reliability Engineering** *(by Google)*
   > Learn how to keep production systems running, including SLIs, SLOs, and incident response.

---

## 🏗️ Top Engineering Blogs (Real-World Architecture)

Reading how companies solve problems at scale is the fastest way to bridge the gap between tutorial-level code and production architecture:

- [Discord Engineering Blog](https://discord.com/category/engineering) - Incredible deep dives into Real-Time Systems, WebSockets, and database migrations.
- [Netflix TechBlog](https://netflixtechblog.com/) - Pioneers of microservices, chaos engineering, and streaming architecture.
- [Uber Engineering](https://www.uber.com/en-IN/blog/engineering/) - Great insights into geospatial data, distributed tracing, and massive scale routing.
- [Cloudflare Blog](https://blog.cloudflare.com/) - The best resource for learning about low-level networking, HTTP protocols, and DDoS protection.
- [Stripe Engineering](https://stripe.com/blog/engineering) - The gold standard for API design, idempotency, and developer experience.

---

## 🛠️ Essential Developer Tools

### API & Networking
- **[Postman](https://www.postman.com/)** or **[Insomnia](https://insomnia.rest/)**: For testing REST APIs manually.
- **[Wireshark](https://www.wireshark.org/)**: For inspecting raw TCP/IP packets to truly understand networking.
- **[Ngrok](https://ngrok.com/)**: Expose your local `localhost` server to the public internet securely (great for testing webhooks).

### Infrastructure & Operations
- **[Docker Desktop](https://www.docker.com/products/docker-desktop/)**: Run your databases and caches locally via containers.
- **[RedisInsight](https://redis.com/redis-enterprise/redis-insight/)**: A powerful GUI for visually exploring your Redis cache data.
- **[DBeaver](https://dbeaver.io/)** or **[DataGrip](https://www.jetbrains.com/datagrip/)**: Universal database GUIs to inspect your SQL and NoSQL data.

### Performance & Load Testing
- **[Artillery](https://www.artillery.io/)** or **[k6](https://k6.io/)**: For hammering your APIs with fake traffic to see where the architecture breaks under load.

---

## 📑 Official Documentation (Bookmark These)

Never rely strictly on StackOverflow. A Senior Engineer reads the source of truth:
- [MDN Web Docs (HTTP Section)](https://developer.mozilla.org/en-US/docs/Web/HTTP) - The ultimate authority on headers, status codes, and methods.
- [Node.js Official Docs](https://nodejs.org/en/docs/) - Crucial for understanding the Event Loop, Streams, and the `fs` module.
- [PostgreSQL Documentation](https://www.postgresql.org/docs/) - Incredibly well-written docs on MVCC, indexing, and transactions.
- [Redis Commands](https://redis.io/commands/) - Learn what Redis can do beyond simple `GET` and `SET`.
