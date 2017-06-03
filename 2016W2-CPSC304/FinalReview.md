## ER Diagram

Important: weak entities, aggregation, inheritance (ISA).

- If there's an inconsistency in ER diagram and description, point it out
- ISA disjoint: can just write "d"
- Weak entity: bold, total participation
- Key constraint: 1-many, 1-1, many-1
- Aggregation: when something exists on a relationship
- Primary key: underlined
- Foreign key: circled

## Relational Mode

### SQL DDL

- Merge relations and entities properly
- Delete/Update on cascade, set default, etc
- Primary keys, foreign keys, constraints
- Identity errors given tables/diagrams
- Weak entities: make sure that system should perform **delete cascading** on this table
- 1-many: ensure that primary key of M cardinality is associated with a key of 1 cardinality that is **not null**

### Schema Refinement and Normal Forms

- BCNF, 3NF
- Decomposition, minimal cover
- Normalization, lossless join
- Finding minimal cover:
  1. Standardize FDs —> one attribute on RHS
  2. Eliminate FDs —> remove unnecessary attributes on LHS
  3. Remove redundant FDs
- Decomposition into 3NF:
  - Using minimal cover
  - 3NF Synthesis

## Formal Relational Languages

RA, Datalog, SQL

- Division is important
- `WHERE` and `HAVING` (used only when `GROUP BY` is present)



## Useful Links

- [Youtube video (18:07): Introduction to OLAP, OTLP, Data Warehousing, Star Schema, Fact Tables, Dimension Tables, OLAP queries, Data Cubes, Drill-down, and Roll-up](https://www.youtube.com/watch?v=1Qdf5c_nmtw)
- [MOLAP, ROLAP, HOLAP](https://en.wikipedia.org/wiki/Online_analytical_processing#Types)
- [Oracle docs: Intro to data warehousing, OLAP, and OTLP](https://docs.oracle.com/cd/B10500_01/server.920/a96520/concept.htm)
- [Oracle docs: Fact tables, dimension tables (look at section Data Warehousing Objects)](https://docs.oracle.com/cd/B10500_01/server.920/a96520/logical.htm)
- [Youtube video (14:31): Apriori Algorithm (Data Mining)](https://www.youtube.com/watch?v=Hk1zFOMLTrw)