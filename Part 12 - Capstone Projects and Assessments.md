# Part 12: Capstone Projects & Assessments

This is your final assessment to prove you have transitioned from a Junior Developer to a Staff-level Vibe Coder. You will simulate a real-world enterprise task from start to finish. You are not required to generate the actual code, but you must generate the **Enterprise Trail** (the artifacts and prompts) that would perfectly guide an AI to build it.

---

## 1. The Capstone Assignment

**The Manager's Request:**
*"We are a fintech startup. We need an internal dashboard that allows our support agents to:
1. View a customer's transaction history.
2. Freeze a customer's account if suspicious activity is detected.
3. Refund specific transactions.
It needs to be highly secure, auditable, and built using our existing Next.js and PostgreSQL stack."*

---

## 2. Your Deliverables

To pass this mentorship, you must create a new folder called `Capstone_Dashboard` and create the following files inside it. 

### Phase A: Requirements Engineering
**Deliverable 1:** Create `BusinessRequirements.md`. Define the business goal and the user roles (e.g., Support Agent, Support Manager).
**Deliverable 2:** Create `EdgeCases.md`. 
* *Hint:* What happens if a support agent tries to refund a transaction that was already refunded? What happens if they try to freeze an account that is already frozen?

### Phase B: Architecture & Security
**Deliverable 3:** Create `SecurityRules.md`. 
* *Hint:* Fintech requires auditability. How do we prove *which* agent clicked the "Refund" button? Document the requirement for an `audit_logs` database table.
**Deliverable 4:** Create `DatabaseDesign.md` outlining the necessary tables (`users`, `transactions`, `audit_logs`).

### Phase C: Project Breakdown
**Deliverable 5:** Create `TaskBreakdown.md`. Break this project into at least 5 strict, sequential tasks. Remember to start with interfaces/schemas before moving to UI.

### Phase D: Prompt Simulation
**Deliverable 6:** Write the exact **Task 1 Implementation Prompt** you would use to initialize the AI.
* *Requirements:* It must include the Role, the Context (referencing your markdown files), the specific Task, the Constraints, and the Output Format.

---

## 3. Final Evaluation Criteria

As your Staff Engineer Mentor, this is how I would evaluate your Capstone deliverables:

* **Automatic Failure:** If you skipped creating the Markdown files and just wrote a prompt saying "Build a fintech dashboard."
* **Security Check:** Did you explicitly define the audit logging mechanism in your `SecurityRules.md`? If not, the AI wouldn't build it, and you'd fail compliance audits.
* **Context Engineering Check:** In your Task 1 Prompt, did you only feed the AI the context it needed for Task 1, or did you overwhelm it by asking it to build the UI and Database at the same time?
* **Constraint Check:** Did you use absolute language (MUST, NEVER) in your documentation?

---

## 4. Conclusion

If you can successfully complete the Capstone, you have mastered the system.

You are no longer a Junior Developer struggling to remember syntax or piecing together copied snippets from Stack Overflow. AI has commoditized the writing of code.

You are now an **AI Software Architect**. 
You manage AI agents the way a Tech Lead manages a team of developers. You ask the right questions, you enforce strict architectural boundaries, you curate context windows, you review outputs ruthlessly, and you build scalable, enterprise-grade systems.

**Welcome to the future of software engineering. End of Course.**
