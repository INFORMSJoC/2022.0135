[![INFORMS Journal on Computing Logo](https://INFORMSJoC.github.io/logos/INFORMS_Journal_on_Computing_Header.jpg)](https://pubsonline.informs.org/journal/ijoc)

# BilevelJuMP.jl

This archive is distributed in association with the [INFORMS Journal on
Computing](https://pubsonline.informs.org/journal/ijoc) under the
[MIT license](LICENSE.md).

The software and data in this repository are associated with the paper
[BilevelJuMP.jl: modeling and solving bilevel optimization problems in Julia](https://doi.org/10.1287/ijoc.2022.0135)
by Joaquim Dias Garcia, Guilherme Bodin and Alexandre Street.

This repository is a snapshot of the project, taken on 2023-08-19 from
[https://github.com/joaquimg/BilevelJuMP.jl](https://github.com/joaquimg/BilevelJuMP.jl) at commit
[`7436b38acc1b74d2aea3830e555e076d9b89aa01`](https://github.com/joaquimg/BilevelJuMP.jl/commit/7436b38acc1b74d2aea3830e555e076d9b89aa01),
and is provided for historical interest.

Readers are directed to [https://github.com/joaquimg/BilevelJuMP.jl](https://github.com/joaquimg/BilevelJuMP.jl)
for the actively developed project repository, and to
[https://joaquimg.github.io/BilevelJuMP.jl/dev/](https://joaquimg.github.io/BilevelJuMP.jl/dev/)
for the latest documentation.

## Cite

To cite the contents of this repository, please cite both the paper and this repo, using their respective DOIs.

https://doi.org/10.1287/ijoc.2022.0135

https://doi.org/10.1287/ijoc.2022.0135.cd

Below is the BibTex for citing this snapshot of the respoitory.

```
@article{bileveljump2023,
  author =        {Joaquim {Dias Garcia} and Guilherme Bodin and Alexandre Street},
  publisher =     {INFORMS Journal on Computing},
  title =         {{BilevelJuMP.jl}},
  year =          {2023},
  doi =           {10.1287/ijoc.2022.0135.cd},
  url =           {https://github.com/INFORMSJoC/2022.0135},
}  
```

## Description

BilevelJuMP is a package for modeling and solving bilevel optimization problems
in Julia.

As an extension of the [JuMP](https://jump.dev/) modeling language, BilevelJuMP
allows users to employ the usual JuMP syntax with minor modifications to
describe the problem and query solutions.

Many modeling features are available in BilevelJuMP, some of which are unique
while others are also not widely available. The main features supported are:

- Arbitrary JuMP models in the upper level (NLP, Conic, MIP)
- Conic constraints and quadratic objectives in the lower-level
- Dual variables of the lower level in the upper level
- MPEC reformulations with MIP or NLP solvers
- MixedMode MPEC reformulation: select the best reformulation for each constraint separately

The currently available methods are based on re-writing the problem using the
KKT conditions of the lower level. For that, we make strong use of
[Dualization.jl](https://github.com/JuMP-dev/Dualization.jl).

## Installation

In Julia, the latest version of the package can be installed as follows:
```julia
import Pkg
Pkg.add("BilevelJuMP")
```
Afterwards, the functionality can be made available in a module or REPL through:
```julia
using BilevelJuMP
```

## Example from the paper

First, install BilevelJuMP as described in the previous section.

Second, install a solver, in the paper example we consider SCIP. To install SCIP on Linux or MacOS run:
```julia
import Pkg
Pkg.add("SCIP")
```
To install SCIP on windows follow the procedure detailed [here](https://github.com/scipopt/SCIP.jl#custom-installations).

Third, run the example of the paper in the Julia REPL:

```julia
using BilevelJuMP, SCIP
model = BilevelModel(SCIP.Optimizer,
    mode = BilevelJuMP.SOS1Mode())
@variable(Upper(model), y)
@variable(Lower(model), x)
@objective(Upper(model), Min, 3x + y)
@constraints(Upper(model), begin
    x <= 5
    y <= 8
    y >= 0
end)
@objective(Lower(model), Min, -x)
@constraints(Lower(model), begin
     x +  y <= 8
    4x +  y >= 8
    2x +  y <= 13
    2x - 7y <= 0
end)
optimize!(model)
objective_value(model) # = 6.133...
value(x) # = 3.5 * 8/15
value(y) # = 8/15
```