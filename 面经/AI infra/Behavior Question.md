**1. Handling a Pipeline Failure in Production**

> Your ETL pipeline silently failed overnight and downstream teams showed up to work with stale or missing data. You were the on-call engineer.

_Tests:_ Ownership, incident response, communication under pressure.

During a backend project where I built a simulated YouTube platform — including video indexing and search functionality — I ran into a critical failure during the final stretch before our deadline. The search feature stopped returning correct results entirely, and I needed to diagnose and fix it fast.

My first step was figuring out whether this was a transient fault or a deeper systemic issue. After seeing it fail consistently across multiple test cases, I ruled out transient and knew I had a real bug. Rather than scanning the entire codebase, I used a divide and conquer approach — splitting the search pipeline into stages and testing each one in isolation, starting with the parts I knew were most likely to break. That led me straight to the KMP string matching algorithm, where I found the failure table was being constructed incorrectly for certain edge case patterns, causing the algorithm to skip valid matches.

I fixed the bug, but while I was in that section of the code I also noticed the query logic was doing redundant scans that could be eliminated. I refactored those on the spot. After the fix, our search accuracy was fully restored and query performance improved by around 30%. We delivered the project on time, and the optimizations I made along the way actually put us ahead of our original performance targets.

---

**2. Pushing Back on Unrealistic Data Requests**

> A senior stakeholder asked you to deliver a "quick" data model in 2 days that would actually require schema redesign, backfilling, and coordination with 3 teams.

_Tests:_ Managing up, setting expectations, technical advocacy.

---

**3. Inheriting a Messy Legacy Codebase**

> You joined a team and took ownership of a spaghetti pipeline — no docs, inconsistent naming, no tests, and it ran on a cron job no one fully understood.

_Tests:_ Ambiguity tolerance, technical judgment, prioritization.

---

**4. Disagreeing with a Technical Decision**

> Your team decided to use a tool or architecture you believed was the wrong choice for the scale you were targeting (e.g., using a flat file approach instead of a proper warehouse).

_Tests:_ Conflict navigation, collaboration, knowing when to commit vs. escalate.

---

**5. Improving Data Quality Proactively**

> You noticed recurring data quality issues that no one had formally flagged — missing values, duplicates, schema drift — that were silently corrupting downstream reports.

_Tests:_ Initiative, cross-functional impact, building for reliability.