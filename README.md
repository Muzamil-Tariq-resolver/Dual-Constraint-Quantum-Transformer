# Dual Constraint Quantum Transformer (DCQT)

A hard-constrained Transformer architecture for quantum-state prediction that structurally enforces unit probability normalization and exact energy conservation by construction.

## Overview

The Dual Constraint Quantum Transformer (DCQT) introduces a differentiable output parameterization that satisfies two quantum constraints independently of model parameters and training:

* **Probability normalization:** \(\sum_n p_n = 1\)
* **Energy conservation:** \(\sum_n E_n p_n = E_0\)

The predicted populations are constructed as convex combinations of analytically computed vertices of the probability-energy feasible polytope. Since every vertex satisfies both constraints, every convex combination also satisfies them exactly in mathematical precision.

Unlike soft-constrained approaches, DCQT does not use:

* Probability penalty losses
* Energy penalty losses
* Lagrange multipliers
* Iterative projection
* Post-processing correction

The Transformer therefore learns the quantum-state evolution while physical feasibility is embedded directly into the output representation.

## Research Setting

The current implementation considers:

* **Closed quantum system**
* **Finite-dimensional truncated Hilbert space**
* **32-state truncated harmonic oscillator**
* **Time-independent Hamiltonian**
* **Diagonal Hamiltonian in the chosen eigenbasis**
* **Varying initial quantum states**
* **Varying conserved initial energies**
* **Single-step quantum-state prediction**

The quantum state is represented in the energy eigenbasis as

$$
|\psi(t)\rangle =
\sum_{n=1}^{N} c_n(t)|n\rangle,
$$

with

$$
p_n(t)=|c_n(t)|^2.
$$

The feasible population space is defined by

$$
p_n\geq0,
\qquad
\sum_n p_n=1,
\qquad
\sum_n E_n p_n=E_0.
$$

## Method

DCQT separates the prediction of populations and phases.

The Transformer predicts the parameters required to construct the output state. The population layer maps the network output to the feasible probability-energy polytope through a convex combination of its analytically computed vertices.

The complex coefficients are then reconstructed as

$$
c_n(t)=\sqrt{p_n(t)}\,e^{i\theta_n(t)}.
$$

This guarantees

$$
\sum_n |c_n(t)|^2=1
$$

and

$$
\sum_n E_n|c_n(t)|^2=E_0
$$

for every admissible model output.

## Training

The model is trained using a wavefunction prediction loss:

$\mathcal{L} = \|\psi_{\mathrm{pred}}-\psi_{\mathrm{true}}\|_2^2$

No probability or energy conservation terms are included in the loss.

This allows the experiment to directly compare structural constraint enforcement against a conventional Transformer using soft penalty constraints.

## Experiments

The experimental evaluation compares:

1. **DCQT** — hard structural constraints
2. **Soft-Constrained Transformer** — probability and energy penalty losses

The evaluation considers:

* Probability constraint violation
* Energy constraint violation
* Wavefunction prediction error
* Quantum-state fidelity

The dataset contains random quantum superpositions and projected Gaussian wavepackets with varying initial energies.

## Key Result

DCQT maintains probability and energy violations close to the float32 numerical precision limit, demonstrating that the constraints arise from the model's parameterization rather than from optimization.

The constrained model also achieves improved wavefunction prediction accuracy and quantum-state fidelity under the evaluated experimental configuration.

## Limitations

The current implementation is a controlled proof-of-concept.

The Hamiltonian is diagonal in the working basis, so the modal populations remain constant and the temporal dynamics are primarily encoded in the phases. The current model also performs **single-step prediction** rather than autoregressive long-horizon rollout.

Therefore, this repository demonstrates structural enforcement of simultaneous probability and energy constraints rather than general energy-conserving population transfer.

## Future Work

Planned extensions include:

* Autoregressive multi-step quantum dynamics
* Nontrivial population-transfer systems
* General non-diagonal Hermitian Hamiltonians
* Time-dependent Hamiltonians
* Open quantum systems
* Larger Hilbert spaces and many-body systems
* Additional simultaneous conservation laws


Muzamil Tariq (tariqjee1919@gmail.com)
