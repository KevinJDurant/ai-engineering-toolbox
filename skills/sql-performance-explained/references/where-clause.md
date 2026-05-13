# Where Clause

Use this reference when the task concerns a `WHERE` clause search condition. Current scope covers the chapter opening, primary-key equality lookup, and concatenated indexes.

## Search Conditions

- Treat the `WHERE` clause as the part of SQL that defines what data must be found.
- Start index reasoning from the search condition, not from the table size alone.
- A search condition that does not match the useful index order can prevent the database from using that index to locate the requested rows. Detailed operator rules are deferred until their sections are processed.

## Equality Operator: Primary-Key Lookup

For a predicate that compares a primary-key column to one value:

- Remember that the database creates an index to support the primary key even when there is no explicit `CREATE INDEX` statement.
- Use the primary-key constraint as the key fact: uniqueness means the predicate can match at most one row.
- Because at most one index entry can match, the database does not need to follow the leaf-node chain to collect multiple entries.
- In Oracle vocabulary, this favorable case appears as `INDEX UNIQUE SCAN` when the supporting index is unique.
- In the processed primary-key example, the query selects non-key table columns, so the database fetches the table row after the index lookup. In Oracle vocabulary, this appears as `TABLE ACCESS BY INDEX ROWID`.
- The slow-index ingredients from Chapter 1 are mostly absent in this case: no large leaf-chain traversal and at most one table-row fetch.

## Primary Key With Non-Unique Index

Do not assume every primary key is backed by a unique index in Oracle:

- A primary-key constraint can be supported by a non-unique index, for example with deferrable constraints.
- The constraint still enforces uniqueness, so the lookup can still return at most one row.
- The Oracle operation may appear as `INDEX RANGE SCAN` rather than `INDEX UNIQUE SCAN` because the index itself is non-unique.

## Concatenated Indexes

Treat a concatenated, composite, combined, or multi-column index as one B-tree index across multiple columns, not as separate indexes on each column.

Column order is the main design choice:

- The first index column is the primary sort criterion.
- The second column orders entries only within equal first-column values, and so on.
- A query using the full unique key can still use an `INDEX UNIQUE SCAN`, no matter how many columns the key has.
- A query using only the second column of a two-column index generally cannot use the B-tree to find a central range because matching entries are scattered across the first-column order.
- A query can generally use a concatenated index when it searches with the leading leftmost column or a leading prefix of columns.

Use the telephone-directory mental model: an index on `(last_name, first_name)` supports searching by last name, or by last name plus first name, but not efficiently by first name alone.

## Choosing Column Order

Choose concatenated index order from application access paths:

- List the column combinations that actually appear in `WHERE` clauses.
- Prefer an order that supports the most important combinations with one index.
- If a query searches by column B alone, an index on `(A, B)` is usually not enough; consider `(B, A)` if full-key lookups still work and searching by A alone is not important.
- Avoid adding a second index when reordering an existing concatenated index supports the needed access paths. Fewer indexes usually mean less storage and better insert, update, and delete performance.
- Developers are often best positioned to make this tradeoff because they know the application access paths.

To reason visually, inspect the indexed columns in index order and ask whether the requested rows are clustered together:

```sql
SELECT <index column list>
  FROM <table>
 ORDER BY <index column list>
 FETCH FIRST 100 ROWS ONLY;
```

If the requested rows are not clustered together in that order, the B-tree cannot directly locate them as one useful range.

## Full Table Scan Caveat

Do not treat `TABLE ACCESS FULL` as automatically wrong:

- A full table scan reads the whole table and checks each row against the predicate.
- It can look harmless in a small development database but scale poorly as table size grows.
- It can still be the most efficient choice when a query needs a large part of the table, because the database can read larger chunks sequentially instead of doing many single-block index lookups.

## Still Out of Scope

- Optimizer side effects from changing indexes.
- Non-primary-key equality predicates outside the concatenated-index examples above.
- Operator-specific rules for ranges, pattern matching, functions, null checks, and expressions.
- General execution-plan reading beyond the operation names above.
- ORM-specific translation from JPQL, Criteria, Spring Data methods, or Hibernate mappings.
