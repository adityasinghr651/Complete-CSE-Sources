# 07 Real-Time Systems

## Overview
This module transitions you from static request-response systems to dynamic, real-time architectures. You will learn how to push live data to users, build chat applications, and architect scalable notification systems used by millions.

## Topics Covered
- Client-Server communication strategies
- Short Polling vs Long Polling
- WebSockets protocol and architecture
- Building Real-Time Chat Systems (Node.js, `ws`, Redis Pub/Sub)
- Notification Systems (Push, Email, SMS) using BullMQ

## Learning Objectives
- Understand the tradeoffs between HTTP polling and WebSockets.
- Implement Long Polling using Node.js Event Emitters.
- Build a persistent WebSocket connection that handles live chatting and broadcasts.
- Design a multi-server real-time architecture utilizing Redis Pub/Sub.
- Architect an asynchronous, distributed notification service using message queues.

## Prerequisites
- Node.js & Express fundamentals.
- Understanding of the MERN stack architecture.
- Understanding of Message Queues and Background Jobs (Phase 6).

## Estimated Time
10 Hours

## Folder Navigation
- [01-polling.md](./01-polling.md)
- [02-long-polling.md](./02-long-polling.md)
- [03-websockets.md](./03-websockets.md)
- [04-chat-architecture.md](./04-chat-architecture.md)
- [05-notification-systems.md](./05-notification-systems.md)
- [../](../README.md)

## Progress Checklist
- [x] 01 Polling Fundamentals
- [x] 02 Long Polling
- [x] 03 WebSockets
- [x] 04 Real-Time Chat Architecture
- [x] 05 Notification Systems Architecture

## References
- [WebSocket API (MDN)](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API)
- [ws library (Node.js)](https://github.com/websockets/ws)
- [Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging)
