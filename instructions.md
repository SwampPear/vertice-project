# 2026 Vertice SWE Test Project

## Context

We're a small, fast-moving team building a data-heavy SaaS platform. We ship fast, use AI tools daily, and care deeply about code quality and system design. This project is designed to simulate a real problem — the kind of thing that would land on your plate in week two.

**Stack:** React + TypeScript + Node.js + SQLite. Using our stack is a plus but not required — use whatever you're most productive in.

**AI tools:** Use whatever you want — Claude Code, Cursor, etc. We expect you to.

---

## The Problem

Build a **dynamic segment builder**.

Our platform manages a large dataset of customer records. Users need to create **segments** — saved groups of customers defined by filter rules — and those segments need to stay current as the underlying data changes.

The catch: the underlying data gets refreshed weekly via a batch process that takes time to run. While that batch is running, users are actively working with their segments in production — editing filters, pinning customers, removing customers. The system needs to handle both of these things happening at the same time without data loss or inconsistency.

### The dataset

Write a seed script that generates **200,000+ customer records** in SQLite with fields like:

- Name, email, signup date, region
- Plan type (free, starter, pro, enterprise)
- Monthly revenue, lifetime value
- Last active date, login count
- Tags (array — e.g., ["churned", "high-value", "enterprise"])
- Any other fields you think would make the filtering interesting

The data should have realistic distributions — not all random. Think: most users on free plans, revenue following a power law, activity clustering around certain dates, regional skews.

### What users need to be able to do

1. **Explore the dataset through aggregate metrics** — not a table of individual rows, but charts and stats that summarize the data. Think: customer count by plan type, revenue distribution by region, signups over time, average lifetime value, etc. These metrics should update in response to applied filters.

2. **Build filters to narrow the dataset** — a filter builder that lets users construct rules with AND/OR grouping and nesting.
   - Example: "plan is 'enterprise' AND revenue > 10,000 AND region is one of ['US', 'CA']"
   - Support various operators depending on field type (equals, not equals, greater than, less than, contains, is one of, is empty, between, etc.)
   - The aggregate metrics should update to reflect the filtered subset

3. **Save filter sets as segments** — users can save their current filter configuration as a named segment they can come back to later. Loading a segment re-applies its filters and shows the updated metrics.

4. **Pin and exclude** specific customers on a segment — manual overrides that persist regardless of what the filter rules say

5. **Show segment history** — after a data refresh, users should be able to see how a segment's membership changed (who was added, who dropped off, what the count was before vs. after)

### The data refresh

This is the core challenge of the project.

New customer data arrives weekly via a batch load — a separate process that updates the underlying customer records (some change, some are added, some are removed). This load takes time (simulate at least 1 minute of processing) and should be triggered independently (a separate script, endpoint, or API).

While the batch load is running, users are still actively using the application — creating segments, editing filters, pinning and excluding customers. Their changes cannot be lost or overwritten by the batch load, and the application should remain usable during it.

After the load completes, segments should reflect the new data. How and when re-segmentation happens is up to you.

Build a mechanism to simulate this: trigger a data load with realistic changes (maybe 5-10% of records change, a few hundred are added, a few hundred are removed), and demonstrate that the system handles it correctly even while a user is making changes.

### What to build

- Database schema and seed script
- Backend API
- Frontend UI (functional and clear — doesn't need to be pixel-perfect or responsive)
- The data refresh / batch process and its interaction with live user activity

### What NOT to build

- Authentication — assume a logged-in user
- Real-time updates — polling or manual refresh is fine
- Deployment — local dev setup only

---

## Deliverables

Push to a GitHub repo. Include a README with setup instructions — it should be runnable with a few commands.

**Time expectation:** Don't slave away for a week on this — a few hours should be enough. It doesn't need to be perfect. We're more interested in your thinking and how you approach the problem.

We'll review the code and, if we move forward, schedule a 30-minute follow-up conversation to talk about your work — the decisions you made, tradeoffs, what you'd do differently with more time, and how you approached the problem.

---

## How We Evaluate

**Does it work?** Can we run it, create segments, refresh data, and see the results?

**How is the code structured?** We're looking at how the system is organized — not whether it follows any specific pattern, but whether the structure holds up. If we needed to add a new filter operator or a new field type next week, how much would we have to touch?

**How does it handle the hard parts?** The interesting problems in this project aren't the CRUD — they're things like: what happens to a user's in-progress edits when a batch refresh lands, how filtering performs against 200k rows, and how the system stays consistent when data is changing underneath it.

**Does it feel like a product?** The UI doesn't need to be beautiful, but it should be clear and usable. Can a non-technical person figure out how to create a segment?

**How simple is it?** Simplicity is beauty. We'd rather see a clean, minimal solution than an over-engineered one. Did you solve the problem without introducing too much verbosity or unnecessary complexity?

**Is the code the kind you'd want to maintain?** We'll read it like we'd review a PR from a teammate.
