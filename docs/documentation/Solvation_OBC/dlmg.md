# Multigrid Solver : DL_MG

CASTEP uses a multigrid solver to solve the Poisson Equation (PE). Currently this is done by interfacing to a solver called DL_MG (https://dlmgteam.bitbucket.io/dlmg_docs/). DL_MG is distributed with CASTEP and is compiled in by default. If your version does not include DL_MG your calculation will stop with a descriptive error message. It is possible to compile CASTEP with a system version of DL_MG, however as of CASTEP version 22 the version of DL_MG required is 3.0.0 or higher.

Solving the PE is a memory- and time-consuming process, and you should expect solvation calculations to take at least 2-3 times longer compared to standard CASTEP (also remembering that you will likely have to run two calculations per result -- one in vacuum, and one in solvent).

The solver uses a multigrid approach to solve the PE to second order. To ensure the high-order accuracy necessary for solvation calculations, the solver then applies a high-order defect correction technique, which iteratively corrects the initial solution to a higher order. Consult https://dlmgteam.bitbucket.io/dlmg_docs/ for more information on the defect correction approach used in DL_MG.

##Grid sizes
###Under OBC
One limitation of DL_MG is that the grid sizes it uses are not created equal. Good grid sizes are divisible many times into grids twice as small. For example a grid with 161 points (and so 160 grid-edges in between them) is an excellent choice, since it divides into two grids with 81 points (160 splits into two 80's), these divide into two grids with 41 points, which in turn divide into two grids with 21 points, which divide into two grids with 11 points and so on. This lets the solver use many multigrid levels, increasing efficiency. For contrast, consider a grid with 174 points (and so 173 grid-edges). 173 is prime, and this grid cannot be subdivided at all, making it a poor choice.

Knowing about these limitations, CASTEP will increase (pad) your fine grid dimensions when passing data to and from the multigrid solver. The padding  to give DL_MG enough flexibility. This is done automatically, however the minimum amount of padding can be set via the variable `mg_padding`, the default value of this variable is -1 which tells CASTEP to pad the grid automatically. You will be informed about the amount of padding used at the beginning of the CASTEP calculation via a message similar to the following:

```
 CASTEP fine grid is 126 x 126 x 126 gridpoints, 29.0000 x 29.0000 x 29.0000 bohr.
 DL_MG  fine grid is 137 x 137 x 137 gridpoints, 31.5317 x 31.5317 x 31.5317 bohr.
```

In the padded region the density is set to zero, and the dielectric permittivity is set to the bulk value of the solvent. The solution found to the PE is generally found to be fairly invariant to the amount of padding used. The exception is when the charge density does not tend to zero at the boundaries of the unit cell. Due to the choice of a plane wave basis set it is possible to get *Fourier ringing*, tails of very small, but nonzero charge density extend in all Cartesian directions from your system. It is thus good practice to pad your system with a little vacuum in all directions, say 10 bohr.

There was once an option to truncate the fine grid as is done in ONETEP calculations however with the introduction of MPI parallelism this functionality was broken and removed. Although it is still occasionally referenced in the source code.


###Difficulty of P.B.C.
Under P.B.C.s the approach of padding or truncating the grid to obtain a grid which satisfies the grid size constraints of the multigrid solver will of course affect the periodicity of the unit cell. These approaches therefore can not be used in periodic BCs and so solvation calculations in P.B.C.s are currently disabled. The future solution will either be to use a Fourier interpolation to transfer the charge density and permittivity from the fine grid used in the rest of CASTEP to the fine grid used for the multigrid calculation, or to alter `fine_grid_scale` in each direction to get an appropriately sized grid in CASTEP. The later approach is used in ONETEP.
