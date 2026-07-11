# Day 32: Unit Testing
*(Detailed, step-by-step, from first principles — with definitions, simple analogies, system diagrams, and production Node.js examples)*

***

## SECTION 1: INTUITION (What is Unit Testing?)

Think of **testing parts on a car assembly line**:

### Scenario: The Assembly Line
```text
Engine arrives → Hook it to a diagnostic machine → Engine OK!
Brakes arrive → Apply hydraulic pressure tester → Brakes OK!
Tires arrive → Inflate to 40 PSI and check for leaks → Tires OK!
```
Notice that we are testing these parts **individually**, BEFORE they are attached to the car. We do not need the engine to test the brakes.

***

### In Backend Development:

**Unit Testing** means testing an individual function or method (a "unit") in total isolation from the rest of your application.

```javascript
// The Unit (A standalone function)
function add(a, b) {
  return a + b;
}

// The Unit Test
test('adds numbers correctly', () => {
  expect(add(2, 3)).toBe(5);
});
```

> [!TIP]
> **Simple Analogy:**  
> - **Unit Testing** is like testing a single puzzle piece to ensure its edges are cut perfectly, without worrying about what the final picture looks like.

***

## SECTION 2: THEORY (What is a Unit Test?)

### 2.1 Definition

A **Unit Test** is a script that:
1. Tests **exactly one** function/method/class.
2. Provides **specific inputs** (arguments).
3. Asserts that the **output** matches expectations.
4. **Isolates** the unit by severing all ties to databases, external APIs, and the file system.

**Key properties**:
- **Lightning Fast**: A suite of 1,000 unit tests should run in under 2 seconds.
- **Isolated**: If the database crashes, your unit tests should still pass.
- **Laser Focused**: If a unit test fails, you know exactly which 5 lines of code are broken.

***

### 2.2 What SHOULD You Unit Test?

**Do Test**:
- Pure utility functions (e.g., date formatting, math calculations, string parsing).
- Core business logic (e.g., calculating cart totals, checking if a user is an adult).
- Data validators (e.g., regex checks for email validity).

**Do NOT Unit Test**:
- Standard library functions (e.g., testing if `Array.push()` works).
- Database queries (these require Integration tests).
- HTTP routing / Controllers (these require API tests).
- 3rd Party APIs (e.g., Twilio, Stripe).

> ✅ **[Principal Engineer Note]: Test Behavior, Not Implementation**
> *A junior engineer tests HOW a function works. A senior engineer tests WHAT a function returns. If you have a function `sortUsers(users)`, do not write a test that checks if it called the `Array.prototype.sort()` method internally using a spy. Just test that the returned array is actually in alphabetical order! If you test the implementation, your test will break the second you refactor the code (e.g., switching to a custom QuickSort), even if the output is still perfectly correct. Brittle tests slow down teams.*

***

## SECTION 3: VISUAL DIAGRAMS

### Diagram 1: The Isolation Barrier

```mermaid
graph TD
    subgraph Unit Test Environment
        Test["Test Runner<br/>(Jest)"] -->|Inputs: 2, 3| Func[Unit Under Test<br/>calculateTax()]
        Func -->|Output: 5| Test
    end
    
    subgraph The Outside World (Banned from Unit Tests)
        DB[(Database)]
        API[External API]
        FS[File System]
    end
    
    Func -.x|NO| DB
    Func -.x|NO| API
    Func -.x|NO| FS
    
    style Func fill:#e8f5e9,stroke:#4caf50
    style DB fill:#ffcdd2,stroke:#f44336
    style API fill:#ffcdd2,stroke:#f44336
```

***

## SECTION 4: PRODUCTION EXAMPLES (Node.js & Jest)

Let's write production-grade unit tests for a currency formatting utility.

### 4.1 The Code (`currencyUtils.js`)

```javascript
function formatCurrency(amount, currency = 'USD') {
  if (typeof amount !== 'number') {
    throw new Error('Amount must be a number');
  }

  if (currency === 'USD') return `$${amount.toFixed(2)}`;
  if (currency === 'EUR') return `€${amount.toFixed(2)}`;
  if (currency === 'INR') return `₹${amount.toFixed(2)}`;
  
  // Fallback for unknown currencies
  return `${amount.toFixed(2)} ${currency}`;
}

module.exports = { formatCurrency };
```

### 4.2 The Tests (`currencyUtils.test.js`)

```javascript
const { formatCurrency } = require('./currencyUtils');

describe('formatCurrency()', () => {
  
  // 1. The "Happy Path" (Standard cases)
  test('formats USD correctly', () => {
    expect(formatCurrency(10.5, 'USD')).toBe('$10.50');
  });

  test('formats INR correctly', () => {
    expect(formatCurrency(1500, 'INR')).toBe('₹1500.00');
  });

  test('defaults to USD if no currency provided', () => {
    expect(formatCurrency(50)).toBe('$50.00');
  });

  // 2. The Fallback Case
  test('appends the string for unknown currencies', () => {
    expect(formatCurrency(10.5, 'JPY')).toBe('10.50 JPY');
  });

  // 3. Edge Cases (Zeroes, Negatives)
  test('handles exactly 0 correctly', () => {
    expect(formatCurrency(0, 'USD')).toBe('$0.00');
  });

  test('handles negative amounts correctly', () => {
    expect(formatCurrency(-5.25, 'EUR')).toBe('€-5.25');
  });

  // 4. Error Cases (Invalid Input)
  test('throws an error if amount is a string', () => {
    // Note: When testing for thrown errors in Jest, wrap the execution in a callback
    expect(() => formatCurrency('100', 'USD')).toThrow('Amount must be a number');
  });
  
});
```

***

## SECTION 5: COMMON MISTAKES

### Mistake 1: Asserting Too Many Things in One Test
```javascript
// BAD - The "God Test". If it fails, you don't know why.
test('formats currency properly', () => {
  expect(formatCurrency(10, 'USD')).toBe('$10.00');
  expect(formatCurrency(20, 'EUR')).toBe('€20.00');
  expect(() => formatCurrency('string')).toThrow();
});

// GOOD - One conceptual assertion per test
test('formats USD properly', () => {
  expect(formatCurrency(10, 'USD')).toBe('$10.00');
});
```

### Mistake 2: Leaking State Between Tests
If tests modify global variables, Test B might fail just because Test A ran before it.
**Solution**: Tests should be pure. If you must use shared variables, reset them in `beforeEach()`.

```javascript
let counter = 0;

beforeEach(() => {
  counter = 0; // Reset state so every test starts clean
});

test('increments', () => {
  counter++;
  expect(counter).toBe(1);
});
```

### Mistake 3: Re-testing the Language Itself
```javascript
// BAD - You are testing V8/Node.js, not your code.
test('Math.max works', () => {
  expect(Math.max(1, 5)).toBe(5); 
});
```

> ✅ **[Principal Engineer Note]: The Over-Mocking Trap**
> *In modern Node.js testing, developers often go crazy with mocks (mocking every single import in a file). If a unit function calls a simple local helper function `formatDate()`, DO NOT mock the helper! Let it run. If you mock the helper to always return "Jan 1", and someone breaks the real `formatDate()` logic, your unit test will still pass because it's using the fake mock. This creates a false sense of security. Only mock external boundaries (Network, Disk, Time).*

***

## SECTION 6: INTERVIEW PREPARATION

### Conceptual Questions
1. **What does it mean to test "in isolation"?** *(Answer: It means the unit test relies on absolutely zero external state. It does not read from a database, it does not fetch from an API, and it does not read local files).*
2. **What is Test-Driven Development (TDD)?** *(Answer: A workflow where you write the Unit Test FIRST, watch it fail, and then write the actual business logic to make the test pass).*
3. **If a Unit Test passes, does it mean the application works?** *(Answer: No. A unit test only proves the individual puzzle piece works. You still need Integration Tests to prove the pieces fit together).*

### System Design Scenario
*Company: PayPal*
"We have a massive monolithic backend. It takes 15 minutes to run our test suite, which is slowing down developer productivity. How do you fix this?"
*(Expected Answer: The test suite likely contains too many End-to-End or Integration tests that hit real databases. I would audit the tests and push them down the Testing Pyramid—converting database-dependent tests into isolated Unit Tests using Mocks. Unit tests run in parallel in milliseconds, drastically cutting CI/CD time).*

***
