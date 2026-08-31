# sparse-recovery-from-scratch
From-scratch implementation of sparse signal recovery algorithms (matching pursuit, LASSO). We explore how a sparse signal can be reconstructed in conditions where there are fewer 
measurements than unknowns.

## Problem Setup

Given `n` unknowns, of which a number `k` are nonzero, and `m` 
indirect linear measurements (`m < n`), can we recover the true 
sparse signal `x_true` using only the measurement matrix `A` and the 
observed results `y = A @ x_true`?

## Current Status

**Matching pursuit: successfully implemented and working.**

The initial implementation utilized raw, un-normalized dot-product results for both candidate selection and coefficient 
estimation. As such, there was a bias toward the selection of columns with large magnitudes regardless of 
true alignment, and incorrectly-scaled coefficients caused the residual to grow instead of shrink across rounds. This lead to significant divergence between the empirical and true sparse signals.

This was fixed by separating the selection functionality into two distinct roles. The latest implementation now uses normalized columns for 
candidate selection to avoid biases toward large magnitudes of little prominence, while a separate least-squares calculation 
uses the original un-normalized column to compute the correctly-scaled coefficient stored in `x_hat`.

At `n=50`, `m=20`, `k=5` (measurement-to-sparsity ratio `m/k = 4`), 
the algorithm achieves partial recovery. Trials under the latest implementation (which can be run on the lab notebook) typically identify 2 out of 5 positions 
correctly. This appears to reflect a genuine limitation posed by our measurement-to-sparsity ratio, rather than an effect of implementation error. Compressed sensing theory (the Candès-Tao Restricted Isometry Property), states that reliable recovery generally requires m = O(k·log(n/k)) measurements. As such, we can presume that the number of unknowns is a comparatively weak factor in the success of our recovery, as n only grows logarithmically. Further tests should likely test varying the m/k ratio across a range both above and below our current value (m/k = 4), to empirically observe its effects on recovery success and to test the hypothesis that recovery success scales more positively with higher m/k ratios.

## Next Steps

- Systematically variation of `m`, `k`, and `n` to empirically test the 
  effect of the measurement-to-sparsity ratio on recovery reliability
- Implementation LASSO via coordinate descent as a second recovery method
- Comparison matching pursuit vs. LASSO recovery quality

## Files

- `sparse_recovery.ipynb` — synthetic problem setup and matching 
  pursuit implementation
