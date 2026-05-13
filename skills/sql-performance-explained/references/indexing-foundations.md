# Indexing Foundations

Use this reference when deciding whether a B-tree index can improve a SQL access path.

## Core Model

- Treat an ordinary B-tree index as redundant structure: it stores indexed values in an ordered form and points back to the table rows.
- Model a B-tree index as two cooperating structures: linked leaf nodes for ordered traversal and a search tree for finding the right leaf node.
- Remember that index order is logical. Leaf nodes can be stored in arbitrary physical locations while links preserve their sorted sequence.
- Each leaf node lives in a database block or page. Entries are ordered inside a leaf node, and leaf nodes are linked to maintain order across blocks.
- The leaf-node chain is doubly linked, so the database can traverse index leaf nodes forward or backward.
- Use the root and branch nodes to reason about finding the correct leaf node quickly. Traversal compares ordered separator values and follows the corresponding child reference until it reaches a leaf node.
- Treat a B-tree as balanced, not binary: every path from root to leaf has the same depth.
- Expect tree depth to grow logarithmically. Large real-world indexes can still have shallow tree depth because databases pack many entries into each node, giving the tree high fanout.
- In the ordinary non-clustered model shown here, table rows are not inherently stored in index order and are not meaningfully ordered by their table blocks.
- Account for base-row access. The index can identify row locations, but reading the full row may still require table access.

## Slow Indexed Lookup Model

When an indexed lookup is slow, break it into three phases:

1. Tree traversal: navigate from the root through branch nodes to the first relevant leaf node.
2. Leaf-chain traversal: follow neighboring leaf nodes to collect all matching index entries.
3. Table-row fetch: use row locators from the index to retrieve base table rows.

The tree traversal has a tight upper bound from the index depth and is usually the efficient phase. The leaf-chain and table-fetch phases can touch many blocks or pages, especially when many index entries match or matching table rows are scattered.

Use this model to challenge premature index rebuild advice. A slow indexed query does not imply the tree is degenerated or unbalanced.

## Oracle Plan Vocabulary From Chapter 1

- `INDEX UNIQUE SCAN`: tree traversal only; possible when uniqueness guarantees at most one matching index entry.
- `INDEX RANGE SCAN`: tree traversal plus leaf-chain traversal; often used when multiple matching entries are possible. In Oracle, it can also appear for a primary key backed by a non-unique index even when the constraint limits the result to one row.
- `TABLE ACCESS BY INDEX ROWID`: table-row fetch using row locators produced by an index scan.

Use these terms only as Oracle vocabulary until database-specific plan references exist.

## Index Recommendation Checklist

Before recommending a new index, identify:

- The application access path the index is meant to support.
- The indexed table data that would need an ordered representation.
- Whether the useful work is finding the first relevant leaf node, traversing ordered leaf nodes, or fetching table rows.
- Whether the table rows themselves must still be fetched after using the index.
- The storage and maintenance cost of adding redundant index data.

## Practical Heuristics

- Put the access path before the index syntax. Explain which rows the database needs to find first.
- Do not describe indexes as magic accelerators. Describe the ordered lookup structure they add.
- Explain that inserts, updates, and deletes must maintain the extra index structure.
- Do not blame slow index behavior on a supposedly broken or unbalanced tree without evidence. The tree traversal itself is usually the efficient part.
- If many rows match, an index can still be slow because the database may traverse many leaf nodes and fetch many table rows.
- Keep physical table order separate from logical index order. Clustered indexes, MySQL/InnoDB clustered storage, and Oracle index-organized tables are deferred until the clustering reference exists.
- Do not use this reference yet for operator-specific rules, joins, sorting, grouping, pagination, or ORM fetch behavior.

## Review Template

```markdown
Current access path:
[What table data the application needs to locate.]

Index model fit:
[Whether an ordered redundant structure plus row locators explains the possible benefit.]

Risk:
[Storage cost, write maintenance, leaf-chain traversal, or continued table-row fetch cost.]

Out of scope:
[Any requested analysis that needs a later section reference.]
```
