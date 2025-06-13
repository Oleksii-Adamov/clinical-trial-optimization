# Solution for Aqora Clinical Trial Optimization Competition 2024 (Third Prize)

More on the competition: https://aqora.io/competitions/ingenii-clinical-trial

# Solution overview

## Summary

I partition input points into clusters, optimize discrepancy for each cluster individually using QAOA, then unite the solutions.

## Clustering

I use [Constrained K-Means Clustering](https://pypi.org/project/k-means-constrained/) for partitioning points into 5 clusters with 20 points each.

## Cost hamiltonian

I reformulate target optimization problem as QUBO by substituting absolute value operations with squaring. I also use second constraint to substitute $x_{i​1}=1-x_{i2}$. Afterward, I assign last patient to group zero, reducing required qubits for cost hamiltonian by one, and construct QUBO matrix.

## QAOA

I use XY-mixer, which preserves a constraint that groups 1 and 2 need to have the same number of patients. I perform QAOA at depth=1 using COBYLA optimizer. I warm-start QAOA with closest pairs of points in different groups in a way that satisfy the constraints.

## Cost function

Instead of using QUBO matrix multiplication in cost function, I compute discrepancy with provided function that uses absolute value operations.


## Unification

Since clusters have an even number of patients and solutions satisfy constraints, trivial unification of these local solutions will also satisfy the global constraints.

## Results

Due to limits in simulating QAOA, my solution achieved a score of only 0.89, which was outperformed by classical methods and quantum annealing solutions.

# How to run

## Input Data Setup

Input for this notebook is provided by the Aqora environment. To set up the environment, you can follow the general Aqora (tutorial)[https://aqora.io/competitions/h2-groundstate-energy/data], using the specific template name for this competition found [here](https://aqora.io/competitions/ingenii-clinical-trial/data).

Alternatively, you can set up the input in the notebook manually:
1. copy code from the [use case](https://aqora.io/competitions/ingenii-clinical-trial/code/package).
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
