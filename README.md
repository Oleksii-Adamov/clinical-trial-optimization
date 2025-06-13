# Solution for Aqora Clinical Trial Optimization Competition 2024 (Third Prize)

More on the competition: https://aqora.io/competitions/ingenii-clinical-trial

# Solution overview

## Summary

I partitioned the input points into clusters, optimized the discrepancy for each cluster individually using QAOA, and then united the solutions.

## Clustering

I use [Constrained K-Means Clustering](https://pypi.org/project/k-means-constrained/) for partitioning points into 5 clusters with 20 points each.

## Cost hamiltonian

I reformulated the target optimization problem as a QUBO by substituting absolute value operations with squaring. I also used the second constraint to substitute $x_{i​1}=1-x_{i2}$. Afterward, I assigned the last patient to group zero, which reduced the number of required qubits for the Cost Hamiltonian by one, and then constructed the QUBO matrix.

## QAOA

I used an XY-mixer, which preserves the constraint that groups 1 and 2 must have the same number of patients. I performed QAOA at a depth of $p=1$ using the COBYLA optimizer. I warm-started the QAOA by initializing it with the closest pairs of points assigned to different groups in a way that satisfied the constraints.

## Cost function

Instead of using QUBO matrix multiplication in the cost function, I computed the discrepancy with the provided function that uses absolute value operations.

## Unification

Since the clusters have an even number of patients and each local solution satisfies its constraints, a trivial unification of these local solutions also satisfies the global constraints.

## Results

Due to limits in simulating QAOA, my solution achieved a score of only 0.89, which was outperformed by classical methods and quantum annealing solutions.

# How to run

## Input Data Setup

Input for this notebook is provided by the Aqora environment. To set up the environment, you can follow the general Aqora [tutorial](https://aqora.io/competitions/h2-groundstate-energy/data), using the specific template name for this competition found [here](https://aqora.io/competitions/ingenii-clinical-trial/data).

Alternatively, you can set up the input in the notebook manually:
1. Copy the code from the [use case](https://aqora.io/competitions/ingenii-clinical-trial/code/package).
2. Download the [data] (https://aqora.io/competitions/ingenii-clinical-trial/code/data).
3. Define the input variable in your notebook:
   ```Python
   w = np.loadtxt("pbc.csv", delimiter=",", dtype=float)
   input = ClinicalTrial(w, 0.5)
   ```

## Requirments
```
requires-python = ">=3.8"
dependencies = ["numpy ==1.26.4", "qiskit ~=1.2.2", "qiskit-aer-gpu ~=0.15.1", "closely ~=19.0.2", "k-means-constrained ~=0.7.3"]
```
