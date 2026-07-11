# Day 35: Mocking & Stubs
*(Detailed, step-by-step, from first principles — with definitions, simple analogies, system diagrams, and production Node.js examples)*

***

## SECTION 1: INTUITION (What is Mocking?)

Think of testing your ability to **fly an airplane**:

### Scenario: The Real World (Dangerous & Expensive)
```text
You step into a $300 Million Boeing 747.
You attempt to practice dealing with engine failure.
You actually turn off the engine.
The plane crashes.
```
Testing on the real dependency (the real plane) is wildly dangerous, expensive, and slow.

***

### Scenario: The Flight Simulator (Mocking)
```text
You step into a Flight Simulator (A Mock).
You press a button. The simulator *pretends* to turn off the engine.
You practice the emergency sequence.
The simulator records your reaction time.
You step out safely.
```
The simulator is a **fake object** that perfectly mimics the inputs and outputs of the real object, but without the real-world consequences or costs.

***

### In Backend Development:

**Mocking** means replacing a real software dependency (like the Stripe API, AWS S3, or your Database) with a "Flight Simulator" (a fake JavaScript object) during tests.

```javascript
// Real implementation (Costs money, requires internet)
await stripe.charges.create({ amount: 1000 });

// Mock implementation in a test (Free, instant, offline)
const stripeMock = {
  charges: {
    create: () => Promise.resolve({ status: 'succeeded', id: 'ch_123' })
  }
};
```

> [!TIP]
> **Simple Analogy:**  
> - **Mocking** is like testing a TV remote using a plastic toy TV that just lights up when you press a button. You don't need a real $2,000 OLED screen just to verify the remote's volume button is transmitting a signal.

***

## SECTION 2: THEORY (Why Do We Mock?)

### 2.1 Definition

A **Mock** is a fake object created specifically for testing that:
1. **Replaces** the real dependency.
2. **Returns** predefined, hardcoded values (so you can test the "Happy Path" and the "Sad Path").
3. **Tracks** how it was interacted with (e.g., "Was this function called exactly twice? Was it called with the email 'test@test.com'?").

***

### 2.2 Why Do We Mock?

1. **To Save Money**: If you have a test suite that runs 1,000 times a day, and one test sends an SMS via Twilio ($0.01 per SMS), that single test will cost you $10 a day. Mock the Twilio client.
2. **To Run Offline & Fast**: Unit tests must run in milliseconds. If your test makes a network request to AWS, it takes hundreds of milliseconds and fails if you are coding on an airplane without WiFi.
3. **To Force Rare Errors**: How do you test what your app does if the Database connection crashes mid-query? You can't easily force a real DB to do that reliably. You *can* easily tell a Mock DB to throw a `ConnectionRefusedError`.

> ✅ **[Principal Engineer Note]: The Danger of Mock Drift**
> *Mocks are dangerous because they are liars. If you mock the Stripe API to return `{ success: true }`, your tests pass. But what if Stripe updates their API tomorrow and starts returning `{ status: 'ok' }` instead? Your mock still returns `{ success: true }`. Your tests still pass. But Production crashes immediately! This is called **Mock Drift**. To prevent this, Principal Engineers enforce TypeScript (so the mock must match the official SDK types) or use "Contract Testing" (where the mock is generated directly from the provider's OpenAPI spec).*

***

## SECTION 3: VISUAL DIAGRAMS

### Diagram 1: The Mocking Architecture

```mermaid
graph TD
    subgraph Test Execution
        Test[Unit Test] --> Logic["Business Logic<br/>processOrder()"]
        
        Logic -.x|BANNED| RealAPI[Real Stripe API]
        Logic -->|Redirected| MockAPI[Mock Stripe Client]
        
        MockAPI -->|Returns {success: true}| Logic
    end
    
    style Logic fill:#e8f5e9,stroke:#4caf50
    style RealAPI fill:#ffcdd2,stroke:#f44336
    style MockAPI fill:#fff3e0,stroke:#ff9800
```

***

## SECTION 4: PRODUCTION EXAMPLES (MERN STACK)

We will use **Jest**, which has incredibly powerful built-in mocking capabilities.

### 4.1 Example: Mocking an External Service (Email)

Suppose we have a service that registers a user and sends them a welcome email using SendGrid. We want to test the registration logic, but we absolutely **do not** want to send a real email.

**The Code (`authService.js`)**:
```javascript
const sendgrid = require('@sendgrid/mail');
sendgrid.setApiKey('real-api-key');

async function registerUser(email) {
  // 1. Business logic (Save to DB, etc)
  const user = { id: 1, email };
  
  // 2. Call external service
  await sendgrid.send({
    to: email,
    from: 'hello@myapp.com',
    subject: 'Welcome!',
    text: 'Thanks for joining'
  });
  
  return user;
}

module.exports = { registerUser };
```

**The Test (`authService.test.js`)**:
```javascript
const { registerUser } = require('./authService');
// 1. Import the actual library we want to mock
const sendgrid = require('@sendgrid/mail');

// 2. Tell Jest to intercept all calls to this module and replace it with a fake!
jest.mock('@sendgrid/mail');

describe('registerUser', () => {
  
  test('successfully registers a user and sends an email', async () => {
    // Tell our mock to pretend the email sent successfully (resolve)
    sendgrid.send.mockResolvedValue(true);
    
    // Execute our function
    const user = await registerUser('test@test.com');
    
    // Assertions on our logic
    expect(user.email).toBe('test@test.com');
    
    // SPY ASSERTIONS: Check if the mock was interacted with correctly!
    expect(sendgrid.send).toHaveBeenCalledTimes(1);
    expect(sendgrid.send).toHaveBeenCalledWith(
      expect.objectContaining({
        to: 'test@test.com',
        subject: 'Welcome!'
      })
    );
  });

  test('handles email provider downtime gracefully', async () => {
    // Tell our mock to simulate an AWS/SendGrid crash!
    sendgrid.send.mockRejectedValue(new Error('Network Timeout'));
    
    // Test how our app responds to the crash
    await expect(registerUser('test@test.com')).rejects.toThrow('Network Timeout');
  });

});
```

***

### 4.2 Example: Mocking Time (Timers)

Sometimes you have code that relies on `setTimeout` or `setInterval`. If you have a function that waits 5 minutes before acting, you don't want your automated test to physically pause for 5 minutes.

**The Code**:
```javascript
function sendDelayedNotification(callback) {
  setTimeout(() => {
    callback("Notification Sent!");
  }, 300000); // 5 Minutes
}
```

**The Test (Time Travel!)**:
```javascript
// Tell Jest to take control of the global JavaScript clock
jest.useFakeTimers();

test('sends notification after 5 minutes', () => {
  const mockCallback = jest.fn(); // Create a fake function to track if it's called
  
  // Call the function (it starts the 5 minute timer)
  sendDelayedNotification(mockCallback);
  
  // Verify it has NOT been called yet
  expect(mockCallback).not.toHaveBeenCalled();
  
  // TIME TRAVEL: Fast-forward time by exactly 5 minutes instantly
  jest.advanceTimersByTime(300000);
  
  // Verify it WAS called
  expect(mockCallback).toHaveBeenCalledWith("Notification Sent!");
  expect(mockCallback).toHaveBeenCalledTimes(1);
});
```

***

## SECTION 5: COMMON MISTAKES

### Mistake 1: Mocking Everything (The "Echo Chamber")
If you mock the Database, the Email Provider, and the File System all in the same test, what are you actually testing? You are just testing that JavaScript can call functions. 
**Solution**: Use mocks *sparingly*. In Integration Tests, use a real test database. Only mock external 3rd-party services that you have no control over (Stripe, Twilio, AWS).

### Mistake 2: Forgetting to Clear Mocks Between Tests
If Test 1 calls the mocked Stripe API twice, and Test 2 calls it once, Test 2 will think the mock was called three times (because the mock remembers history).
**Solution**: Always clear mock history in `beforeEach`.

```javascript
beforeEach(() => {
  jest.clearAllMocks(); // Resets the "toHaveBeenCalledTimes" counters
});
```

> ✅ **[Principal Engineer Note]: Mocking Modules vs Mocking Network**
> *Using `jest.mock('axios')` is common but fragile. It intercepts the `require` statement. If you switch from Axios to `fetch`, your tests break even if the HTTP logic is correct. In production, we prefer tools like **MSW (Mock Service Worker)** or **Nock**. These tools run at the OS/Network level, intercepting the actual outgoing HTTP request to `https://api.stripe.com` and returning fake JSON. This allows your code to execute the real HTTP client library normally, providing much higher confidence.*

***

## SECTION 6: INTERVIEW PREPARATION

### Conceptual Questions
1. **What is the difference between a Mock and a Stub?** *(Answer: A Stub simply returns hardcoded data to keep the code from crashing. A Mock is "smart"—it records if it was called, how many times, and with what arguments, allowing you to make assertions against it).*
2. **Why shouldn't you mock your database in Integration tests?** *(Answer: The entire point of an integration test is to verify that your SQL/NoSQL queries actually work. If you mock the DB, a syntax error in your query will go undetected because the mock simply returns fake data regardless of the query string).*

### System Design Scenario
*Company: Netflix*
"We have a microservice that charges user credit cards on the 1st of every month. The code iterates over 50 million users and calls the Visa/Mastercard API. How do we test this billing script in our CI/CD pipeline?"
*(Expected Answer: We must use API Mocking. We write a test where the Visa API client is replaced with a Mock. We instruct the Mock to return "Success" for the first 10 calls, and "Insufficient Funds" for the 11th call. We then assert that our billing script correctly records the successes in our Test Database and correctly handles the failure logic for the 11th user, all without making a single real network request).*

***
