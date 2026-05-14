# Where Clause

Use this reference when the task concerns a `WHERE` clause search condition. Current scope covers the chapter opening, primary-key equality lookup, concatenated indexes, full table scan caveat, Slow Indexes, Part II, and Functions through Over-Indexing, stopping before Parameterized Queries.

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

## Slow Indexes, Part II

Changing an index can affect all queries on the indexed table:

- An index that starts with a searched column can become usable for queries that previously used another access path.
- When multiple access paths are available, the optimizer chooses between them.
- A cost-based optimizer generates plan variations and compares cost estimates based on operations and estimated row counts.
- A rule-based optimizer uses hard-coded rules; the book notes that rule-based optimizers are less flexible and seldom used today.

For the `LAST_NAME` and `SUBSIDIARY_ID` example:

- A revised primary-key index that starts with `SUBSIDIARY_ID` can be used for the subsidiary predicate.
- The `INDEX RANGE SCAN` can apply only the `SUBSIDIARY_ID` access predicate when `LAST_NAME` is not part of that index.
- The range scan traverses the tree to the first matching subsidiary entry, then follows the leaf chain for the other entries in that subsidiary.
- `TABLE ACCESS BY INDEX ROWID` fetches the rows from the table so the database can evaluate the remaining `LAST_NAME` filter.
- Response time depends on the number of employees in the subsidiary, not only on the final result count.

A slow index plan can combine the two slow-index ingredients from Chapter 1:

- A wide index range.
- Many individual table-row fetches.

Use statistics to explain optimizer choices:

- Cost-based optimizers use table, column, and index statistics to estimate predicate selectivity.
- Table size in rows and blocks is the most important table statistic.
- Column statistics include number of distinct values, smallest and largest values, null count, and histograms.
- Index statistics include tree depth, leaf-node count, number of distinct keys, and clustering factor. Treat clustering factor as name-only until the Chapter 5 clustering reference exists.
- Missing statistics can make the optimizer fall back to defaults and underestimate an index range.
- Correct statistics can make the optimizer prefer a full table scan when the indexed plan is estimated to cost more.

Prefer an index that matches the actual search:

- A query that searches by `LAST_NAME` is best supported by an index on `LAST_NAME`.
- With that dedicated index, the range scan can apply the `LAST_NAME` access predicate and fetch far fewer table rows.
- The same operation names and similar plan shape can perform very differently depending on how selective the index access is.
- Using an index does not automatically mean the statement is executed in the best possible way.

## Functions

Use this section when a `WHERE` predicate applies a function or expression to a column.

- MySQL 5.6 does not support the function-based indexing described in this section; the book mentions MariaDB virtual columns as the alternative introduced at MariaDB 5.2.

### Case-Insensitive Search Using `UPPER` Or `LOWER`

- A predicate such as `UPPER(last_name) = UPPER('winand')` searches for `UPPER(last_name)`, not for `LAST_NAME`.
- A normal index on `LAST_NAME` cannot support that search term because the indexed value and the searched expression are different.
- From the optimizer's view, an arbitrary function behaves like a black box: the relationship between input column and function result is not generally available.
- The optimizer can evaluate constant-side expressions at compile time when all inputs are already known; in the example, the search literal can be folded to uppercase.
- To support the predicate, index the searched expression, for example `UPPER(last_name)`.
- A function-based index stores the function result in the index instead of copying the base column value directly.
- The database can use the function-based index when the same expression appears in the SQL statement.
- Function-based index usage still appears as a regular index range scan; there is no special plan operation name for it in the processed Oracle example.
- Hibernate can inject an implicit `LOWER` for case-insensitive searches, so inspect generated SQL before assuming the query uses the plain column.
- Case-insensitive matching can also depend on collation; the book notes that SQL Server and MySQL default collations are case-insensitive by default.
- SQL Server does not support function-based indexes in the same form; use an indexed computed column for the expression.
- SQL Server can use the computed-column index when the indexed expression appears in the statement without requiring the query to reference the computed column directly.

### Statistics For Function-Based Indexes

- Check row-count estimates after adding a function-based index.
- Contradictory estimates, such as table access expecting more rows than the preceding index scan returns, indicate a statistics problem in the processed example.
- Updated statistics can improve optimizer estimates without changing runtime when the correct access path was already used.
- In the Oracle example, creating the function-based index does not update table statistics, even though index statistics are collected automatically in newer releases.
- Oracle keeps distinct column-value statistics at table level and reuses them when a column appears in multiple indexes.
- Oracle stores statistics for function-based indexes as virtual-column table statistics.
- After creating a function-based index in Oracle, collect statistics for both the index and the base table.
- The book recommends coordinating broader statistics refreshes with DBAs and keeping the original statistics backed up because refreshed statistics can have side effects.
- Oracle extended statistics on expressions and column groups start with Oracle 11g; treat that as a version note until more sections are processed.

### User-Defined Functions

- Function-based indexing can use built-in functions, expressions such as `A + B`, and user-defined functions.
- Only deterministic functions are suitable for index definitions: the same parameters must always produce the same result.
- A function that depends on the current time cannot be indexed correctly because stored index entries are not periodically recalculated.
- The database updates a stored function result in the index when the indexed input column changes, not when time passes or external state changes.
- Random number generators and functions depending on environment variables are also unsuitable for indexing.
- Oracle requires deterministic functions used in indexes to be declared with `DETERMINISTIC`.
- PostgreSQL requires indexable functions to be declared `IMMUTABLE`.
- Treat those declarations as promises from the developer; declaring a non-deterministic function as deterministic does not make the index logically correct.

### Over-Indexing

- Do not index every function or expression just because function-based indexing is available.
- Every index adds ongoing maintenance for inserts, updates, and deletes.
- Function-based indexes can easily become redundant when different expressions express the same logical search.
- A single function-based index on `UPPER(last_name)` does not also support `LOWER(last_name)`.
- Standardize the application access path so several queries can use one expression and one index.
- Prefer indexing original data when it supports the access path, because original values are often the most useful indexed information.

## Still Out of Scope

- Non-primary-key equality predicates outside the concatenated-index examples above.
- Parameterized queries, bind parameters, and their effect on function expressions.
- Operator-specific rules for ranges, pattern matching, null checks, and expressions beyond the function-based indexing material above.
- General execution-plan reading beyond the operation names and cost examples above.
- ORM-specific translation from JPQL, Criteria, Spring Data methods, or Hibernate mappings beyond the Hibernate `LOWER` warning above.
