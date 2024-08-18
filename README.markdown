CDCL SAT solver with added permanent learnt clauses
===========================================


These solvers are built based on the winner of the silver
medal in the main track of the [2020 SAT Solver Competition]([https://helda.helsinki.fi/items/3bd6f832-e0cf-4db9-bf9b-89764dea3a72](https://satcompetition.github.io/2020/)). A member of the MapleSAT family of
solvers that has been improved over the last few years.
We modify the clause maintenance strategy in different ways to learn and store more valuable clauses.
You can read about it in more detail [here] ([https://www.cs.sfu.ca/~mitchell/papers/sat19-deletion.pdf](https://arxiv.org/abs/2110.14187)). 

High-Centrality Permanent Clauses
------------------------

The centrality of a clause is the mean betweenness centrality
of its variables. To compute centralities, we generate the
primal graph of the input CNF (after pre-processing). The
betweenness centrality of a vertex (variable) v is the number
of shortest paths between pairs of vertices excluding v, that
visit v. We use Brandes algorithm to compute centrality values.
High centrality (HC) learned clauses (those with centrality greater than a threshold CT), are stored in
permanent clause storage, regardless of their LBD. The solver, `Maple_MBDR_Cent_PERM_10K` stores at
most the first 10,000 HC clauses permanently.

BackJump Learning Scheme
------------------------

Standard conflict analysis schemes derive one clause, called
the 1-UIP clause, at each conflict. To `Maple_MBDR_BJL`, we
add a simple and inexpensive scheme to learn additional small
clauses. Assume a conflict at level x, meaning after assigning x literals l1; l2; ::; lx to
true, a conflict is reached. After conflict analysis the solver
backjumps to a level b and learns a 1UIP clause C. Only one literal from C belongs
to level x, and b < x, so after the first b decisions, if we had
C in the clause database, unit propagation would prevent this
conflict by assigning that literal true. For small values of b, this
new learned clauses is small.


Compiling and installing
------------------------

The code is written in c++. You can use the Makefile to compile and install. 
After the code is compiled, the binary is available under `/simp/M_Permanent`.



Solver's input file and results
-------------------------------
The command-line interface takes a [cnf](http://en.wikipedia.org/wiki/Conjunctive_normal_form) as an
input in the [DIMACS](http://www.satcompetition.org/2009/format-benchmarks2009.html)
format which is broadly used in SAT competitions.


Let's take the file:

```
p cnf 3 2
-1 2 0
1 3 0
```

The cnf files has 3 variables and 2 clauses, as shown in the header `p cnf 3 2`. 
In DIMACS format all clauses end by '0'. In this example the first clause says: either variable 1 has to be False or 2 has to be True.
The goal is to assign boolean values to the variables in a way that all clauses are satisfied (made TRUE). A solution to this problem is to assign all variables to TRUE. 
Given that there is a truth assignment that satisfies all clauses, when we run the solver on this SAT instance the solver returns:

```
$ m_OnlineDel file.cnf
s SATISFIABLE
```

If we had the following cnf file instead:

```
p cnf 1 2
1 0
-1 0
```

Then there is no solution to satisfy all clauses and the solver returns:

```
s UNSATISFIABLE
```

...
