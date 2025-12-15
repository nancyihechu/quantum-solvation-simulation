# Quantum Solvation Simulation of Methanol Using SQD IEF-PCM

This repository contains my implementation of **Sample-based Quantum Diagonalization (SQD)** with **Implicit Solvent IEF-PCM** to simulate the electronic structure and solvation free energy of methanol using **Qiskit Serverless** and IBM QPUs.

## Implementation Overview

The notebook `Quantum_Simulation_Methanol.ipynb` demonstrates my implementation of the workflow to compute the **ground-state energy** and **solvation free energy** of a methanol molecule in an implicit solvent (water). This implementation leverages **LUCJ circuits** for quantum sampling and **SQD post-processing** to obtain the final energy estimates.

### Workflow Steps

1. **Upload the Qiskit Function template**
   - The workflow template was instantiated and uploaded using:
     ```python
     from qiskit_ibm_catalog import QiskitFunction

     template = QiskitFunction(
         title="sqd_pcm_template",
         entrypoint="sqd_pcm_entrypoint.py",
         working_dir="./source_files/"
     )
     ```
   - This allowed the function to be executed remotely on Qiskit Serverless.

2. **Execute the template on IBM QPU**
   - The template was run on the IBM QPU Heron R1 `ibm_torino`.
   - Inputs included the methanol molecular geometry, active space selection (14 electrons in 12 orbitals), and LUCJ/SQD configuration options.

3. **Quantum sampling with LUCJ circuits**
   - The LUCJ circuit was automatically constructed based on the selected active space and qubit layout.
   - Same-spin orbitals were connected in a zig-zag topology; opposite-spin orbitals were connected at periodic intervals via ancilla qubits.
   - The circuit was transpiled for the QPU and executed, generating ~200,000 bitstrings representing sampled electron configurations.

4. **Post-processing using SQD**
   - The sampled bitstrings were used in the SQD procedure to recover the ground-state configuration and compute the solvation free energy.
   - Multiple batches and iterations of S-CORE were performed to improve statistical accuracy.

### Results

| Quantity | Value (Hartree) |
|----------|----------------|
| Ground-state energy | -115.15860274515678 |
| Solvation free energy | -0.007183902681386506 |

**Notes:**
- `sci_solver_total_duration` was ~311 seconds.
- Energy values are reported in Hartree; the solvation free energy represents the implicit solvent contribution.

---

## Repository Structure
Quantum_Solvation_Simulation/
├── README.md
├── Quantum_Simulation_Methanol.ipynb
└── source_files/
├── solve_solvent.py
└── sqd_pcm_entrypoint.py
