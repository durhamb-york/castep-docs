#Forces and Stresses in Open Boundary Conditions

##Forces
By default CASTEP will calculate forces at the end of the ground state calculation. The force terms due to implicit solvent will be automatically calculated and included. The formulas employed are exact (to numerical accuracy) when a self-consistently updated cavity is used. For the case of a fixed cavity, they are approximate. The approximation is very good, but initial tests suggest that you might not be able to converge geometries to typical thresholds -- although the noise in the forces will be small, it might be enough close to equilibrium to throw off the geometry optimiser. Keep this in mind. 

Smeared-ion forces in vacuum are also implemented. These are numerically exact and practically negligible. 

You should be able to do Geometry Optimisation (GO) and Molecular Dynamics (MD) without any problems with implicit solvent, provided that you using a self-consistent cavity. If using a fixed cavity the cavity will not be updated after each geometry or MD step, therefore calculating geometry's will be specific to the initial cavity, which depending on how far the geometry is from the ground state will vary the quality of the approximation. MD calculations will suffer from similar problems. Restarting these calculations with with no might be tricky if they are interrupted during the in-solvent stage -- you will need to ensure the correct restart files (the vacuum restart files) are used to generate the solvent cavity upon restart.

##Stress
Calculation of the stress is disabled for O.B.C.s calculations and so Geometry Optimisation and Molecular Dynamics calculations **should be done using a fixed cell**.
