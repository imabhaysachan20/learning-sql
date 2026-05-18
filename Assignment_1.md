# 📘 Assignment 1: Handling Large-Scale Nightly Billing Jobs

## Description
A nightly billing job processes **1 million records** in a database.  
Previously, the job **crashed midway**, and since all work was handled in a **single transaction** (or progress wasn’t saved), the system lost all progress and had to restart from zero.

---

## Core Issues
- Large volume of data (**1M records**)
- Long-running job prone to failure
- No intermediate commits (**progress not saved**)
- No recovery mechanism (**cannot resume from failure point**)

---

## 1. Batching

### Idea
Process records in **smaller chunks** (e.g., 10,000 rows at a time) instead of one large transaction.

### Example

First run: rows 1 – 10,000 → commit
Second run: rows 10,001 – 20,000 → commit


### 🎯 Why It Matters
- Reduces risk of full rollback  
- Improves performance  
- Ensures partial progress is saved  

---

## State-Based Filtering

### Idea
Use a column like `status` (e.g., `'pending'`, `'processed'`) to track progress.

### Example

Table:
id | status
1 | paid
2 | paid
3 | pending

UPDATE table
SET status = 'processed'
WHERE status = 'pending'
LIMIT 10000;


### Why It Matters
- Automatically skips already processed rows  
- No need for manual tracking  
- Simplifies restart logic  

---

## Idempotency

### Idea
Design updates so that **running them multiple times produces the same result**.

### Example

** Good**
UPDATE table
SET status = 'paid'
WHERE status = 'pending';

**Bad**

UPDATE table
SET amount = amount - 100;


### Why It Matters
- Prevents duplicate processing  
- Avoids data corruption  
- Enables safe retries  

---

## Checkpoint-Based Recovery

### Idea
Store the **last processed position** (e.g., `last_id`) in a separate table.

### Example
job_checkpoint:
job_name | last_id
billing_job | 400000

Next run:

UPDATE table
SET status = 'paid'
WHERE id > 400000
LIMIT 10000;


### Why It Matters
- Allows precise resume from failure point  
- Useful when status tracking isn’t available  

---

## Commit Strategy

### Idea
Commit after each batch instead of one large commit at the end.

### Example
job_checkpoint:
job_name | last_id
billing_job | 400000


Next run:

UPDATE table
SET status = 'paid'
WHERE id > 400000
LIMIT 10000;


### Why It Matters
- Allows precise resume from failure point  
- Useful when status tracking isn’t available  

---

## 🔹 5. Commit Strategy

### Idea
Commit after each batch instead of one large commit at the end.

### Example
Batch 1 → commit
Batch 2 → commit
Batch 3 → crash


### Why It Matters
- Completed batches remain saved  
- Minimizes data loss on failure  
- Improves reliability  

---

## Conclusion
By combining:
- **Batching**
- **State tracking**
- **Idempotent operations**
- **Checkpointing**
- **Frequent commits**

we can transform a fragile, failure-prone system into a **robust, fault-tolerant, and scalable data processing pipeline**.