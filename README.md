# Exact Graph-State Encrypted Cloning Certification (GSECC)

## Overview

This repository implements an exact certification framework for determining when a graph state can serve as a resource for multiparty encrypted quantum cloning under a fixed sector-wise two-Pauli encoder.

The central point is that **full signal-noise cut rank is sufficient, but not necessary in general**.

For even numbers of encrypted outputs, full cut rank remains necessary and sufficient. For odd numbers of outputs, however, certain rank-deficient graph states can still support exact recovery when the complete cut kernel is compatible with the chosen encoder.

The implementation therefore goes beyond a rank-only test and checks the exact graph-state condition

\[
\ker\!\left(B_{S\mathcal N}^{\mathsf T}\right)
\subseteq
\mathcal E_{P,Q}^{(m,k)}(A_S),
\]

where:

- \(S\) is the signal subsystem,
- \(\mathcal N\) is the common noise/key subsystem,
- \(B_{S\mathcal N}\) is the signal-noise cut matrix,
- \(A_S\) is the adjacency matrix induced on the signal subsystem,
- \((P,Q)\) specifies the ordered two-Pauli encoder class,
- \(\mathcal E_{P,Q}^{(m,k)}(A_S)\) is the corresponding exceptional kernel subspace.

The code performs an exhaustive graph-level search over balanced signal-noise cuts and, when required, over logical-sector decompositions of the signal subsystem.

---

# 1. Encrypted-Cloning Setting

Consider a graph state on

\[
2mk
\]

qubits.

The vertices are partitioned into two equally sized subsystems,

\[
V=S\cup\mathcal N,
\qquad
|S|=|\mathcal N|=mk.
\]

Here:

- \(m\) is the number of encrypted recovery pathways,
- \(k\) is the number of logical qubits,
- \(\nu=mk\) is the number of signal qubits and also the number of key/noise qubits.

The signal subsystem is further decomposed into \(k\) logical sectors,

\[
S=\mathsf S_1\cup\cdots\cup\mathsf S_k,
\qquad
|\mathsf S_j|=m.
\]

Each logical sector contains the \(m\) signal qubits associated with one logical input qubit.

For a chosen balanced cut, write the graph adjacency matrix as

\[
\Gamma=
\begin{pmatrix}
A_S & B_{S\mathcal N}\\
B_{S\mathcal N}^{\mathsf T} & A_{\mathcal N}
\end{pmatrix}.
\]

All ranks, kernels, and linear-algebra operations used in the certification algorithm are evaluated over

\[
\mathbb F_2.
\]

---

# 2. Why Cut Rank Alone Is Not Enough

For a graph state, define

\[
B\equiv B_{S\mathcal N}.
\]

The signal-noise entanglement is determined by the binary cut rank,

\[
r=
\operatorname{rank}_{\mathbb F_2}B.
\]

In particular,

\[
S(\rho_S)=r,
\]

and

\[
r=mk
\]

is equivalent to

\[
\rho_S=\frac{I_S}{2^{mk}}.
\]

Therefore every full-rank balanced cut gives a valid encrypted-cloning resource.

However, the exact criterion is more general.

The reduced state of the signal subsystem has the graph-state stabilizer expansion

\[
\rho_S
=
\frac{1}{2^{mk}}
\sum_{x\in\ker(B^{\mathsf T})}
K_S(x),
\]

where

\[
K_S(x)
\doteq
X^x Z^{A_Sx}.
\]

Thus the complete kernel

\[
\ker(B^{\mathsf T})
\]

specifies the signal-side correlations responsible for any departure from maximal mixing.

For odd \(m\), some of these correlations can be harmless for particular encoders. Consequently,

\[
\operatorname{rank}_{\mathbb F_2}B<mk
\]

does **not** automatically imply failure.

The exact resource property depends on the pair

\[
\text{graph resource}+\text{encoder}.
\]

---

# 3. Exact Graph-State Criterion

For a fixed balanced cut, fixed logical-sector decomposition, and fixed two-Pauli encoder \(U_{P,Q}^{(m,k)}\), the graph state is valid exactly when

\[
\boxed{
\ker(B_{S\mathcal N}^{\mathsf T})
\subseteq
\mathcal E_{P,Q}^{(m,k)}(A_S)
}
\]

where \(\mathcal E_{P,Q}^{(m,k)}(A_S)\) is the exceptional subspace selected by the encoder.

Because both the cut kernel and the exceptional space are linear subspaces over \(\mathbb F_2\), it is sufficient to test a basis of

\[
\ker(B^{\mathsf T}).
\]

---

# 4. Sector-Indicator Map

For odd \(m\), every nonzero exceptional kernel vector must be constant within each logical sector.

Define

\[
F:\mathbb F_2^k\rightarrow\mathbb F_2^{mk}
\]

by

\[
Fc=
(c_1\mathbf 1_m,\ldots,c_k\mathbf 1_m),
\]

where

\[
c=(c_1,\ldots,c_k)\in\mathbb F_2^k.
\]

Thus an exceptional vector must have the form

\[
x=Fc.
\]

Computationally, this means that every kernel basis vector must first be checked for constancy within every logical sector.

---

# 5. Parity- and Encoder-Dependent Classification

The exact criterion separates into four branches.

## Even \(m\)

For every two-Pauli encoder,

\[
\mathcal E_{P,Q}^{(m,k)}(A_S)=\{0\}.
\]

Hence

\[
\boxed{
\text{valid}
\iff
\ker(B^{\mathsf T})=\{0\}
}
\]

or equivalently,

\[
\boxed{
\operatorname{rank}_{\mathbb F_2}B=mk.
}
\]

Thus full cut rank is necessary and sufficient for even \(m\).

---

## Odd \(m\): \(XZ/ZX\) encoder class

Every exceptional vector must satisfy

\[
x=Fc,
\]

together with

\[
(A_S+I_{mk})Fc=0
\]

and

\[
\mathbf 1_k^{\mathsf T}c=0.
\]

Therefore

\[
\boxed{
\ker(B^{\mathsf T})
\subseteq
\left\{
Fc:
(A_S+I_{mk})Fc=0,\;
\mathbf 1_k^{\mathsf T}c=0
\right\}.
}
\]

A necessary rank condition is

\[
\boxed{
\operatorname{rank}_{\mathbb F_2}B
\ge
mk-k+1.
}
\]

This rank bound is necessary but **not sufficient**. The complete kernel must satisfy the exceptional-space condition.

---

## Odd \(m\): \(YZ/ZY\) encoder class

Every exceptional vector must satisfy

\[
x=Fc
\]

and

\[
A_SFc=0.
\]

Thus

\[
\boxed{
\ker(B^{\mathsf T})
\subseteq
\left\{
Fc:A_SFc=0
\right\}.
}
\]

A necessary rank condition is

\[
\boxed{
\operatorname{rank}_{\mathbb F_2}B
\ge
(m-1)k.
}
\]

Again, the rank inequality alone is not sufficient.

This branch permits the maximal allowed nullity

\[
\dim\ker(B^{\mathsf T})=k,
\]

and therefore can attain

\[
\operatorname{rank}_{\mathbb F_2}B=(m-1)k.
\]

---

## Odd \(m\): \(XY/YX\) encoder class

The exceptional subspace is trivial,

\[
\mathcal E_{P,Q}^{(m,k)}(A_S)=\{0\}.
\]

Hence

\[
\boxed{
\text{valid}
\iff
\operatorname{rank}_{\mathbb F_2}B=mk.
}
\]

Full cut rank is again necessary and sufficient.

---

# 6. Summary of Exact Resource Classes

| \(m\) | Encoder class | Exact condition | Necessary rank consequence |
|---|---|---|---|
| even | any \(P\neq Q\) | \(\ker B^{\mathsf T}=\{0\}\) | \(\operatorname{rank}B=mk\) |
| odd | \(XZ/ZX\) | \(\ker B^{\mathsf T}\subseteq\mathcal E_Y(A_S)\) | \(\operatorname{rank}B\ge mk-k+1\) |
| odd | \(YZ/ZY\) | \(\ker B^{\mathsf T}\subseteq\mathcal E_X(A_S)\) | \(\operatorname{rank}B\ge(m-1)k\) |
| odd | \(XY/YX\) | \(\ker B^{\mathsf T}=\{0\}\) | \(\operatorname{rank}B=mk\) |

The rank bounds in the two exceptional odd-\(m\) branches are only preliminary filters.

The final decision always uses the complete cut kernel.

---

# 7. Exact Fixed-Realization Test

For a prescribed realization

\[
\mathfrak R=
(S,\mathcal N,\{\mathsf S_j\}_{j=1}^k),
\]

the function

```python
verify_gsecc_realization()
