# 08 Testing

## Overview
This module transitions you from simply writing code that "works on my machine" to engineering robust, production-grade applications that can be safely modified and deployed with zero fear. You will learn the mechanics and philosophy of automated testing.

## Topics Covered
- The Testing Pyramid (Unit, Integration, End-to-End)
- Unit Testing business logic in isolation
- Integration Testing with temporary, in-memory databases
- API Testing (Black-Box testing endpoints)
- Mocking and Stubbing external dependencies and APIs
- Jest and Supertest fundamentals

## Learning Objectives
- Understand why automated testing is mathematically cheaper than manual QA testing.
- Write lightning-fast, isolated Unit Tests using Jest.
- Spin up isolated MongoDB Memory Servers to run safe Integration Tests.
- Assert HTTP Headers, Status Codes, and JSON payloads using Supertest.
- Intercept and Mock external 3rd-party API calls (e.g., SendGrid, Stripe) to keep test suites fast and free.

## Prerequisites
- Node.js & Express fundamentals.
- Understanding of the MERN stack architecture.
- Understanding of REST APIs (Phase 2).

## Estimated Time
8 Hours

## Folder Navigation
- [01-why-testing-matters.md](./01-why-testing-matters.md)
- [02-unit-testing.md](./02-unit-testing.md)
- [03-integration-testing.md](./03-integration-testing.md)
- [04-api-testing.md](./04-api-testing.md)
- [05-mocking.md](./05-mocking.md)
- [../](../README.md)

## Progress Checklist
- [x] 01 Why Testing Matters
- [x] 02 Unit Testing
- [x] 03 Integration Testing
- [x] 04 API Testing
- [x] 05 Mocking & Stubs

## References
- [Jest Official Documentation](https://jestjs.io/docs/getting-started)
- [Supertest NPM](https://www.npmjs.com/package/supertest)
- [MongoDB Memory Server](https://github.com/nodkz/mongodb-memory-server)
