- Why normalize?
	- UPDATE/DELETE anomolies
- Functional Dependency: X -> Y if and only if each value of X is associated with exactly one value of Y
	- "uniquely determines, defines, maps to, identifys"
### Functional Dependencies
A -> A, AB -> A (reflexive/trivial)
A -> B, then AY -> BY (augmentation)
A -> B, B -> C (transitivity)
A -> B and X -> Y, then AX -> BY (composition)
A -> BY, then A -> B and A -> Y (decomposition)
A -> B, BC -> Y, then BA -> Y (pseudotransitivty)
### Reducing (canonical cover)
- Include trivial CK relations!
- Decompose the right hand side: A->BC becomes A->B and A->C
- Remove extraneous attributes, e.g. AB -> C but A -> C already exists, so eliminate
- Remove inferred/redundant FDs:
	- transitivity
- Cleanup using union rule
### Normal Forms
#### 1NF
- Attributes are atomic, indivisible
- No repeated groups (e.g. no arrays/lists, or unordered repeated columns)
- **Unique key (PK exists)**
- No null values
#### 2NF
- in 1NF and no composite keys
- Every attribute is in candidate key or depends on the **entirety** of **all** CKs
#### 3NF
- 2NF and attributes must ONLY directly depend on all candidates keys and NOTHING ELSE (except those in candidate key)
	- No transitivity
- Other def: all functional dependencies are trivial, start in full superkey, or end in part of candidate key
#### BCNF
- For all FD a->b,
- 
### Lossless joins
- Check R1 ⨝ R2 = R OR
- R1 U R2 = R
- R1 ∩ R2 not empty set (e.g. should have join key)
- R1 ∩ R2: closure of it (follow func. dep) is a superkey for R1 or R2