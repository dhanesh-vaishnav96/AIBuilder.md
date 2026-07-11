# Part 10: Production Deployment

"It works on my machine" is a junior developer's excuse. A Staff Engineer cares about how the system behaves under load, in a production environment, with real users and real security threats. AI tools are notoriously bad at deployment configuration because they are inherently biased toward local development setups.

---

## 1. What AI Doesn't Know About Production

If you tell an AI to "build an app," it will likely give you a SQLite database, an in-memory session store, and a development server script. In production, this setup will fail immediately.

AI models often forget about:
* **Environment Variables (`.env`):** Hardcoding API keys or database URIs.
* **CORS Restrictions:** Allowing `*` (all origins) instead of locking down to your specific production domain.
* **Connection Pooling:** Crashing the database with too many open connections under load.
* **Statelessness:** Storing uploaded images or user sessions on the local disk (which breaks as soon as you scale to 2+ servers behind a load balancer).
* **Observability:** Lacking structured JSON logging (like Winston or Pino) required for Datadog or AWS CloudWatch.

---

## 2. The Deployment Pipeline

You must instruct the AI to build code that fits into a standard CI/CD (Continuous Integration / Continuous Deployment) pipeline.

```mermaid
graph LR
    A[Local Code (Git Push)] --> B(CI/CD: GitHub Actions)
    
    subgraph CI: Continuous Integration
    B --> C{Run Unit/Int Tests}
    C -->|Pass| D[Lint & Security Scan]
    end
    
    subgraph CD: Continuous Deployment
    D --> E[Build Docker Image]
    E --> F[Push to Container Registry]
    F --> G[Deploy to AWS ECS / Vercel]
    end
    
    C -->|Fail| H[Halt Pipeline]
    D -->|Fail| H
    
    style B fill:#bbdefb,stroke:#1976d2
    style G fill:#c8e6c9,stroke:#388e3c
    style H fill:#ffcdd2,stroke:#d32f2f
```

---

## 3. Preparing AI Code for Production

Before you deploy, you must run a specific "Production Readiness" prompt to force the AI to transition the codebase from local-dev to production-ready.

**The Production Prompt:**
*"@auth_module.ts @db.ts 
**Task:** Review the authentication and database modules for production deployment on AWS ECS behind a Load Balancer.
**Actions Required:**
1. Extract all hardcoded secrets and URLs into environment variables with validation (e.g., using Zod for `process.env`).
2. Add structured JSON logging for all errors. Replace all `console.log` with a proper logger.
3. Configure CORS to strictly allow `https://app.abclimited.com`.
4. Ensure the session store is using Redis, not in-memory, to support multi-instance scaling.
**Output:** Provide the updated files and a `Dockerfile` optimized for a production Node.js environment (multi-stage build, non-root user)."*

---

## 4. Hands-on Exercise: Pre-Deployment Audit

**Scenario:**
You are deploying a Node.js API to production. The AI used a local SQLite database file (`data.db`) during development and implemented a user file upload feature that saves images to `./uploads/images`.

**Your Task:**
List 2 critical architectural changes you must prompt the AI to make before this goes to a production environment consisting of 3 load-balanced servers.

> **Staff Engineer Solution & Rationale:**
> 1. **Database Migration:** SQLite is file-based. If Server 1 writes to its local SQLite file, Server 2 will not see that data. You MUST prompt the AI to migrate the database connection to a centralized relational database like PostgreSQL or MySQL (e.g., AWS RDS).
> 2. **Storage Migration:** Saving files to the local `./uploads` directory means if a user uploads an avatar to Server A, and later their request is routed to Server B, their avatar will be missing. You MUST prompt the AI to rewrite the upload logic to stream files to centralized object storage (like AWS S3) instead of the local disk.
> 
> *Rationale: Production systems are ephemeral and distributed. Local disk state is the enemy of scaling.*

---

## 5. Review Checklist

- [ ] I understand that AI defaults to local, stateful development patterns.
- [ ] I will actively audit AI code for hardcoded secrets and CORS vulnerabilities before deployment.
- [ ] I will prompt the AI to abstract storage (S3) and sessions (Redis) for scalable deployments.
- [ ] I will use a "Production Prompt" to finalize my codebase before merging to the main branch.

**Next Steps:**
In Part 11, we will apply the entire workflow to massive, real-world Enterprise Case Studies.
