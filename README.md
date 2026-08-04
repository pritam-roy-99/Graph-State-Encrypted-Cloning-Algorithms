# Graph-State Encrypted Cloning Certification (GSECC)

## Objective

Given a graph

$$
G=(V,E),
$$

GSECC determines whether there exists a balanced partition

$$
V=S\cup N,\qquad |S|=|N|=mk,
$$

such that

$$
\mathrm{rank}_{GF(2)}(\Gamma_{S,N})=mk.
$$

If this condition holds, the graph state is certified as a valid encrypted cloning resource. Equivalently,

$$
\rho_S=\frac{I}{2^{mk}},
$$

so the signal subsystem is maximally mixed.

---

## Algorithm

Given the graph adjacency matrix, GSECC proceeds as follows.

1. Verify that the graph contains exactly $2mk$ vertices.
2. Enumerate all balanced bipartitions $(S,N)$ (fixing one vertex to remove the $S\leftrightarrow N$ symmetry).
3. Construct the cut matrix $\Gamma_{S,N}$.
4. Compute $\mathrm{rank}_{GF(2)}(\Gamma_{S,N})$ using Gaussian elimination.
5. If the rank equals $mk$, return $(S,N)$ as a valid certificate.
6. If no valid partition is found after exhaustive search, return `None`.

Since every balanced partition is examined, a return value of `None` is a rigorous proof that no valid encrypted-cloning partition exists.

---

## Computational Complexity

Verifying a candidate partition requires Gaussian elimination over GF(2), giving a complexity of

$$
O((mk)^3).
$$

The exhaustive search examines

$$
\binom{2mk-1}{mk-1}
$$

balanced partitions, resulting in an overall complexity of

$$
O\!\left(\binom{2mk-1}{mk-1}(mk)^3\right).
$$

The exponential scaling arises from the exhaustive search, while verification of an individual certificate remains polynomial.

---

## Practical Scalability

| $mk$ | Balanced partitions |
|-----:|--------------------:|
| 4 | 35 |
| 6 | 462 |
| 8 | 6,435 |
| 10 | 92,378 |
| 20 | $\approx 6.9\times10^{10}$ |

---

## Exact Certification

This implementation performs **exact** certification. Every positive result is a valid certificate, and every negative result is a proof that no balanced partition satisfying

$$
\mathrm{rank}_{GF(2)}(\Gamma_{S,N})=mk
$$

exists.

Only the exhaustive certification algorithm is included in this repository.



---

# Problem 2: Graph-State Decoder Construction (GSDC)

Once GSECC certifies a balanced partition $(S,N)$, the decoder unitary can be constructed explicitly.

## Objective

Given a certified graph state, construct a unitary

$$
W:\mathcal{H}_N\rightarrow\mathcal{H}_N
$$

such that

$$
|G\rangle=(I_S\otimes W)|\Phi_{2^{mk}}\rangle,
$$

where

$$
|\Phi_{2^{mk}}\rangle=
\frac{1}{\sqrt{2^{mk}}}
\sum_i |i\rangle_S|i\rangle_N
$$

is the canonical maximally entangled state.

---

## Decoder Construction

Given a certified partition $(S,N)$, the decoder is constructed as follows.

1. Reorder the qubits so that the signal subsystem precedes the noise subsystem.
2. Express the graph state as

   $$
   |G\rangle=\sum_{i,j}M_{ij}|i\rangle_S|j\rangle_N.
   $$

3. Since

   $$
   \rho_S=\frac{I}{2^{mk}},
   $$

   the matrix

   $$
   Q=\sqrt{2^{mk}}\,M
   $$

   is unitary.

4. The decoder is obtained as

   $$
   W=Q^T.
   $$

The implementation automatically verifies the unitarity of $W$ and the reconstruction of the graph state.

---

# Features

- Exact GSECC certification
- GF(2) Gaussian elimination
- Graph-state generation
- Reduced density matrix computation
- Decoder construction (GSDC)
- Publication-quality graph visualization
- Built-in benchmark graph families

---

# Main Functions

| Function | Description |
|----------|-------------|
| `find_certificate_exact()` | Exact implementation of GSECC. |
| `verify_certificate()` | Verifies a candidate certificate. |
| `construct_decoder()` | Constructs and verifies the decoder unitary. |
| `graph_state_from_adjacency()` | Generates a graph state from an adjacency matrix. |
| `reduced_density_matrix()` | Computes reduced density matrices. |
| `plot_graph()` | Generates publication-quality figures. |
| `report_certificate()` | Runs the complete certification pipeline. |

---

# Installation

```bash
pip install -r requirements.txt
```

---

# Requirements

- Python ≥ 3.10
- NumPy
- Matplotlib
- NetworkX

---

# Running

```bash
python gsecc.py
```

The script automatically

- certifies graph states,
- constructs decoder unitaries,
- verifies numerical correctness,
- generates PDF and PNG figures.

---

# Included Examples

The repository includes examples for

- Complete graphs
- Cycle graphs
- Linear cluster graphs
- Cluster grids
- Bell-pair matching graphs
- GHZ (star) graphs
- Erdős–Rényi random graphs

