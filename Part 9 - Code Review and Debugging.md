# Part 9: Code Review & Debugging

AI will write bad code. It will write insecure code. It will write unscalable code. Your job as a Senior Vibe Coder is not to write the code, but to act as the ultimate gatekeeper for quality. You must treat the AI as a highly productive, very confident Junior Developer who sometimes hallucinates.

---

## 1. The Senior Review Checklist

When the AI finishes generating code, you do not just click "Accept". You must perform a rigorous review against these five pillars:

1. **Security:** Did it sanitize inputs? Are SQL queries parameterized? Are secrets hardcoded instead of using environment variables?
2. **Architecture:** Did it bypass the Domain layer and put database queries directly in the UI component?
3. **Performance:** Is it doing N+1 queries in a loop? Is it rendering a massive list in React without virtualization?
4. **Resilience:** Are network calls wrapped in `try/catch`? Is it gracefully handling API timeouts?
5. **Maintainability:** Are the variables named `data1` and `res`, or `userData` and `authResponse`?

---

## 2. Debugging Workflow with AI

When an error occurs, junior developers often paste the entire 500-line stack trace into the AI and say "fix it". This destroys the AI's context window and often leads to the AI proposing a "fix" that simply suppresses the error (like adding an empty `catch` block) instead of solving the root cause.

### The Staff Engineer Debugging Loop

```mermaid
flowchart TD
    A[Error Occurs / Test Fails] --> B[Isolate the Bug Manually]
    B --> C[Gather Specific Context]
    C --> D[Prompt AI with Context + Logs]
    D --> E{AI Proposes Fix}
    
    E -->|Band-aid Fix (Reject)| F[Identify Flaw & Re-prompt]
    F --> D
    
    E -->|Root Cause Fix (Accept)| G[Run Tests & Verify]
```

**How to gather context properly:**
1. Identify the exact file and the exact line number where the failure originates.
2. Extract *only* the relevant 10-15 lines of the error log.
3. Define the expected state vs. the actual state.

**Example Debugging Prompt:**
*"@paymentService.ts line 45. The Stripe API call is failing with `amount_too_large`. We are passing `45.50`. Stripe expects amounts in cents, not dollars. Fix the `processPayment` function to convert the decimal amount to an integer representing cents before calling the API."*

---

## 3. Hands-on Exercise: Reviewing AI Code

**Scenario:**
You asked the AI to write a function that retrieves a user's profile and their recent orders. The AI generated this Node.js/SQL code:

```javascript
async function getUserProfile(userId) {
    // Get user
    let user = await db.query("SELECT * FROM users WHERE id = " + userId);
    
    // Get orders
    let orders = await db.query("SELECT * FROM orders");
    let userOrders = [];
    for (let i = 0; i < orders.length; i++) {
        if (orders[i].user_id == userId) {
            userOrders.push(orders[i]);
        }
    }
    
    return { user, userOrders };
}
```

**Your Task:**
Identify at least **3 critical, production-breaking issues** that a Senior Engineer would reject in a Code Review.

> **Staff Engineer Solution & Rationale:**
> 1. **Security (SQL Injection):** `WHERE id = " + userId` concatenates raw input into the query. It MUST use parameterized queries (e.g., `WHERE id = ?`).
> 2. **Performance (N+1 / Data Fetching):** `SELECT * FROM orders` fetches the entire company's order history into RAM just to filter it in JavaScript. It MUST filter at the database level (`WHERE user_id = ?`).
> 3. **Resilience (Error Handling):** There is no `try/catch` block. If the database connection drops, this function throws an unhandled promise rejection, potentially crashing the entire Node server.
> 
> *Rationale: If you blindly accepted this AI code, your app would be instantly hackable, and it would crash due to out-of-memory errors as soon as you had more than a few thousand orders in the database.*

---

## 4. Review Checklist

- [ ] I will never blindly accept AI-generated code without reviewing it.
- [ ] I will explicitly check AI code for Security and Performance flaws.
- [ ] I will never paste a massive stack trace into an AI; I will isolate the error first.
- [ ] I understand that AI often provides "band-aid" fixes (like suppressing errors) instead of root-cause fixes.

**Next Steps:**
In Part 10, we cover how to take approved, reviewed code and prepare it for the harsh realities of Production Deployment.
