# Graph-State Encrypted Cloning Certification (GSECC)
## and Graph-State Decoder Construction (GSDC)

This repository provides a reference implementation of two complementary algorithms developed for the analysis of **graph-state encrypted cloning resources**.

- **Graph-State Encrypted Cloning Certification (GSECC)** is an **exact certification algorithm** that determines whether a graph state can serve as a valid encrypted cloning resource.

- **Graph-State Decoder Construction (GSDC)** is a constructive algorithm that explicitly recovers the decoder unitary associated with every certified graph state.

The implementation accompanies the theoretical results presented in our work and is intended as both a reproducible research tool and a reference implementation for future studies on graph-state encrypted cloning.

---

# Overview

The repository provides implementations of

- Exact certification of graph-state encrypted cloning resources.
- Gaussian elimination over the binary field GF(2).
- Exhaustive search over balanced graph bipartitions.
- Automatic verification of maximal mixedness.
- Explicit decoder construction.
- Publication-quality graph visualization.
- Benchmark examples for several important graph families.

Unlike heuristic search methods, the certification algorithm implemented here is **exact**. Every positive certification and every negative result produced by the algorithm is mathematically rigorous.

---

# Mathematical Notation

Throughout this repository,

- **$m$** denotes the number of clones.
- **$k$** denotes the number of input (signal) qubits.
- **$mk = m \times k$** denotes the size of each subsystem.
- **$2mk$** denotes the total number of graph vertices.

Every graph processed by the algorithm must therefore satisfy

$$
|V| = 2mk.
$$

A balanced bipartition of the graph is written as

$$
V = S \cup N,
$$

with

$$
|S| = |N| = mk.
$$

Throughout the documentation,

- **$S$** denotes the signal subsystem.
- **$N$** denotes the noise subsystem.

---

# Problem 1: Graph-State Encrypted Cloning Certification (GSECC)

## Objective

Given a graph

$$
G=(V,E),
$$

the objective is to determine whether there exists a balanced partition

$$
V=S\cup N,
\qquad
|S|=|N|=mk,
$$

such that the associated cut (biadjacency) matrix satisfies

$$
\operatorname{rank}_{GF(2)}(\Gamma_{S,N}) = mk.
$$

If such a partition exists, the corresponding graph state is certified as a valid encrypted cloning resource.

Equivalently, certification guarantees that the reduced density operator of the signal subsystem is maximally mixed,

$$
\rho_S
=
\frac{I}{2^{mk}}.
$$

---

# Certification Algorithm

The GSECC algorithm is deterministic and performs an exhaustive search over all balanced graph bipartitions.

Given the adjacency matrix of a graph, the algorithm proceeds as follows.

### Step 1 — Validate the graph size

Verify that the graph contains exactly

$$
2mk
$$

vertices.

If this condition is violated, execution terminates immediately with a `ValueError`.

---

### Step 2 — Enumerate balanced partitions

Generate every balanced bipartition

$$
(S,N)
$$

satisfying

$$
|S|=|N|=mk.
$$

To eliminate the symmetry

$$
S \leftrightarrow N,
$$

one vertex is fixed during the enumeration.

---

### Step 3 — Construct the cut matrix

For each candidate partition, construct the cut (biadjacency) matrix

$$
\Gamma_{S,N}.
$$

---

### Step 4 — Compute the GF(2) rank

Compute

$$
\operatorname{rank}_{GF(2)}
\left(
\Gamma_{S,N}
\right)
$$

using Gaussian elimination over the binary field GF(2).

---

### Step 5 — Certification

If

$$
\operatorname{rank}_{GF(2)}
\left(
\Gamma_{S,N}
\right)
=
mk,
$$

the partition

$$
(S,N)
$$

is returned as a valid certificate.

---

### Step 6 — Exhaustive rejection

If every balanced partition has been examined and none satisfies the certification criterion, the algorithm returns

```text
None
```

which constitutes a rigorous proof that no valid balanced partition exists.

Unlike randomized or heuristic search methods, this is **not** merely a failure to find a solution.

---

# Exactness of the Algorithm

The implementation performs an exhaustive search over the complete space of balanced bipartitions.

Consequently,

- every returned certificate is mathematically correct;
- every rejected graph has been exhaustively verified;
- every negative result is a proof of non-existence.

The algorithm therefore provides **exact certification**, not heuristic evidence.

---

# Computational Complexity

## Certificate Verification

For a fixed candidate partition, certification requires only the computation of the GF(2) rank of the cut matrix.

Using Gaussian elimination, this requires

$$
O((mk)^3)
$$

operations.

Thus, verification of a proposed certificate is polynomial in the subsystem size.

---

## Exhaustive Search

To discover a valid certificate, the algorithm examines every balanced partition,

$$
\binom{2mk-1}{mk-1},
$$

where one vertex is fixed to remove the symmetry between

$$
S
\quad\text{and}\quad
N.
$$

The overall complexity is therefore

$$
O\!\left(
\binom{2mk-1}{mk-1}
(mk)^3
\right).
$$

The exponential complexity arises solely from the exhaustive search over balanced bipartitions, while the verification of an individual certificate remains polynomial.

---

## Practical Scalability

The number of balanced partitions grows rapidly with subsystem size.

| $mk$ | Balanced partitions |
|------:|--------------------:|
| 4 | 35 |
| 6 | 462 |
| 8 | 6,435 |
| 10 | 92,378 |
| 20 | $\approx 6.9\times10^{10}$ |

This exponential growth is an inherent property of exhaustive certification rather than a limitation of the implementation itself.

---

# Exact Certification Only

This repository intentionally implements **only the exact certification algorithm**.

Earlier experimental versions also included randomized search routines for exploratory studies on very large graphs. These routines have been omitted because they cannot certify non-existence.

A heuristic search can only report

> No certificate found within the search budget.

whereas the exhaustive algorithm implemented here can rigorously conclude

> No valid balanced partition exists.

For this reason, only the exact certification algorithm is included in the released implementation.





