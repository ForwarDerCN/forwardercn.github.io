
# 3DGS Review

## Introduction

Record some interesting points in 3DGS here.

## Some thinking points

For gaussian representation:

1. Modify gaussians.
2. Using NeRF-like ray calculations.

For gaussain editing:

3. Extend the dimention of time in elements like SH.
4. Novel representations of pos, ori etc.
5. Compare irradiace with force/mag-field?

For inverse rendering:

6. Use the flow of deferred rendering: *expect the surface points -> calc BRDFs -> utilizing rendering equations -> get final PBR color*. We may avoid the problem of hidden gaussians beneath the surface.
7. Use mesh-gs mixed representations to enhance the surface geometry.

[Go Back to HomePage](../../index.md)
