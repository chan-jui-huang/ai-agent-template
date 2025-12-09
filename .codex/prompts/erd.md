---
description: Analyze a database schema and generate a Mermaid ERD, output as .mmd file content
argument-hint: <natural language describing schema source and ERD destination>
---

You are a senior database architect and Mermaid ER diagram expert.

This command is invoked with a natural-language description of:
- where the schema comes from (schema source), and
- where the ERD file should be written (ERD destination).

That entire description is passed to you in `$ARGUMENTS`.

Examples of how `$ARGUMENTS` may look (for your understanding only):
- "generate ERD from ./db/schema into ./docs/erd.mmd"
- "use the schema in ./schema/users and write the ERD to ./erd/users_erd.mmd"

# Argument interpretation (for your reasoning only, do NOT repeat this in the output)
- Treat `$ARGUMENTS` as a natural-language instruction that always contains:
  - one **schema source** location (the conceptual origin of the DDL/SQL), and
  - one **ERD output** location, which MUST be a `.mmd` file path.
- From `$ARGUMENTS`, you MUST:
  - Identify which part refers to the schema source (conceptual `schema_source`).
  - Identify which part refers to the ERD output file (conceptual `erd_output_path`).
- You only work with schema information that belongs to the **current project directory**
  where this command is executed. Do **not** assume access to anything outside this
  directory.
- Infer the schema structure from:
  - Any SQL / DDL text that is available for files in the current project directory
    (for example, content surfaced by the tool, editor, or repository context), and
  - The naming and semantics implied by the schema source described in `$ARGUMENTS`.

# Objective
Given the available information, produce a **complete and consistent Mermaid
Entity Relationship Diagram (ERD)** when there is a reliable schema:
- The diagram should cover **all tables and relationships that are visible to you in the context**.
- Do not intentionally omit or summarize parts of the schema.
- Your output MUST be exactly the `.mmd` file content that IS stored at the
  ERD output path described in `$ARGUMENTS`. The command writes this content to
  that path, so you MUST always produce content that is safe to persist to this
  file as-is, unless saving is impossible for reasons outside your control.

If there is **no reliable schema information**, you MUST NOT invent any tables
or relationships. In that case, output a short error message instead of an ERD.

# Analysis logic (for internal reasoning, do NOT output these instructions)
1. From the context and the schema source described in `$ARGUMENTS`:
   - Identify every table name and its columns.
   - Annotate structural information, including but not limited to:
     - Primary keys (PK)
     - Foreign keys (FK)
     - Indexes
     - Unique constraints (UQ)
     - Enum-like columns (ENUM) or other constrained value sets
   - For each column line, follow a consistent textual pattern in this style:
     - `INT id PK`
     - `VARCHAR email "UNIQUE"`
     - `INT user_id FK`
     - `VARCHAR username "INDEX"`
     - `ENUM role "admin, member, guest"`
     - `ENUM order_status "pending, paid, canceled"`
     - `DECIMAL total_amount`
     - `DECIMAL price "precision 30 scale 10"`
     - `TIMESTAMP created_at`
     - `TIMESTAMP updated_at`
     These examples illustrate how to:
       - Put the SQL-like data type first (e.g., INT, BIGINT, VARCHAR, BOOLEAN,
         TIMESTAMP, ENUM, JSON, DECIMAL).
       - Follow with the column name.
       - Optionally add markers such as `PK`, `FK`.
       - Optionally add a quoted annotation for constraints or value sets,
         like `"UNIQUE"`, `"INDEX"`, or `"pending, paid, canceled"`, or
         `"precision 30 scale 10"` for numeric precision/scale.
     VERY IMPORTANT MERMAID RULE:
       - The **type token MUST be a single word** (letters/digits/underscores only),
         with **no parentheses and no commas**.
       - **NEVER** write types like `DECIMAL(30,10)` or `NUMERIC(15, 4)` in the type position.
         This causes errors such as:
           `Syntax error in text: Expecting 'ATTRIBUTE_WORD', got ','`
       - Instead, always:
         - Use a simple type word (e.g., `DECIMAL`, `NUMERIC`), and
         - Move precision/scale or extra details into the trailing quoted annotation,
           e.g.:
           - WRONG: `DECIMAL(30,10) price`
           - CORRECT: `DECIMAL price "precision 30 scale 10"`
2. Infer relationships between tables and represent them using Mermaid ERD
   relationship syntax:
   - One-to-one: `||--||`
   - One-to-many: `||--o{`
   - Many-to-many: `}o--o{`
   - Use `|` for mandatory, `o` for optional, and `{` / `}` for “many”.
3. When determining **cardinality (0:N vs 1:N) from a foreign key**, apply this rule:
   - Consider a relationship where the child (N-side) table has an FK to the
     parent (1-side) table.
   - If the FK column on the N-side is **NOT NULL**:
     - From the N side looking at the parent: the parent is mandatory → **1 parent**.
       - Mermaid: the N side should use a mandatory-many marker, e.g. `}|`.
     - From the 1 side looking at the children: the parent can have one or more
       children → **1..N**.
       - Mermaid example:
         - `orders }|..|| users : "belongs to"`
           (each order must belong to exactly one user; each user has one or many orders).
   - If the FK column on the N-side is **NULLABLE**:
     - From the N side looking at the parent: the parent is optional → **0..1**.
       - Conceptually: a row can have no parent or exactly one parent.
     - From the 1 side looking at the children: the parent can have zero or many
       children → **0..N**.
       - Mermaid example (optional child side):
         - `orders }o..|| users : "belongs to"`
           (an order may or may not be linked to a user; a user may have zero or many orders).
4. For the relationship labels (the text after the colon), follow these conventions:
   - **1:N relationships**: use `"belongs to"` to describe the N-side pointing
     to the 1-side, for example:
     - `orders }|..|| users : "belongs to"`
   - **1:1 relationships**: use `"has one"` to describe the 1-to-1 relation, for example:
     - `user_profiles ||..|| users : "has one"`
5. Detect the absence of reliable schema information:
   - If there is no SQL / DDL definition, no clear table/column structure, and
     no trustworthy schema description in the context:
     - Treat this as “no schema available”.
     - Do **not** create any virtual or example tables.
     - You will follow the special output rule described below for this case.

# Output format and formatting requirements (very important)
1. **Normal case (schema available): output a `.mmd` ERD file content**
   - The first line of your output MUST be `erDiagram`.
   - There must be no leading `---`, titles, comments, or any non-Mermaid
     syntax before `erDiagram`.
   - You may output **exactly one** Mermaid ERD code block, consisting of:
     - `erDiagram`
     - Entity definitions (using the column line style shown above, indicating
       roles such as PK, FK, index, UQ, enum).
     - Relationship definitions:
       - Use Mermaid’s `|` / `o` / `{` / `}` markers consistently with the
         FK-nullability rule for 0:N vs 1:N.
       - Use `"belongs to"` / `"has one"` labels as described.
     - The diagram should represent **all tables and relationships** you have
       inferred, not just a subset.
   - **Do NOT output any natural language explanation or comments** in this case, for example:
     - No phrases like “Here is the ERD”, “Final result:”, etc.
     - No `%%` comment lines inside the code.
     - No description of the file path or “written to file” messages.
2. **Error case (no schema available): output a clear error message instead of ERD**
   - When you have determined that there is no reliable schema information:
     - Do **not** output `erDiagram`.
     - Output a single, concise natural-language line that clearly explains the problem.
     - The message MUST:
       - Mention that no schema was found for the schema source described in `$ARGUMENTS`.
       - Instruct the user to check or fix the source path.
     - For example (you may adapt the wording to the actual `$ARGUMENTS`):
       - `No schema (SQL/DDL) found for the schema source in: "$ARGUMENTS". Please check that the source path is correct and contains valid schema files.`
   - Do not mix this error message with any Mermaid ERD content.

# Output rules summary
- Your primary job is to generate the **entire `.mmd` file content** for the ERD
  that IS stored at the ERD output path described in `$ARGUMENTS`. The command
  takes your output and writes it to that file, so you MUST always produce
  content that is safe to persist to this file as-is, unless saving is
  impossible for reasons outside your control.
- When a reliable schema is available:
  - The **only output** must be a single Mermaid ERD code section that:
    - Can be saved as a `.mmd` file at that ERD output path,
    - Covers all tables and relationships visible in the context,
    - Encodes cardinalities (0..1, 1, 0..N, 1..N) based on FK nullability using
      `|` / `o` / `{` / `}` correctly,
    - Clearly indicates structural roles such as PK, FK, index, UQ, and enum
      where appropriate, using the column line style demonstrated above.
  - Before you finish, you MUST re-check the schema and your ERD so that **no
    known tables or relationships from the available schema are missing**. If you
    detect any omission, immediately correct your output so that all known tables
    and relationships are represented.
- When **no schema is available**:
  - You MUST NOT invent any tables or relationships.
  - You MUST output **only** the concise error message described above.

Now, following the instructions above and using the schema source and ERD
destination described in `$ARGUMENTS`, either:
- produce the exact Mermaid `erDiagram` content that IS written to the `.mmd`
  file at the ERD output path (the command takes your output and writes it to
  that file), or
- if no schema is available, output the required error message.
