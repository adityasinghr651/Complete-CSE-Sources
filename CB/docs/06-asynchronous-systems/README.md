# 06 Asynchronous Systems

## Overview
This module covers how to move heavy, time-consuming workloads off the main Node.js Event Loop to ensure your APIs remain fast and scalable. We explore background jobs, message queues, and Event-Driven Architecture.

## Topics Covered
- Background Jobs and asynchronous processing (BullMQ)
- Message Queues (RabbitMQ vs Kafka)
- Point-to-Point vs Publish-Subscribe messaging patterns
- Event-Driven Architecture (EDA)
- Producers, Consumers, and Event Buses

## Learning Objectives
- Understand why and when to offload tasks from the main HTTP request cycle.
- Implement background queues using Redis and BullMQ.
- Differentiate between RabbitMQ and Kafka for message brokering.
- Design fully decoupled microservices using Event-Driven Architecture.

## Prerequisites
- Node.js & Express fundamentals.
- Understanding of the MERN stack architecture.
- Redis basics.

## Estimated Time
6 Hours

## Folder Navigation
- [01-background-jobs.md](./01-background-jobs.md)
- [02-message-queues.md](./02-message-queues.md)
- [03-event-driven-architecture.md](./03-event-driven-architecture.md)
- [../](../README.md)

## Progress Checklist
- [ ] 01 Background Jobs & Async Processing
- [ ] 02 Message Queues (RabbitMQ & Kafka)
- [ ] 03 Event-Driven Architecture

## References
- [BullMQ Documentation](https://docs.bullmq.io/)
- [RabbitMQ Official Tutorials](https://www.rabbitmq.com/getstarted.html)
- [KafkaJS Documentation](https://kafka.js.org/)
