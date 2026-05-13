---
name: sql-performance-explained
description: Explain SQL B-tree indexing foundations, primary-key equality lookups, and concatenated index column order for application queries. Use when reviewing index anatomy, why an index is not a generic speed switch, how indexed data relates to table rows, why an indexed lookup can still be slow, how a primary-key WHERE predicate uses an index, or whether a multi-column index supports a WHERE clause. Current source-backed scope is Chapter 1 plus Chapter 2 through Concatenated Indexes.
---

# SQL Performance Explained

Explain persistence performance by reducing it to the database access path. In the current draft, keep recommendations limited to the source-backed indexing, primary-key equality, and concatenated-index material captured in the references.

This skill is informed by SQL performance concepts from Markus Winand's *SQL Performance Explained*. Do not quote or reproduce source text; use the distilled procedures and references here.

## Current Workflow

1. Identify the application access path: which data the query needs and how the application asks for it.
2. Explain whether the request is about B-tree structure, indexed lookup phases, or table-row access.
3. Separate tree traversal, leaf-chain traversal, and table-row access: each explains a different part of index behavior.
4. Explain index tradeoffs as redundancy: extra storage plus automatic insert, update, and delete maintenance in exchange for a faster access path.
5. For primary-key equality predicates, explain why uniqueness limits the index lookup to at most one matching entry.
6. For concatenated indexes, check whether the query uses the leading index columns and whether the requested rows are consecutive in index order.
7. Avoid recommendations that depend on unprocessed topics such as optimizer choice side effects, joins, non-primary-key `WHERE` operator details beyond concatenated equality examples, sorting, pagination, broad execution-plan analysis, or ORM fetch behavior until their references exist. The Oracle index operation names in the references are in scope.

## Reference Loading

- Read [references/indexing-foundations.md](references/indexing-foundations.md) before explaining B-tree index behavior, index maintenance overhead, or why an indexed lookup can still be slow.
- Read [references/where-clause.md](references/where-clause.md) before explaining a `WHERE` clause, equality predicate, primary-key lookup, concatenated index, leading-column usability, or the difference between primary-key `INDEX UNIQUE SCAN` and table access.

## Gotchas

- An index is not a generic speed setting. It is redundant ordered data with write and storage cost.
- Index order is logical, not necessarily physical. Leaf nodes can be linked in order even when stored in arbitrary places.
- A B-tree index combines linked leaf nodes for ordered traversal with a search tree for locating the right leaf node.
- A B-tree is balanced, not binary. Its depth is usually shallow even for large indexes.
- Slow indexed lookups are usually not explained by a broken tree; the expensive parts are often leaf-chain traversal and table-row fetches.
- A fast index lookup can still require table access because ordinary index entries point to base rows.
- A primary-key equality lookup is the simplest favorable case: uniqueness prevents multiple matching index entries, but selected table columns can still require one table-row fetch.
- A concatenated index is one index across multiple columns; column order determines which `WHERE` clause combinations it can support.

## Output Shape

For current-scope reviews, answer in this order:

1. What the database is doing according to the execution plan, or what it is likely doing if no plan is available.
2. Which index lookup phase dominates: tree traversal, leaf-chain traversal, or table-row access.
3. What an index can and cannot improve.
4. What material is still outside the current source-backed scope.
