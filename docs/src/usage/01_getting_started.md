# [Getting started](@id getting_started)

## Installing

In Julia open the package manager in the REPL via `]` and run:

```julia
(v1.0) pkg> add git@github.com:haampie/IRAM.jl.git
```

Then use the package.

```julia
using IRAM
```

## Construct a partial Schur decomposition

IRAM.jl exports the `partial_schur` function which can be used to obtain a 
partial Schur decomposition of any matrix `A`.

```@docs
partial_schur
```

## From a Schur decomposition to an eigendecomposition
The eigenvalues and eigenvectors are obtained from the Schur form with the 
`schur_to_eigen` function that is exported by IRAM.jl:

```julia
λs, X = schur_to_eigen(decomp::PartialSchur)
```

Note that whenever the matrix `A` is real-symmetric or Hermitian, the partial 
Schur decomposition coincides with the partial eigendecomposition, so in that 
case there is no need for the transformation.

## Example

Here we compute the first ten eigenvalues and eigenvectors of a tridiagonal
sparse matrix.

```julia
julia> using IRAM, LinearAlgebra, SparseArrays
julia> A = spdiagm(
           -1 => fill(-1.0, 99),
            0 => fill(2.0, 100), 
            1 => fill(-1.0, 99)
       );
julia> decomp, history = partial_schur(A, nev=10, tol=1e-6, which=SR());
julia> history
Converged after 178 matrix-vector products
julia> norm(A * decomp.Q - decomp.Q * decomp.R)
3.717314639756976e-8
julia> λs, X = schur_to_eigen(decomp);
julia> norm(A * X - X * Diagonal(λs))
3.7173146389810755e-8
```