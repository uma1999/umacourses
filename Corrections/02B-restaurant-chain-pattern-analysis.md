

# 🗄️🤖 SQL & GenAI Course
**🎯 Quality Education for Anyone, Anywhere, Anytime — 💫 with Comfort, Convenience at no Cost**

---

# 02B — Restaurant Chain Pattern Analysis

## Structured Grain Diagnosis in Practice

**Document Type:** Grain Foundations — Part 2B of 3  
**Version:** 1.0  
**Status:** FROZEN  
**Location:** `SQLVerse-Architects-Blueprint/01-GRAIN-FOUNDATIONS/`  
**Governing Standard:** `SQLVERSE-ARCH-FW-001`

---

## 🔗 From 02A

In **02A — Grain in Practice Concepts**, you learned:

- The difference between **Table Grain** and **Query Grain**.
- The diagnostic framework of **Required Grain vs. Available Grain**.
- The Restaurant Chain domain and its three tables with different grains.

Now we apply that framework to real business questions.

**You will learn:**

- How to identify the required grain for each business question.
- How to map the available grain from the tables.
- How to decide between safe aggregation, direct joining, and aggregation‑first strategies.
- How to verify that your query grain is correct using count validation.

---

## 📌 Purpose

This document is a **pattern analysis laboratory**.

You have learned the diagnostic framework in **02A**. Now you will apply it to six real business questions — each with a different grain, a different risk profile, and a different analytical pattern.

**By the end of this document, you will be able to:**
- Diagnose the required and available grain for any multi‑table question.
- Choose between safe aggregation, direct joining, and aggregation‑first strategies.
- Verify your query grain using count validation.
- Recognise the fan‑out trap and prevent it.

**This is where theory meets practice.**

---

## 🍽️ The Restaurant Chain — SpiceRoute

### Business Context

You have a restaurant chain **"SpiceRoute"** with multiple branches in Chennai:

- Branches: `SR-Anna Nagar`, `SR-T Nagar`, `SR-Adyar`, etc.
- Each branch takes many **bills** per day.
- Each bill has multiple **line items** (dishes, drinks, etc.).

### The Tables (Available grain)

#### 1. `branches` table
Grain: **one row = one branch**

| branch_id | branch_name   | city    |
|----------:|--------------:|--------:|
| B01       | SR-Anna Nagar | Chennai |
| B02       | SR-T Nagar    | Chennai |
| B03       | SR-Adyar      | Chennai |

---

#### 2. `bills` table
Grain: **one row = one bill**

| bill_id | branch_id | bill_time           | total_amount |
|--------:|----------:|---------------------|-------------:|
| 5001    | B01       | 2026-09-01 12:15:00 | 850          |
| 5002    | B01       | 2026-09-01 12:40:00 | 620          |
| 5003    | B02       | 2026-09-01 13:05:00 | 1100         |
| 5004    | B01       | 2026-09-02 19:10:00 | 940          |

---

#### 3. `bill_items` table
Grain: **one row = one line item in a bill**

| bill_id | item_id | menu_item_id | quantity | unit_price |
|--------:|--------:|-------------:|---------:|-----------:|
| 5001    | 1       | M10          | 2        | 200        |
| 5001    | 2       | M25          | 1        | 250        |
| 5001    | 3       | M30          | 2        | 100        |
| 5002    | 1       | M10          | 1        | 200        |
| 5002    | 2       | M40          | 2        | 210        |
| 5003    | 1       | M25          | 3        | 250        |
| 5004    | 1       | M10          | 1        | 200        |
| 5004    | 2       | M30          | 3        | 100        |
| 5004    | 3       | M50          | 1        | 240        |

---

### The Grain Map

| Table | Grain | Type |
|-------|-------|------|
| `branches` | One branch | Entity (Master) |
| `bills` | One bill | Event (Master) |
| `bill_items` | One line item in a bill | Transaction (Detail) |

**Relationships:**

```text
branches (1) ──── (N) bills (1) ──── (N) bill_items
```

---
## 📋 The Restaurant Chain — Business Use Cases

We will examine **six business questions**, each with a different grain and a different analytical challenge.

| # | Use Case | Required Grain | Complexity |
|---|----------|----------------|------------|
| **1** | Branch‑Month Sales | `(branch_id, month)` | Low — Master aggregation |
| **2** | Bill‑Level Report with Item Details | `(bill_id, menu_item_id)` | Medium — Fan‑out trap |
| **3** | Branch‑Item‑Month Sales | `(branch_id, menu_item_id, month)` | Medium — Detail aggregation first |
| **4** | Most Popular Menu Items per Branch | `(branch_id, menu_item_id)` | Medium — Detail aggregation by entity |
| **5** | Most Popular Menu Items Across All Branches | `(menu_item_id)` | Low — Global aggregation |
| **6** | Top 3 Menu Items per Branch | `(branch_id, menu_item_id)` with ranking | High — Top‑N per group |

---

**Each use case follows the same structure:**

```text
Business Question
      ↓
Tables Involved
      ↓
Relationships
      ↓
Required Grain
      ↓
Available Grain
      ↓
Grain Diagnosis
      ↓
Algorithm
      ↓
Verification Query
      ↓
Query
      ↓
Pattern Reflection
```
---

## Use Case 1: Branch‑Month Sales (Master Table Only)

**Business Question:**

> *"What was the total sales per branch per month for the last year?"*

---

**Tables Involved:**

| Table | Grain |
|-------|-------|
| `bills` | One row = one bill |
| `branches` | One row = one branch |

---

**Relationships:**

```text
branches (1) ──── (N) bills
```

---

**Required Grain:**

> One row = one branch in one month
>
> Keys: `(branch_id, month)`
>
> Measure: `total_sales`

---

**Available Grain:**

| Table | Grain |
|-------|-------|
| `bills` | One row = one bill (finer than required) |
| `branches` | One row = one branch (matches required entity grain) |

---

**Grain Diagnosis:**

| Condition | Status |
|-----------|--------|
| Required Grain vs Available Grain | Required is **coarser** than available (bill → branch‑month) |
| Risk | **None** — safe aggregation |

---

**Algorithm:**

1. Start with `bills` table (bill grain).
2. Join `branches` to get branch name.
3. Group by `branch_id` and month (`strftime('%Y-%m', bill_time)`).
4. `SUM(total_amount)` to aggregate to required grain.

---

**Verification Query:**

```sql
-- Verify: Count the number of distinct branch‑month combinations
SELECT COUNT(*) AS expected_rows
FROM (
    SELECT DISTINCT branch_id, strftime('%Y-%m', bill_time) AS month
    FROM bills
) AS grain_check;
```

**What this checks:** The number of rows in the result should equal the number of distinct `(branch_id, month)` combinations.

---

**Query:**

```sql
SELECT
    b.branch_id,
    br.branch_name,
    strftime('%Y-%m', b.bill_time) AS month,
    ROUND(SUM(b.total_amount), 2) AS total_sales
FROM bills b
JOIN branches br ON b.branch_id = br.branch_id
GROUP BY
    b.branch_id,
    strftime('%Y-%m', b.bill_time)
ORDER BY b.branch_id, month;
```

---

**Pattern Reflection:**

| Pattern | Description |
|---------|-------------|
| **Master Table Aggregation** | When required grain is coarser than available, use `GROUP BY` to roll up |
| **Rule** | You can always aggregate from finer to coarser grain |
| **Common Mistake** | Forgetting to group by the correct level |

---

## Use Case 2: Bill‑Level Report with Item Details (The Fan‑Out Trap)

**Business Question:**

> *"For each bill, show the branch name, total amount, and the items in the bill."*

---

**Tables Involved:**

| Table | Grain |
|-------|-------|
| `bills` | One row = one bill |
| `bill_items` | One row = one line item in a bill |
| `branches` | One row = one branch |

---

**Relationships:**

```text
branches (1) ──── (N) bills (1) ──── (N) bill_items
```

---

**Required Grain:**

> One row = one bill with its line items
>
> Keys: `(bill_id, menu_item_id)`
>
> Measures: `total_amount` (bill‑level), `quantity` (item‑level)

---

**Available Grain:**

| Table | Grain |
|-------|-------|
| `bills` | One row = one bill |
| `bill_items` | One row = one bill line item (finer) |
| `branches` | One row = one branch |

---

**Grain Diagnosis:**

| Condition | Status |
|-----------|--------|
| Required Grain vs Available Grain | Required is **finer** than `bills`, **same** as `bill_items` |
| Risk | **High** — joining coarser `bills` to finer `bill_items` creates fan‑out |

---

**Algorithm:**

1. Start with `bill_items` (the grain matches the required level).
2. Join `bills` to get bill‑level data (branch, total_amount).
3. Join `branches` to get branch name.
4. **Do not** aggregate `total_amount` at this grain — it will be repeated.

---

**Verification Query:**

```sql
-- Verify: Count the number of rows per bill in the result
SELECT bill_id, COUNT(*) AS row_count
FROM (
    SELECT
        b.bill_id,
        br.branch_name,
        b.total_amount,
        bi.menu_item_id,
        bi.quantity
    FROM bills b
    JOIN branches br ON b.branch_id = br.branch_id
    JOIN bill_items bi ON b.bill_id = bi.bill_id
) AS result
GROUP BY bill_id;
```

**What this checks:** Each bill should have exactly as many rows as it has items. If a bill has 3 items, the result should have 3 rows for that bill.

---

**Query:**

```sql
SELECT
    b.bill_id,
    br.branch_name,
    b.total_amount,
    bi.menu_item_id,
    bi.quantity,
    bi.unit_price
FROM bills b
JOIN branches br ON b.branch_id = br.branch_id
JOIN bill_items bi ON b.bill_id = bi.bill_id
ORDER BY b.bill_id, bi.menu_item_id;
```

---

**Pattern Reflection:**

| Pattern | Description |
|---------|-------------|
| **Coarse-to-Fine Join** | Joining a coarser table to a finer table changes the grain of the result |
| **Rule** | If your metric is defined at the coarser grain, **do not sum it** after fan‑out |
| **Common Mistake** | Summing `total_amount` after joining to `bill_items` → double counting |

---

## Use Case 3: Branch‑Item‑Month Sales (Aggregate Detail First)

**Business Question:**

> *"For each branch and menu item, show total quantity sold and total sales per month."*

---

**Tables Involved:**

| Table | Grain |
|-------|-------|
| `bill_items` | One row = one line item in a bill |
| `bills` | One row = one bill |
| `branches` | One row = one branch |

---

**Relationships:**

```text
branches (1) ──── (N) bills (1) ──── (N) bill_items
```

---

**Required Grain:**

> One row = one branch in one month for one menu item
>
> Keys: `(branch_id, menu_item_id, month)`
>
> Measures: `total_quantity_sold`, `total_sales`

---

**Available Grain:**

| Table | Grain |
|-------|-------|
| `bill_items` | One row = one bill line item |
| `bills` | One row = one bill |
| `branches` | One row = one branch |

---

**Grain Diagnosis:**

| Condition | Status |
|-----------|--------|
| Required Grain vs Available Grain | Required is **coarser** than `bill_items` (line item → branch‑item‑month) |
| Risk | **Low** — safe aggregation, but must include both `bill_items` and `bills` |

---

**Algorithm:**

1. Start with `bill_items` (the finest grain).
2. Join `bills` to get `branch_id` and `bill_time`.
3. Group by `branch_id`, `menu_item_id`, and month.
4. `SUM(quantity)` and `SUM(quantity * unit_price)` to aggregate.

---

**Verification Query:**

```sql
-- Verify: Count distinct branch‑item‑month combinations
SELECT COUNT(*) AS expected_rows
FROM (
    SELECT DISTINCT
        b.branch_id,
        bi.menu_item_id,
        strftime('%Y-%m', b.bill_time) AS month
    FROM bill_items bi
    JOIN bills b ON bi.bill_id = b.bill_id
) AS grain_check;
```

**What this checks:** The number of rows in the result should equal the number of distinct `(branch_id, menu_item_id, month)` combinations.

---

**Query:**

```sql
SELECT
    b.branch_id,
    br.branch_name,
    bi.menu_item_id,
    strftime('%Y-%m', b.bill_time) AS month,
    SUM(bi.quantity) AS total_quantity_sold,
    ROUND(SUM(bi.quantity * bi.unit_price), 2) AS total_sales
FROM bill_items bi
JOIN bills b ON bi.bill_id = b.bill_id
JOIN branches br ON b.branch_id = br.branch_id
GROUP BY
    b.branch_id,
    bi.menu_item_id,
    strftime('%Y-%m', b.bill_time)
ORDER BY
    b.branch_id,
    bi.menu_item_id,
    month;
```

---

**Pattern Reflection:**

| Pattern | Description |
|---------|-------------|
| **Detail Table Aggregation First** | When joining master and detail tables, aggregate the detail table first |
| **Rule** | The target grain determines where aggregation belongs |
| **Common Mistake** | Joining first, then aggregating — which can lead to fan‑out |

---

## Use Case 4: Most Popular Menu Items per Branch

**Business Question:**

> *"Which menu items are most popular at each branch?"*

---

**Tables Involved:**

| Table | Grain |
|-------|-------|
| `bill_items` | One row = one line item in a bill |
| `bills` | One row = one bill |
| `branches` | One row = one branch |

---

**Relationships:**

```text
branches (1) ──── (N) bills (1) ──── (N) bill_items
```

---

**Required Grain:**

> One row = one branch for one menu item
>
> Keys: `(branch_id, menu_item_id)`
>
> Measure: `total_quantity_sold`

---

**Available Grain:**

| Table | Grain |
|-------|-------|
| `bill_items` | One row = one bill line item |
| `bills` | One row = one bill |
| `branches` | One row = one branch |

---

**Grain Diagnosis:**

| Condition | Status |
|-----------|--------|
| Required Grain vs Available Grain | Required is **coarser** than `bill_items` (line item → branch‑item) |
| Risk | **Low** — safe aggregation |

---

**Algorithm:**

1. Start with `bill_items` (finest grain).
2. Join `bills` to get `branch_id`.
3. Join `branches` to get branch name.
4. Group by `branch_id` and `menu_item_id`.
5. `SUM(quantity)` to aggregate.

---

**Verification Query:**

```sql
-- Verify: Count distinct branch‑item combinations
SELECT COUNT(*) AS expected_rows
FROM (
    SELECT DISTINCT
        b.branch_id,
        bi.menu_item_id
    FROM bill_items bi
    JOIN bills b ON bi.bill_id = b.bill_id
) AS grain_check;
```

**What this checks:** The number of rows in the result should equal the number of distinct `(branch_id, menu_item_id)` combinations.

---

**Query:**

```sql
SELECT
    b.branch_id,
    br.branch_name,
    bi.menu_item_id,
    SUM(bi.quantity) AS total_quantity_sold
FROM bill_items bi
JOIN bills b ON bi.bill_id = b.bill_id
JOIN branches br ON b.branch_id = br.branch_id
GROUP BY
    b.branch_id,
    bi.menu_item_id
ORDER BY
    b.branch_id,
    total_quantity_sold DESC;
```

---

**Pattern Reflection:**

| Pattern | Description |
|---------|-------------|
| **Detail Aggregation by Entity** | Aggregating detail data to entity level (branch‑item) |
| **Rule** | Always group by the entity keys that define the required grain |

---

## Use Case 5: Most Popular Menu Items Across All Branches

**Business Question:**

> *"Which menu items are popular across all branches?"*

---

**Tables Involved:**

| Table | Grain |
|-------|-------|
| `bill_items` | One row = one line item in a bill |
| `bills` | One row = one bill |

---

**Relationships:**

```text
bills (1) ──── (N) bill_items
```

---

**Required Grain:**

> One row = one menu item (across all branches)
>
> Keys: `(menu_item_id)`
>
> Measure: `total_quantity_sold`

---

**Available Grain:**

| Table | Grain |
|-------|-------|
| `bill_items` | One row = one bill line item |

---

**Grain Diagnosis:**

| Condition | Status |
|-----------|--------|
| Required Grain vs Available Grain | Required is **coarser** than `bill_items` (line item → item) |
| Risk | **None** — safe aggregation |

---

**Algorithm:**

1. Start with `bill_items`.
2. Join `bills` to ensure only valid bills (if needed).
3. Group by `menu_item_id`.
4. `SUM(quantity)` to aggregate.

---

**Verification Query:**

```sql
-- Verify: Count distinct menu items
SELECT COUNT(DISTINCT menu_item_id) AS expected_rows
FROM bill_items;
```

**What this checks:** The number of rows in the result should equal the number of distinct menu items.

---

**Query:**

```sql
SELECT
    bi.menu_item_id,
    SUM(bi.quantity) AS total_quantity_sold
FROM bill_items bi
JOIN bills b ON bi.bill_id = b.bill_id
GROUP BY bi.menu_item_id
ORDER BY total_quantity_sold DESC;
```

---

**Pattern Reflection:**

| Pattern | Description |
|---------|-------------|
| **Global Aggregation** | Aggregating across all branches to get overall popularity |
| **Rule** | When no entity filter is needed, group only by the dimension of interest |

---

## Use Case 6: Top 3 Menu Items per Branch

**Business Question:**

> *"What are the top 3 menu items in each branch?"*

---

**Tables Involved:**

| Table | Grain |
|-------|-------|
| `bill_items` | One row = one line item in a bill |
| `bills` | One row = one bill |
| `branches` | One row = one branch |

---

**Relationships:**

```text
branches (1) ──── (N) bills (1) ──── (N) bill_items
```

---

**Required Grain:**

> One row = one branch with its top 3 menu items
>
> Keys: `(branch_id, menu_item_id)` — filtered to top 3 per branch
>
> Measure: `total_quantity_sold`

---

**Available Grain:**

| Table | Grain |
|-------|-------|
| `bill_items` | One row = one bill line item |
| `bills` | One row = one bill |
| `branches` | One row = one branch |

---

**Grain Diagnosis:**

| Condition | Status |
|-----------|--------|
| Required Grain vs Available Grain | Required is **coarser** than `bill_items` (line item → branch‑item, with ranking) |
| Risk | **Low** — safe aggregation, but requires window function (Level 2) or multiple queries (Level 1) |

---

**Algorithm (Level 1 Approach):**

1. Start with `bill_items`.
2. Join `bills` and `branches`.
3. Group by `branch_id` and `menu_item_id` to get total quantity per branch‑item.
4. For Level 1, use multiple queries (one per branch) or a subquery to extract top 3.

**Algorithm (Level 2 Approach — Preview):**

1. Start with `bill_items`.
2. Join `bills` and `branches`.
3. Group by `branch_id` and `menu_item_id`.
4. Use `ROW_NUMBER()` window function to rank items within each branch.
5. Filter to rank ≤ 3.

---

**Level 1 Query (Multiple Queries):**

```sql
-- Query for branch B01 (repeat for each branch)
SELECT
    br.branch_name,
    bi.menu_item_id,
    SUM(bi.quantity) AS total_quantity_sold
FROM bill_items bi
JOIN bills b ON bi.bill_id = b.bill_id
JOIN branches br ON b.branch_id = br.branch_id
WHERE b.branch_id = 'B01'
GROUP BY bi.menu_item_id
ORDER BY total_quantity_sold DESC
LIMIT 3;
```

---

**Level 2 Query (Preview — Using Window Functions):**

```sql
-- Level 2 preview: CTE + ROW_NUMBER
WITH branch_item_sales AS (
    SELECT
        b.branch_id,
        br.branch_name,
        bi.menu_item_id,
        SUM(bi.quantity) AS total_quantity_sold
    FROM bill_items bi
    JOIN bills b ON bi.bill_id = b.bill_id
    JOIN branches br ON b.branch_id = br.branch_id
    GROUP BY
        b.branch_id,
        bi.menu_item_id
),
ranked AS (
    SELECT
        branch_id,
        branch_name,
        menu_item_id,
        total_quantity_sold,
        ROW_NUMBER() OVER (PARTITION BY branch_id ORDER BY total_quantity_sold DESC) AS rank
    FROM branch_item_sales
)
SELECT
    branch_name,
    menu_item_id,
    total_quantity_sold
FROM ranked
WHERE rank <= 3
ORDER BY branch_id, rank;
```

---

**Pattern Reflection:**

| Pattern | Description |
|---------|-------------|
| **Top‑N per Group** | Requires aggregation first, then ranking within groups |
| **Rule** | Level 1: multiple queries or subqueries. Level 2: window functions |
| **Common Mistake** | Trying to use `LIMIT` without `GROUP BY` or without per‑group ranking |

---
## 🧠 The Artisan's Checklist — Before You Write a Query

Before writing any query that involves multiple tables or aggregations:

1. **Write the required grain in one sentence**
   - *"One row per branch per month."*
   - *"One row per bill."*
   - *"One row per branch, menu item, and month."*

2. **Write each table's available grain**
   - `bills`: one row per bill.
   - `bill_items`: one row per bill line item.
   - `branches`: one row per branch.

3. **Check joins**
   - If you join a coarser table (`bills`) to a finer table (`bill_items`), the result grain becomes finer.
   - If your metric (like `total_amount`) is defined at the coarser grain, **do not sum it** after fanning out to the finer grain.

4. **Validate with counts**
   - Count bills per branch per month from `bills` alone.
   - Count again after joins. If the number of "bill equivalents" changes, your grain has shifted — adjust your aggregation.

---

**The Lesson:**

```text
FINE GRAIN
(Line Items)
      │
      ▼
  Aggregated Upward
      │
      ▼
COARSE GRAIN
(Daily Store Summary)

❌ Cannot go back down
```

This is why **understanding grain is not optional**. If you lose detail, you lose the ability to answer certain questions.

---

## 🪞 Consolidated Pattern Reflection

| Pattern | Rule | Common Mistake |
|---------|------|----------------|
| **Master Table Aggregation** | You can always aggregate from finer to coarser grain | Forgetting to group by the correct level |
| **Coarse-to-Fine Join** | Joining a coarser table to a finer table changes the grain of the result | Summing a coarse‑grain metric after fan‑out |
| **Detail Table Aggregation** | The target grain determines where aggregation belongs. Aggregate to match the required grain, not necessarily the master grain. | Aggregating to the wrong level |
| **Detail Aggregation by Entity** | Always group by the entity keys that define the required grain | Omitting a key from `GROUP BY` |
| **Global Aggregation** | When no entity filter is needed, group only by the dimension of interest | Over‑grouping |
| **Top‑N per Group** | Requires aggregation first, then ranking within groups | Using `LIMIT` without per‑group ranking |

---

### 🏛️ The Artisan's Grain Principle

> **The target grain determines where aggregation belongs.**

Not:

> *"Always aggregate the many side first."*

Because the required grain may be finer than the master grain (e.g., Branch × Item × Month), and aggregation must happen at that specific level.

**The Rule:**

1. Identify the **Required Grain**.
2. Identify the **Available Grain** of each table.
3. Determine where aggregation must occur to align the two.
4. Execute the aggregation at the correct level.
5. Verify with a count query.

---

## 🏛️ The Artisan's Rule for Restaurant Data

```text
BEFORE YOU JOIN, KNOW THE GRAIN.

Master tables (branches, bills) → one row per entity.
Detail tables (bill_items) → many rows per master entity.

If your business question requires data from both:
1. Identify the required grain.
2. Identify each table's available grain.
3. The target grain determines where aggregation belongs.
4. Then join.
5. Verify with a count query.
```

---

## 🔁 Bridge to File 3

You have now applied the Required vs Available Grain framework to six real business questions across three levels of complexity.

In **File 3 — Advanced Grain + Joins**, you will:

- Explore fan‑out in 1:1, 1:N, and M:N relationships.
- Apply these principles to the Banking domain from ACQUIRE File 3.
- Learn the Artisan's Guardrail for safe joins across grains.
- Examine disaggregation limits, temporal grain, and history preservation.

**Next: `03-advanced-grain-evolution-with-joins.md`**

---

*Part of our mission for 🎯 Quality Education for Anyone, Anywhere, Anytime — 💫 with Comfort, Convenience at no Cost.*

**SQLVerse | Architecture | Grain Foundations | 02B — Restaurant Chain Pattern Analysis**