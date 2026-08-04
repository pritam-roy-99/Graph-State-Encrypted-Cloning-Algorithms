# Graph-State Encrypted Cloning Certification (GSECC)
## and Graph-State Decoder Construction (GSDC)

This repository provides a reference implementation of two complementary algorithms introduced in our work on **graph-state encrypted cloning**.

- **Graph-State Encrypted Cloning Certification (GSECC)** is an **exact certification algorithm** that determines whether a graph state can serve as a valid encrypted cloning resource.

- **Graph-State Decoder Construction (GSDC)** is a constructive algorithm that explicitly builds the decoder unitary associated with every certified graph state.

The implementation is intended both as a research tool and as a reproducible reference for the theoretical results presented in the accompanying paper.

---

# Overview

The repository provides

- Exact certification of graph-state encrypted cloning resources.
- Exact GF(2) Gaussian elimination.
- Exhaustive search over balanced graph bipartitions.
- Automatic verification of maximal mixedness.
- Explicit decoder construction.
- Publication-quality graph visualization.
- Demonstration examples for several important graph families.

Unlike heuristic approaches, the certification algorithm implemented here is **exact**. Every positive certification and every negative result returned by the algorithm is mathematically rigorous.

---

# Mathematical Notation

Throughout this repository

- **m** denotes the number of clones.
- **k** denotes the number of input (signal) qubits.
- **mk = m \times k** denotes the size of each subsystem.
- **2mk** is the total number of graph vertices.

Every graph processed by the algorithm must therefore satisfy

$$
|V|=2mk.
$$

A balanced graph partition is written as

$$
V=S\cup N,
$$

with

$$
|S|=|N|=mk.
$$

Here

- **S** represents the signal subsystem.
- **N** represents the noise subsystem.

---

# Problem 1: Graph-State Encrypted Cloning Certification (GSECC)

## Objective

Given a graph

$$
G=(V,E),
$$

determine whether there exists a balanced partition

$$
V=S\cup N,
\qquad
|S|=|N|=mk,
$$

such that the corresponding cut (biadjacency) matrix satisfies

$$
\operatorname{rank}_{GF(2)}
(\Gamma_{S,N})
=
mk.
$$

If such a partition exists, the graph state is certified as a valid encrypted cloning resource.

Equivalently, the reduced density operator on the signal subsystem satisfies

$$
\rho_S
=
\frac{I}{2^{mk}},
$$

meaning that the signal subsystem is maximally mixed.

---

# Certification Algorithm

The GSECC algorithm is completely deterministic.

Given an adjacency matrix, the algorithm performs the following steps.

### Step 1

Verify that the graph contains exactly

$$
2mk
$$

vertices.

If not, execution immediately terminates with a `ValueError`.

---

### Step 2

Enumerate every balanced bipartition

$$
(S,N)
$$

of the graph.

To remove the symmetry between

$$
S
\leftrightarrow
N,
$$

one vertex is fixed during enumeration.

---

### Step 3

Construct the cut matrix

$$
\Gamma_{S,N}.
$$

---

### Step 4

Compute

$$
\operatorname{rank}_{GF(2)}
(\Gamma_{S,N})
$$

using Gaussian elimination over the binary field.

---

### Step 5

If

$$
\operatorname{rank}_{GF(2)}
(\Gamma_{S,N})
=
mk,
$$

the partition

$$
(S,N)
$$

is returned as a valid certificate.

---

### Step 6

If every balanced partition has been examined without finding a certificate, the algorithm returns

```text
None
```

which constitutes a mathematical proof that no valid balanced partition exists.

Unlike randomized search methods, this is **not** simply a failure to find a solution.

---

# Exactness of the Algorithm

The implementation performs an exhaustive search over the complete space of balanced partitions.

Consequently,

- every returned certificate is mathematically correct,
- every rejected graph has been exhaustively verified,
- every negative result is a proof of non-existence.

The implementation therefore produces **exact certification**, not heuristic evidence.

---

# Computational Complexity

## Certificate Verification

For a fixed candidate partition, certification requires only the computation of the GF(2) rank of the cut matrix.

Using Gaussian elimination, this requires

$$
O((mk)^3)
$$

operations.

Thus, once a candidate partition is supplied, verification is polynomial time.

---

## Exhaustive Search

To discover a certificate, the algorithm examines every balanced partition

$$
\binom{2mk-1}{mk-1},
$$

where one vertex is fixed to eliminate the symmetry

$$
S
\leftrightarrow
N.
$$

The overall complexity is therefore

$$
O\!\left(
\binom{2mk-1}{mk-1}
(mk)^3
\right).
$$

The exponential complexity arises solely from the exhaustive search over balanced bipartitions.

The verification of any individual certificate remains polynomial.

---

## Practical Scalability

The exhaustive search grows rapidly with the subsystem size.

For example

| \(mk\) | Balanced partitions |
|-------:|--------------------:|
| 4 | 35 |
| 6 | 462 |
| 8 | 6,435 |
| 10 | 92,378 |
| 20 | approximately \(6.9\times10^{10}\) |

This exponential growth is an inherent property of exhaustive certification rather than a limitation of the implementation.

---

# Exact Certification Only

This repository intentionally implements **only the exact certification algorithm**.

Earlier experimental versions also contained randomized search routines intended for exploratory studies on very large graphs.

These routines have been removed because they cannot certify non-existence.

A heuristic search can only report

> No certificate found within the search budget.

whereas the exhaustive algorithm implemented here can rigorously conclude

> No valid balanced partition exists.

For this reason, only the exact certification algorithm is included in the released implementation.


---

# Problem 2: Graph-State Decoder Construction (GSDC)

Once GSECC certifies a balanced partition

$$
(S,N),
$$

the corresponding decoder can be constructed explicitly.

Rather than merely proving the existence of a valid encrypted cloning resource, GSDC provides a constructive procedure for recovering the decoder unitary associated with the certified graph state.

---

## Objective

Given a certified graph state

$$
|G\rangle,
$$

construct a unitary

$$
W:\mathcal{H}_N\rightarrow\mathcal{H}_N
$$

such that

$$
|G\rangle
=
(I_S\otimes W)
|\Phi_{2^{mk}}\rangle,
$$

where

$$
|\Phi_{2^{mk}}\rangle
=
\frac{1}{\sqrt{2^{mk}}}
\sum_i
|i\rangle_S|i\rangle_N
$$

is the canonical maximally entangled state.

The decoder construction demonstrates that every certified graph state is locally equivalent to a maximally entangled state.

---

# Decoder Construction Algorithm

Suppose

$$
(S,N)
$$

is a certificate returned by GSECC.

The decoder is constructed as follows.

### Step 1

Reorder the qubits so that all signal qubits precede the noise qubits.

---

### Step 2

Rewrite the graph state as

$$
|G\rangle
=
\sum_{i,j}
M_{ij}
|i\rangle_S
|j\rangle_N,
$$

where

$$
M
$$

is a

$$
2^{mk}\times2^{mk}
$$

coefficient matrix.

---

### Step 3

Since GSECC guarantees

$$
\rho_S
=
\frac{I}{2^{mk}},
$$

the normalized matrix

$$
Q
=
\sqrt{2^{mk}}
\,M
$$

is unitary.

---

### Step 4

The decoder is obtained simply as

$$
W
=
Q^T.
$$

The implementation additionally verifies

- unitarity,
- reconstruction accuracy,
- numerical residuals.

---

# Computational Cost

The decoder construction operates on a Hilbert space of dimension

$$
d
=
2^{mk}.
$$

The dominant operations are

- reshaping the state vector,
- constructing the coefficient matrix,
- computing

$$
Q,
$$

- transposing the matrix.

Each requires

$$
O(d^2)
$$

operations.

Consequently,

- polynomial in Hilbert-space dimension,
- exponential in qubit number,

which is expected for explicit state-vector manipulation.

---

# Repository Features

The implementation includes

- Exact GF(2) Gaussian elimination.
- Exact certification of graph-state encrypted cloning resources.
- Automatic graph-size validation.
- Polynomial-time certificate verification.
- Exhaustive search over balanced partitions.
- Explicit graph-state construction.
- Reduced density matrix computation.
- Verification of maximal mixedness.
- Automatic decoder construction.
- Numerical verification of decoder unitarity.
- Automatic reconstruction verification.
- Publication-quality graph visualization.
- PDF and 600 dpi PNG figure generation.
- Built-in benchmark graph families.

---

# Main Functions

| Function | Description |
|----------|-------------|
| `gf2_rank()` | Computes the rank of a binary matrix over GF(2). |
| `gf2_is_invertible()` | Tests invertibility over GF(2). |
| `adjacency_matrix()` | Constructs an adjacency matrix from an edge list. |
| `cut_matrix()` | Extracts the cut matrix associated with a partition. |
| `validate_graph_size()` | Checks that the graph contains exactly $$2mk$$ vertices. |
| `verify_certificate()` | Polynomial-time verification of a candidate certificate. |
| `find_certificate_exact()` | Exact exhaustive implementation of GSECC. |
| `graph_state_from_adjacency()` | Constructs a graph-state vector. |
| `ghz_state()` | Generates an $$n$$-qubit GHZ state. |
| `bell_pair_matching_graph()` | Generates Bell-pair matching graphs. |
| `star_graph()` | Generates GHZ (star) graphs. |
| `path_graph()` | Generates linear cluster graphs. |
| `grid_graph()` | Generates rectangular cluster graphs. |
| `reduced_density_matrix()` | Computes reduced density matrices. |
| `rho_S_matches_maximally_mixed()` | Verifies maximal mixedness of $$\rho_S$$. |
| `construct_decoder()` | Implements GSDC and verifies the decoder. |
| `plot_graph()` | Generates publication-quality figures. |
| `report_certificate()` | Complete certification pipeline. |
| `report_non_graph_state()` | Applies GSDC to arbitrary quantum states. |

---

# Installation

Clone the repository

```bash
git clone https://github.com/your_username/Graph-State-Encrypted-Cloning.git
cd Graph-State-Encrypted-Cloning
```

Install the required packages

```bash
pip install -r requirements.txt
```

---

# Requirements

The implementation depends on

- Python 3.10 or newer
- NumPy
- NetworkX
- Matplotlib

These can be installed using

```bash
pip install numpy matplotlib networkx
```

---

# Running the Code

Execute

```bash
python gsecc.py
```

The script automatically

- validates graph parameters,
- certifies graph states,
- computes reduced density matrices,
- constructs decoder unitaries,
- verifies numerical correctness,
- generates publication-quality figures.

---

# Generated Output

For every certified graph the script reports

- certification status,
- balanced partition,
- GF(2) rank,
- reduced density matrix verification,
- decoder unitarity,
- reconstruction residual.

Figures are automatically saved as

- PDF,
- 600 dpi PNG.

---

# Repository Structure

```text
Graph-State-Encrypted-Cloning/

├── README.md
├── gsecc.py
├── requirements.txt
├── LICENSE
└── figures/
```

---

# Demonstration Examples

The script automatically evaluates

- Complete graph
- Cycle graph
- Linear cluster graph
- Cluster grid
- Bell-pair matching graph
- GHZ (star) graph
- Dense Erdős–Rényi random graph
- Sparse Erdős–Rényi random graph
- GHZ state vector
- Invalid parameter example

Each example

- validates the graph,
- performs exact GSECC certification,
- constructs the decoder whenever certification succeeds,
- verifies maximal mixedness,
- verifies decoder unitarity,
- generates publication-quality figures.

---

