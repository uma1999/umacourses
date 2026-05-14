


# 🗄️🤖 SQL & GenAI Course
**🎯 Quality Education for Anyone, Anywhere, Anytime — 💫 with Comfort, Convenience at no Cost**

---

## 🤖 AI Persona Prompt – Socratic SQL Mentor

This prompt configures your AI Consultant (Tab 3) as a **Socratic SQL mentor** for the ACCELERATE phase. It will **never write code** – only explain logic, suggest strategies, and ask guiding questions.

---
## 🧭 The 6‑Step Configuration Workflow

Configuring your AI as a Socratic mentor is a **sequence of deliberate actions**, not a single copy‑paste. Follow these steps in order. Do not skip.

| Step | Action | Why It Matters |
|------|--------|----------------|
| **1** | Provide generic schema context | The AI needs to know the actual database structure (Education & E‑Commerce planets). |
| **2** | Feed character stories | Without the SQLVerse lore, the AI’s examples will be generic or hallucinated. |
| **3** | Copy the persona prompt | This locks in the “No‑Code” rules and Socratic tone. |
| **4** | Veracity check | Prevents the AI from suggesting invalid SQLite syntax. |
| **5** | Quick test | Confirms the AI is responding with logic, not code. |
| **6** | Recovery protocol | A safety net for when the AI accidentally writes code – turns mistakes into learning. |

**Prerequisite:** Complete the setup in [`BROWSER-OFFICE-ACCELERATE.md`](./BROWSER-OFFICE-ACCELERATE.md) (character stories + generic schema anchors). That file handles Steps 1 and 2.

> *“The Artisan doesn’t just configure once. The Artisan follows a ritual.”*

---

## 📋 Step 1: Provide Context (Generic Schemas)

**Feed the AI the core database schemas it needs for Modules 2 & 3.**  

You have followed the generic context feeding strategy in **BROWSER-OFFICE-ACCELERATE** markdown file and completed this step.

---

## 📖 Step 2: Feed the Character Stories (SQLVerse Lore)

**The AI needs to know the SQLVerse characters.**

You have already pasted the **CAPSTONE STORY** and **CAPSTONE STORY EXPANSION** in **BROWSER-OFFICE-ACCELERATE**


> 💡 **Why this matters:** Without this context, the AI may ignore the character names or hallucinate fake stories. Feeding the stories first ensures its examples are grounded in the SQLVerse universe.

---

## 🎭 Step 3: Copy the Persona Prompt

Copy and paste the following **Persona Prompt** into your AI Consultant **(Tab 3)** :

### 🎭 The Persona Prompt

```text
You are a Socratic SQL mentor. You are an expert data engineer with 20 years of experience.

**Your rules:**
- NEVER write full SQL code. Only explain logic, suggest approaches, and ask guiding questions.
- When the student asks for code, politely decline and ask: “What is the logical relationship you are trying to express?”
- Help the student break down problems into small, logical steps.
- Validate the student’s manual SQL by asking: “Does this handle NULLs? What about edge cases?”
- Use examples from the SQLVerse characters (Arjun, Geetha, Raj, Ravi, Annie, Simon) and their domains to ground your explanations.
- Maintain a tone that is encouraging but intellectually demanding. Use metaphors of ‘mining’ and ‘polishing’ to align with the SQLVerse Artisan theme.

**Your goal:** Turn the student into an independent data artisan who thinks in systems, not syntax.
```

---

## 🧠 Step 4: Veracity Check (Hallucination Prevention)

**Always verify AI suggestions, especially for SQLite.**  

If the AI suggests a function you don’t recognise, ask:  
*“Is that valid SQLite syntax? Show me the official documentation or an alternative.”*

**Common SQLite hallucination example:**  
AI may suggest `DATEDIFF()` – but SQLite uses `JULIANDAY()`.

> *“The Artisan never trusts blindly. The Artisan verifies.”*

---

## 🧪 Step 5: Quick Test

Ask the AI a Socratic question. It must **not** write code.

**Example prompt:**  
*“How would I find the names of students who are enrolled in courses taught by instructor_id 501?”*

**Expected response:** Logic and strategy – no SQL code. For example:  
*“You would join the students table to enrollments, then to courses, then filter by instructor_id. You need to decide which join type preserves students with no enrollments and which columns to select.”*

If the AI writes code, remind it: *“Explain the logic, don’t write SQL.”*

---

## 🔄 Step 6: Recovery Protocol (When AI Writes Code)

If the AI accidentally generates SQL:

1. **Stop** – do not copy the query.
2. **Redirect** – ask the AI to explain the logic conceptually instead.
3. **Rewrite** – write the SQL manually from scratch, based on the explanation.
4. **Log** – in your Socratic Journal, note what triggered the code and how you redirected.

> *“Discipline is not about never making mistakes. It’s about recovering with integrity.”*

---
## ✅ Ready

Once all 6 steps are complete, your AI is configured as a **Socratic SQL mentor.**  

Proceed with your learning.

---

*Part of our mission for 🎯 Quality Education for Anyone, Anywhere, Anytime — 💫 with Comfort, Convenience at no Cost.*

**Level 1 | ACCELERATE Phase | AI Persona Prompt**


---------------------

-------------------------------



------------------------------------







# 🗄️🤖 SQL & GenAI Course
**🎯 Quality Education for Anyone, Anywhere, Anytime — 💫 with Comfort, Convenience at no Cost**

---

## 🌐 Browser Office for ACCELERATE – AI Context Strategy

In ACCELERATE, your AI Consultant (Tab 3) needs **context** to give relevant, accurate advice. Without context, the AI will guess – and guessing leads to hallucinations, wrong syntax, and irrelevant examples.

The amount and type of context you provide depends on the module:

- **Modules 2 & 3** use the **same two databases** (`training_institution_sample.db` and `level1_estore_basic.db`) for every concept. You feed these schemas **once**, and the AI remembers them.
- **Module 4** uses **multiple specialised databases** (normalized E‑Store, self‑join databases, Tourism Planet, etc.). Each concept uses a different database. You feed **only the relevant schema** when you reach that file, and you **reset the AI’s memory** when switching databases.

This document is your step‑by‑step guide to feeding context correctly. Follow it in order.


---

## 🧭 The 5‑Step Context Workflow

| Step | Action |
|------|--------|
| **1** | Feed the character stories first |
| **2** | Understand the two‑tier context strategy |
| **3** | Load generic schema anchors (Modules 2 & 3) |
| **4** | Use file‑specific context boxes (Module 4) |
| **5** | Reset instruction when switching databases |
| **6** | Reference the schema anchor files |

---
## 📖 Step 1: Feed the Character Stories First (SQLVerse Lore)

The AI needs to know the SQLVerse characters before anything else.

Copy and paste these files into your AI Consultant (Tab 3):

**1. [`0-CAPSTONE-STORY.md`](../Module4-JoiningTables/2-practiceExercises/Capstone%20Reports/0-CAPSTONE-STORY.md)** – Arjun, Geetha, Raj  
**2. [`0-CAPSTONE-STORY-EXPANSION.md`](../Module4-JoiningTables/2-practiceExercises/Capstone%20Reports/0-CAPSTONE-STORY-EXPANSION.md)** – Ravi, Annie, Simon  

After each file, tell the AI: *“These are the SQLVerse characters. Use them to ground your examples.”*


> 💡 **Why this matters:** Without this context, the AI may ignore the character names or hallucinate fake stories. Feeding the stories first ensures its examples are grounded in the SQLVerse universe.

---

## 🧠 Step 2: Understand the Two‑Tier Context Strategy

ACCELERATE uses **two different strategies** for feeding database context, depending on the module:

| Tier | Modules | Strategy | Why |
|------|---------|----------|-----|
| **Tier 1** | Modules 2 & 3 | Generic schema anchors (once) | The same two databases (`training_institution_sample.db` and `level1_estore_basic.db`) are used for all concepts in these modules. Feed them once. |
| **Tier 2** | Module 4 | File‑specific context boxes (per concept) | Each concept uses a different specialised database (`level1_estore_normalized_MODULE4.db`, `level1_estore_self_join.db`, `tourism_planet_self_join.db`, etc.). Small table schemas provided at the top of each Socratic Mirror file. Feed only the relevant schema when you reach that file. |

### ✅ Why This Two‑Tier Strategy Works

| Benefit | Explanation |
|---------|-------------|
| **No overload** | You don’t feed all schemas at once. |
| **Context stays relevant** | Module 4 advice is grounded in the exact database you are using. |
| **AI doesn’t hallucinate** | It has the exact table and column names. |
| **Reset prevents contamination** | Switching domains is safe and clean. |
| **Repeatable** | The same pattern works for any future specialised database. |



> *“Don’t overload the AI. Give it only what it needs for the task at hand.”*

---

## 📦 Step 3: Load Generic Schema Anchors (Modules 2 & 3)

**Do this once at the start of ACCELERATE.** Feed these two files to the AI:

**- [`SCHEMA_ANCHOR_TRAINING_INSTITUTION_SAMPLE.md`](../SCHEMA_ANCHOR_TRAINING_INSTITUTION_SAMPLE.md)** – Education Planet (students, courses, enrollments, instructors, payments)
**- [`SCHEMA_ANCHOR_LEVEL1_ESTORE_BASIC.md`](../SCHEMA_ANCHOR_LEVEL1_ESTORE_BASIC.md)** – E‑Commerce Planet (customers, products, orders, order_items)

**How to feed them:**
- Open your AI Consultant (Tab 3).
- Copy and paste the contents of each file.

Tell the AI: *“These are the core schemas for ACCELERATE. Remember them.”*

> 💡 The AI will remember these for your entire session.

---

## 📂 Step 4: Use File‑Specific Context Boxes (Module 4)

For Module 4 concepts, **do not** use the generic anchors. Instead, each Socratic Mirror file in `01-The-Socratic-Mirror/ACQUIRE-MODULE4/` contains a **context box** at the top with:

- The exact database name
- The relevant tables and columns
- The business problem

**Example from `5-SelfJoin.md`:**

> **Database:** `level1_estore_self_join.db`  
> **Tables:** `employees` (employee_id, name, manager_id)  
> **Business problem:** Find employee–manager relationships using a self join.

Copy the entire context box and paste it into the AI before asking Socratic questions.

**Your workflow:**
1. Open the Socratic Mirror file (e.g., `5-SelfJoin.md`).
2. Copy the context box.
3. **Reset the AI’s memory for Module 4:**  
   Tell the AI: *“Discard previous specific table structures for Module 4. Here is the new context for [Concept Name].”*
4. Paste the context box.
5. Then ask your Socratic questions.

---

## 🔄 Step 5: Reset Instruction When Switching Databases

When you move from one Module 4 database to another (e.g., from `level1_estore_self_join.db` to `tourism_planet_self_join.db`), you must **reset the AI’s memory**.

When switching between Module 4 databases (e.g., from Self‑Join to Join Conditions), always reset the AI’s context. This prevents the AI from mixing up table structures from different domains. Say this exact phrase before pasting the new context box:

> *“Discard previous specific table structures for Module 4. Here is the new context for [Concept Name].”*

> 💡 **Pro‑Tip:** Without this reset, the AI may mix up tables from different databases and give incorrect advice.

---

## 🗄️ Step 6: Reference the Schema Anchor Files

The four specialised databases used in Module 4 have dedicated schema anchor files. They are located in `Module5-GenAI-Walkthrough/schema_anchors/`.

| Schema Anchor | Database | When to Use |
|---------------|----------|-------------|
| `SCHEMA_ANCHOR_ESTORE_NORMALIZED.md` | `level1_estore_normalized_MODULE4.db` | Join concepts (INNER, LEFT, chaining) |
| `SCHEMA_ANCHOR_ESTORE_SELF_JOIN.md` | `level1_estore_self_join.db` | Self‑join (employee hierarchy, product series) |
| `SCHEMA_ANCHOR_TOURISM_SELF_JOIN.md` | `tourism_planet_self_join.db` | Self‑join practice (tours, guides, customers) |
| `SCHEMA_ANCHOR_TRAINING_SELF_JOIN.md` | `training_institution_self_join.db` | Instructor mentorship self‑join |

You do not need to feed these files directly. The **context boxes** in the Socratic Mirror files already contain the essential table structures. Use these anchor files as a reference when you need the full schema.

---

## 🚀 Ready to Begin

- For **Modules 2 & 3**, feed the generic anchors once.
- For **Module 4**, follow the context box in each file and **reset the AI’s memory** before each new concept.

Now you understand how to feed context to the AI for every part of ACCELERATE. 

**The SQLVerse expands. Go build with clarity.**

---

*Part of our mission for 🎯 Quality Education for Anyone, Anywhere, Anytime — 💫 with Comfort, Convenience at no Cost.*

**Level 1 | ACCELERATE Phase | Browser Office – AI Context Strategy**






------------------------
