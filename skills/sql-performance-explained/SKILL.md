---
name: sql-performance-explained
description: Explain SQL B-tree indexing foundations, primary-key equality lookups, concatenated index column order, slow-index behavior from wide range scans and table-row fetches, optimizer/statistics effects on plan selection, and function-based indexing for WHERE clauses. Use when reviewing index anatomy, why an index is not a generic speed switch, how indexed data relates to table rows, why an indexed lookup can still be slow, how a primary-key WHERE predicate uses an index, whether a multi-column index supports a WHERE clause, how changing an index can affect plans for other queries, or why applying a function such as UPPER or LOWER can prevent use of a normal column index. Current source-backed scope is Chapter 1 plus Chapter 2 through Over-Indexing, stopping before Parameterized Queries.
---

# SQL Performance Explained

Explain persistence performance by reducing it to the database access path. In the current draft, keep recommendations limited to the source-backed indexing and `WHERE` clause material captured in the references.

This skill is informed by SQL performance concepts from Markus Winand's *SQL Performance Explained*. Do not quote or reproduce source text; use the distilled procedures and references here.

## Current Workflow

1. Identify the application access path: which data the query needs and how the application asks for it.
2. Explain whether the request is about B-tree structure, indexed lookup phases, or table-row access.
3. Separate tree traversal, leaf-chain traversal, and table-row access: each explains a different part of index behavior.
4. Explain index tradeoffs as redundancy: extra storage plus automatic insert, update, and delete maintenance in exchange for a faster access path.
5. For primary-key equality predicates, explain why uniqueness limits the index lookup to at most one matching entry.
6. For concatenated indexes, check whether the query uses the leading index columns and whether the requested rows are consecutive in index order.
7. For slow-index cases, explain how a wide index range plus row-by-row table access can make an index plan slower than a full scan, and how optimizer estimates and statistics explain plan selection.
8. For function predicates, compare the expression in the `WHERE` clause to the expression stored in the index; ordinary column indexes and function-based indexes support different search terms.
9. Avoid recommendations that depend on unprocessed topics such as joins, non-primary-key `WHERE` operator details beyond the processed examples, parameterized queries, range search, sorting, pagination, broad execution-plan analysis beyond the current Oracle examples, or ORM fetch behavior beyond the Hibernate `LOWER` warning until their references exist. The Oracle index operation names in the references are in scope.

## Reference Loading

- Read [references/indexing-foundations.md](references/indexing-foundations.md) before explaining B-tree index behavior, index maintenance overhead, or why an indexed lookup can still be slow.
- Read [references/where-clause.md](references/where-clause.md) before explaining a `WHERE` clause, equality predicate, primary-key lookup, concatenated index, leading-column usability, optimizer access-path choice, statistics, function-based index, function-based index statistics, deterministic user-defined functions, over-indexing, case-insensitive search using `UPPER` or `LOWER`, or the difference between primary-key `INDEX UNIQUE SCAN` and table access.
- For Slow Indexes, Part II cases, read both [references/indexing-foundations.md](references/indexing-foundations.md) and [references/where-clause.md](references/where-clause.md), because the chapter combines the Chapter 1 slow-index model with Chapter 2 optimizer and statistics examples.

## Gotchas

- An index is not a generic speed setting. It is redundant ordered data with write and storage cost.
- Index order is logical, not necessarily physical. Leaf nodes can be linked in order even when stored in arbitrary places.
- A B-tree index combines linked leaf nodes for ordered traversal with a search tree for locating the right leaf node.
- A B-tree is balanced, not binary. Its depth is usually shallow even for large indexes.
- Slow indexed lookups are usually not explained by a broken tree; the expensive parts are often leaf-chain traversal and table-row fetches.
- A fast index lookup can still require table access because ordinary index entries point to base rows.
- A primary-key equality lookup is the simplest favorable case: uniqueness prevents multiple matching index entries, but selected table columns can still require one table-row fetch.
- A concatenated index is one index across multiple columns; column order determines which `WHERE` clause combinations it can support.
- Changing an index can affect all queries on the table, so a better index for one predicate can become a worse access path for another.
- A slow index plan can be chosen when estimates understate the index range; corrected statistics can make the optimizer prefer `TABLE ACCESS FULL` when it is estimated cheaper.
- When an index range scan is slow, inspect the filtered column, the leaf-range width, and the number of table rows fetched through row locators.
- A normal index on a column does not support a search on a function of that column; an index must match the searched expression.
- Function-based indexes require deterministic expressions; values that change without table updates cannot be safely stored in the index.
- Consistently use the same expression for the same access path to avoid redundant function-based indexes.

## Output Shape

For current-scope reviews, answer in this order:

1. What the database is doing according to the execution plan, or what it is likely doing if no plan is available.
2. Which index lookup phase dominates: tree traversal, leaf-chain traversal, table-row access, or optimizer choice between access paths.
3. What an index can and cannot improve, including when a different index helps one query but hurts another.
4. Whether the searched expression matches the index definition.
5. Whether statistics or row-count estimates explain the chosen plan.
6. What material is still outside the current source-backed scope.
