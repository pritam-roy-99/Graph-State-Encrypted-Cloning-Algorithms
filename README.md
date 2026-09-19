# Exact Graph-State Encrypted Cloning Certification (GSECC)

## Overview

This repository implements an exact certification framework for determining whether a graph state can serve as a resource for multiparty encrypted quantum cloning under a fixed sector-wise two-Pauli encoder.

The main structural point is that **full signal-noise cut rank is sufficient, but is not necessary in general**.

For even $m$, full cut rank is necessary and sufficient. For odd $m$, certain rank-deficient graph states can still support exact recovery when the complete cut kernel is compatible with the chosen encoder.

The implementation therefore goes beyond a rank-only test and evaluates the exact graph-state condition

$\ker(B_{S\mathcal N}^{\mathsf T})\subseteq\mathcal E_{P,Q}^{(m,k)}(A_S)$,

where

- $S$ is the signal subsystem,
- $\mathcal N$ is the common noise/key subsystem,
- $B_{S\mathcal N}$ is the signal-noise cut matrix,
- $A_S$ is the adjacency matrix induced on the signal subsystem,
- $(P,Q)$ specifies the ordered two-Pauli encoder,
- $\mathcal E_{P,Q}^{(m,k)}(A_S)$ is the corresponding exceptional kernel subspace.

The code performs an exhaustive graph-level search over balanced signal-noise cuts and, when required, over logical-sector decompositions of the signal subsystem.

---

# 1. Encrypted-Cloning Setting

Consider a graph state on $2mk$ qubits.

The vertices are divided into two equally sized subsystems,

$V=S\cup\mathcal N,\qquad |S|=|\mathcal N|=mk.$

Here

- $m$ is the number of encrypted recovery pathways,
- $k$ is the number of logical qubits,
- $\nu=mk$ is the number of signal qubits and also the number of noise/key qubits.

The signal subsystem is further decomposed into $k$ logical sectors,

$S=\mathsf S_1\cup\cdots\cup\mathsf S_k,\qquad |\mathsf S_j|=m.$

The sector $\mathsf S_j$ contains the $m$ signal qubits associated with logical input qubit $A_j$.

For a chosen balanced cut, the graph adjacency matrix is written as

$\Gamma=\begin{pmatrix}A_S&B_{S\mathcal N}\\B_{S\mathcal N}^{\mathsf T}&A_{\mathcal N}\end{pmatrix}.$

All ranks, kernels, and linear-algebra calculations used by GSECC are performed over $\mathbb F_2$.

---

# 2. Cut Rank and Signal-Noise Entanglement

For a fixed signal-noise cut, define

$B\equiv B_{S\mathcal N}.$

The signal-noise entanglement of a graph state is determined by the binary cut rank

$r=\text{rank}_{\mathbb F_2}B.$

For graph states,

$S(\rho_S)=r.$

In particular,

$\text{rank}_{\mathbb F_2}B=mk$

is equivalent to

$\rho_S=\frac{I_S}{2^{mk}}.$

Therefore every full-rank balanced cut provides a valid maximally entangled resource.

However, full rank is not the complete criterion.

---

# 3. Why Rank Alone Is Not Enough

For a graph state, the reduced signal state can be written as

$\rho_S=\frac{1}{2^{mk}}\sum_{x\in\ker(B^{\mathsf T})}K_S(x),$

where

$K_S(x)\doteq X^xZ^{A_Sx}.$

Thus the complete kernel

$\ker(B^{\mathsf T})$

specifies exactly which signal-side stabilizer correlations survive after tracing out $\mathcal N$.

If

$\ker(B^{\mathsf T})=\{0\},$

then

$\rho_S=\frac{I_S}{2^{mk}}.$

If instead

$\ker(B^{\mathsf T})\neq\{0\},$

the resource is rank deficient across the signal-noise cut.

For odd $m$, some nonzero kernel directions can nevertheless be compatible with exact encrypted cloning for particular encoder classes.

Consequently,

$\text{rank}_{\mathbb F_2}B<mk$

does **not** automatically imply failure.

The exact operational property depends on both the graph state and the encoder.

---

# 4. Exact Graph-State Criterion

For a fixed balanced cut, fixed sector decomposition, and fixed two-Pauli encoder $U_{P,Q}^{(m,k)}$, the graph state is valid exactly when

$\ker(B_{S\mathcal N}^{\mathsf T})\subseteq\mathcal E_{P,Q}^{(m,k)}(A_S).$

Here $\mathcal E_{P,Q}^{(m,k)}(A_S)$ is the exceptional subspace selected by the encoder.

Because both

$\ker(B^{\mathsf T})$

and

$\mathcal E_{P,Q}^{(m,k)}(A_S)$

are linear subspaces over $\mathbb F_2$, it is sufficient to test a basis of the cut kernel.

This gives an exact finite binary-linear-algebra certification test for every prescribed realization.

---

# 5. Sector-Indicator Map

For odd $m$, every nonzero exceptional kernel vector must be constant within each logical sector.

Define the sector-indicator map

$F:\mathbb F_2^k\rightarrow\mathbb F_2^{mk}$

by

$Fc=(c_1\mathbf 1_m,\ldots,c_k\mathbf 1_m),$

where

$c=(c_1,\ldots,c_k)\in\mathbb F_2^k.$

Thus every exceptional vector must have the form

$x=Fc.$

Equivalently, within each logical sector, all $m$ entries of $x$ must agree.

The exact implementation therefore first checks whether every kernel basis vector is sector constant.

---

# 6. Parity- and Encoder-Dependent Classification

The exact criterion separates into four cases.

## 6.1 Even $m$

For even $m$, the exceptional subspace is trivial for every two-Pauli encoder,

$\mathcal E_{P,Q}^{(m,k)}(A_S)=\{0\}.$

Therefore

$\text{valid}\iff\ker(B^{\mathsf T})=\{0\},$

which is equivalent to

$\text{rank}_{\mathbb F_2}B=mk.$

Hence full signal-noise cut rank is both necessary and sufficient whenever $m$ is even.

---

## 6.2 Odd $m$: $XZ/ZX$ Encoder Class

For the $XZ/ZX$ encoder class, every exceptional vector must have the form

$x=Fc$

and satisfy

$(A_S+I_{mk})Fc=0$

together with

$\mathbf 1_k^{\mathsf T}c=0.$

Thus the exact condition is

$\ker(B^{\mathsf T})\subseteq\{Fc:(A_S+I_{mk})Fc=0,\ \mathbf 1_k^{\mathsf T}c=0\}.$

A necessary rank consequence is

$\text{rank}_{\mathbb F_2}B\ge mk-k+1.$

This rank inequality is **necessary but not sufficient**.

A graph can satisfy the rank bound and still fail because the orientation of its complete cut kernel is incompatible with the exceptional subspace.

---

## 6.3 Odd $m$: $YZ/ZY$ Encoder Class

For the $YZ/ZY$ encoder class, every exceptional vector must have the form

$x=Fc$

and satisfy

$A_SFc=0.$

Thus the exact condition is

$\ker(B^{\mathsf T})\subseteq\{Fc:A_SFc=0\}.$

A necessary rank consequence is

$\text{rank}_{\mathbb F_2}B\ge(m-1)k.$

Again, the rank inequality alone is not sufficient.

The $YZ/ZY$ class is special because it can permit the maximal exceptional nullity

$\dim\ker(B^{\mathsf T})=k.$

When this occurs,

$\text{rank}_{\mathbb F_2}B=(m-1)k.$

Thus this branch can attain the minimum signal-noise entanglement compatible with exact all-output recovery.

---

## 6.4 Odd $m$: $XY/YX$ Encoder Class

For the $XY/YX$ encoder class, the exceptional subspace is again trivial,

$\mathcal E_{P,Q}^{(m,k)}(A_S)=\{0\}.$

Therefore

$\text{valid}\iff\ker(B^{\mathsf T})=\{0\},$

or equivalently,

$\text{rank}_{\mathbb F_2}B=mk.$

Thus full cut rank remains necessary and sufficient for this encoder class.

---

# 7. Summary of Exact Resource Classes

| $m$ | Encoder class | Exact validity condition | Necessary rank consequence |
|---|---|---|---|
| even | any $P\neq Q$ | $\ker(B^{\mathsf T})=\{0\}$ | $\text{rank}_{\mathbb F_2}B=mk$ |
| odd | $XZ/ZX$ | $\ker(B^{\mathsf T})\subseteq\mathcal E_Y(A_S)$ | $\text{rank}_{\mathbb F_2}B\ge mk-k+1$ |
| odd | $YZ/ZY$ | $\ker(B^{\mathsf T})\subseteq\mathcal E_X(A_S)$ | $\text{rank}_{\mathbb F_2}B\ge(m-1)k$ |
| odd | $XY/YX$ | $\ker(B^{\mathsf T})=\{0\}$ | $\text{rank}_{\mathbb F_2}B=mk$ |

For the two exceptional odd-$m$ branches, the rank bounds are only preliminary filters.

The final certification decision is determined by the complete cut-kernel inclusion.

---

# 8. Exact Fixed-Realization Test

For a prescribed realization

$\mathfrak R=(S,\mathcal N,\{\mathsf S_j\}_{j=1}^k),$

the function

```python
verify_gsecc_realization()
