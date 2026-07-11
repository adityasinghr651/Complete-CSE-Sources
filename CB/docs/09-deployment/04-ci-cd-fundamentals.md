# Day 39: CI/CD Fundamentals (Continuous Integration / Continuous Deployment)
*(Detailed, step-by-step, from first principles — with definitions, system diagrams, and production Node.js examples)*

***

## SECTION 1: INTUITION (What is CI/CD?)

Think of **a modern car manufacturing plant**:

### Scenario 1: Manual Production (Pre-CI/CD)
```text
Engineer designs a steering wheel.
Engineer carries it to the factory floor.
Engineer realizes it doesn't fit the steering column.
Engineer walks back to their desk to fix it.
Engineer finally fits it, then manually drives the car out of the factory to sell it.
```
This is slow, error-prone, and relies entirely on human memory. If the engineer forgets to tighten a bolt, a broken car is sold to a customer.

***

### Scenario 2: Automated Assembly Line (CI/CD)
```text
Engineer places the steering wheel on a conveyor belt.
Robot 1 instantly checks if the dimensions are correct (Automated Tests).
Robot 2 automatically bolts it to the car (Automated Build).
Robot 3 drives the finished car to the dealership (Automated Deployment).
```
The engineer never leaves their desk. They just drop the part on the belt. If it's flawed, Robot 1 immediately throws it in the trash and blares an alarm.

***

### In Backend Development:

**CI/CD** is the automated conveyor belt for your source code. When you type `git push`, robots (servers) take over. They run your unit tests, build your Docker images, and deploy the new code to AWS/Vercel without a human ever touching a server.

***

## SECTION 2: THEORY (Defining CI and CD)

### 2.1 Continuous Integration (CI)

**CI** is the practice of merging all developer working copies to a shared mainline several times a day.
- **The Goal**: Detect bugs instantly. 
- **The Mechanism**: Every time you open a Pull Request or push code, a server downloads your code, runs `npm install`, runs `npm test`, and runs a linter. If any test fails, the Pull Request is blocked from merging.

### 2.2 Continuous Deployment (CD)

**CD** takes over where CI leaves off. 
- **The Goal**: Get code to users as fast as safely possible.
- **The Mechanism**: Once the code is merged to the `main` branch (and CI has proven it is safe), the CD server automatically builds the production artifacts (e.g., Docker Images) and pushes them to your live servers.

***

## SECTION 3: VISUAL DIAGRAMS

### Diagram 1: The CI/CD Pipeline

```mermaid
graph TD
    subgraph Developer Laptop
        Code[Write Code] --> Push[git push origin main]
    end

    subgraph The CI Server (GitHub Actions)
        Push --> Install[1. npm install]
        Install --> Lint[2. ESLint / Prettier]
        Lint --> Test[3. Run Jest Tests]
    end
    
    subgraph The CD Server (GitHub Actions / Vercel)
        Test -->|If Tests Pass| Build[4. Build Docker Image]
        Build --> Deploy[5. Deploy to Production Server]
    end
    
    Test -.x|If Tests Fail| Block[Halt Pipeline & Alert Dev]
    
    style Push fill:#e3f2fd,stroke:#2196f3
    style Test fill:#fff9c4,stroke:#fbc02d
    style Deploy fill:#c8e6c9,stroke:#4caf50
    style Block fill:#ffcdd2,stroke:#f44336
```

***

## SECTION 4: PRODUCTION EXAMPLES (GitHub Actions)

We will use **GitHub Actions**, which is free and built directly into GitHub. It listens for `git push` events and spins up temporary Linux servers to run your pipeline.

### 4.1 The Configuration File

Create a file in your project exactly here: `.github/workflows/ci-cd.yml`.

```yaml
name: Node.js CI/CD Pipeline

# 1. TRIGGER: When does this pipeline run?
on:
  push:
    branches: [ "main" ] # Run on pushes to the main branch
  pull_request:
    branches: [ "main" ] # Run when a PR targets the main branch

# 2. JOBS: What servers should spin up and what should they do?
jobs:
  
  # --- JOB 1: CONTINUOUS INTEGRATION ---
  build-and-test:
    runs-on: ubuntu-latest # Spin up a fresh Linux machine

    steps:
    - name: Checkout Code
      uses: actions/checkout@v3 # Downloads your code onto the Linux machine

    - name: Setup Node.js Environment
      uses: actions/setup-node@v3
      with:
        node-version: '18.x' # Install Node.js v18

    - name: Install Dependencies
      run: npm ci # 'npm ci' is faster and stricter than 'npm install'

    - name: Run Linter
      run: npm run lint

    - name: Run Unit Tests
      run: npm test
      # If this fails, GitHub instantly halts the pipeline. The next job will NEVER run.

> ✅ **[Principal Engineer Note]: Caching in CI**
> *In the pipeline above, `npm ci` downloads 500MB of dependencies from the internet on every single push. This can add 2-3 minutes to your pipeline. In enterprise environments, speed is money. Senior engineers use GitHub Actions Caching (`actions/cache@v3`) to cache the `~/.npm` directory between runs. If the `package-lock.json` hasn't changed since the last push, the cache is restored in 2 seconds, entirely skipping the network download!*

  # --- JOB 2: CONTINUOUS DEPLOYMENT ---
  deploy-to-production:
    needs: build-and-test # CRITICAL: This job waits for the CI job to succeed!
    runs-on: ubuntu-latest
    
    # We only want to deploy if code was merged into main. (Don't deploy PRs)
    if: github.ref == 'refs/heads/main' 

    steps:
    - name: Checkout Code
      uses: actions/checkout@v3

    # Example A: Deploying to Vercel/Railway using a CLI
    - name: Deploy to Vercel
      run: npx vercel --prod --token ${{ secrets.VERCEL_TOKEN }}
      
    # Example B: SSH into a VPS and pull new code
    # - name: Deploy to VPS
    #   uses: appleboy/ssh-action@master
    #   with:
    #     host: ${{ secrets.VPS_HOST }}
    #     username: ${{ secrets.VPS_USERNAME }}
    #     key: ${{ secrets.VPS_SSH_KEY }}
    #     script: |
    #       cd /var/www/myapp
    #       git pull origin main
    #       npm install
    #       pm2 restart api
```

### 4.2 Handling Secrets in CI/CD

Notice `${{ secrets.VERCEL_TOKEN }}` in the YAML file. 
You **cannot** commit your deployment passwords into this YAML file. Instead, you go to your GitHub Repository Settings -> Secrets, and paste your passwords there. The GitHub Action runner injects them dynamically.

***

## SECTION 5: COMMON MISTAKES

### Mistake 1: Deploying Without Passing Tests
If your `deploy` job does not explicitly declare `needs: build-and-test`, GitHub Actions will run both jobs in parallel! It will deploy your broken code at the exact same time it is running the failing tests.
**Solution**: Always chain jobs using the `needs` keyword.

### Mistake 2: Writing Flaky Tests
If a test passes 90% of the time but fails 10% of the time (usually due to network timeouts or missing database teardowns), developers will stop trusting the CI pipeline. They will start manually overriding failed tests.
**Solution**: Quarantine and fix flaky tests immediately. A CI pipeline must be mathematically deterministic.

### Mistake 3: Deploying on Fridays
CD is powerful, but it isn't magical. If a bug slips past your tests and deploys at 5:00 PM on a Friday, your engineering team will spend their entire weekend fixing the production database.
**Solution**: Implement "Deploy Freezes" on Friday afternoons.

> ✅ **[Principal Engineer Note]: The Database Migration Trap**
> *CD works flawlessly for stateless code. But what if your PR renames a database column from `userId` to `customer_id`? If the CD pipeline runs the database migration first, the currently running production servers will instantly crash because they are still querying `userId`! To solve this, you must use the **Expand and Contract Pattern**: 1. Deploy code that writes to BOTH columns. 2. Run a script to copy old data to the new column. 3. Deploy code that only reads the new column. 4. Finally, drop the old column.*

***

## SECTION 6: INTERVIEW PREPARATION

### Conceptual Questions
1. **What is the difference between Continuous Delivery and Continuous Deployment?** *(Answer: In Continuous Delivery, the code is automatically tested and built into a deployable artifact, but a human must press a button to push it to production. In Continuous Deployment, the push to production happens completely automatically without human intervention).*
2. **Why do we use `npm ci` instead of `npm install` in a CI environment?** *(Answer: `npm ci` (Clean Install) strictly follows the `package-lock.json` file and does not modify it. It deletes the existing `node_modules` folder and guarantees a deterministic, repeatable installation, whereas `npm install` might quietly update a sub-dependency).*

### System Design Scenario
*Company: AWS*
"We have a team of 50 developers. Currently, to deploy our backend, Developer A has to SSH into the production server, run `git pull`, and restart Node.js. This takes 20 minutes and often causes downtime. How do you fix this?"
*(Expected Answer: I would completely ban developers from SSH-ing into production. I would set up a CI/CD pipeline using GitHub Actions. Developers simply push their code to the `main` branch. The CI pipeline will run all Jest tests. If they pass, the CD pipeline will authenticate with the server and orchestrate a zero-downtime deployment (e.g., using Docker or PM2 rolling restarts). This reduces deployment time from 20 minutes to seconds, and removes the risk of human error).*

***
