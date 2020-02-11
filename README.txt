This is a case containing a rough setup for the single riblet system.  This is based on the damBreak tutorial case with the following modifications:

1. Replaced the geometry from the damBreak one with the solo riblet one.  All boundary conditions preserved.

2. Added dynamic meshing.  This requires 4 things to be done.
	1) Add "libs ("libdynamicRefine2D.so");" to the top of controlDict.
	2) Create a dynamicMeshDict file in the constant/ directory.
	3) Make sure the dynamicFVMesh method is "dynamicRefine2DFvMesh;".
	4) In the dynamicMeshDict, make sure everything is contained within a "dynamicRefine2DFvMeshCoeffs{};" dictionary/list thingy.

3. Changed the parallel decomposition method from simple to scotch in system/decomposeParDict.

4. Changed the constant/transportProperties file so that the phases represent a viscous liquid surrounded by water.  To avoid breaking anything, I didn't change the names of the phases yet.
   Therefore the phase currently denoted by "air" has properties of water, and phase denoted by "water" has properties of viscous fluid.  Currently honey. Also increased surface tension.

5. Added contact angle boundary conditions to the walls in 0/alpha.water.orig, instead of zeroGradient.

6. Changed the initial placement of the thicker fluid so that it's spread across the bottom of the system.  This is done in system/setFieldsDict.  The small gap left at the RHS of this setup cuased an
   odd jiggle to be observed when the case was run.  This has been saved in the 'jiggle.mp4' file.

7. Modified the boundary conditions such that lid-driven flow was included.  Several things were done here.
	1) Changed the atmosphere face type from patch -> wall in system/blockMeshDict. (This may not be necessary, and we might need to make it a patch later on
	so it doesn't stop biofilm leaving the system, but we can investigate later on.)

	2) Changed pressure boundary conditions from (walls) fixedFluxPressure; uniform 0; -> zeroGradient / (atmosphere) totalPressure; uniform 0; -> zeroGradient in 0/p_rgh.

	3) Changed velocity boundary conditions from (atmosphere) pressureInletOutletVelocity; uniform (0 0 0) -> fixedValue; uniform (2 0 0) in 0/U.

	4) Changed alpha.water boundary conditions from (atmosphere) inletOutlet; value 0; uniform 0; -> constantAlphaContactAngle; in 0/alpha.water.orig.

	5) IMPORTANT: as none of the pressure boundary conditions have a specified value now, we need to add pRefCell 0; pRefValue 0; to the PIMPLE section in
	system/fvSolution.

	6) IMPORTANT: This still resulted in an error.  This error was removed by changing the atmosphere pressure condition to fixedValue; uniform 0;

8. Removed the pRefCell stuff.  This was unnecessary as we'd now defined the atmosphere to have 0 pressure.

9. Changed the atmosphere back to an inletOutlet.
	1) Face changed back to patch in blockMeshDict.
	2) Velocity boundary condition changed back to pressureInletOutletVelocity, but with value uniform (4 0 0).
	3) alpha.water boundary condition changed back to inletOutlet type.  (Doing this removed those weird artifacts that were seen earlier.)
	4) Velocity changed to fixed value rather than pressureInletOutletVelocity, not sure if this makes any difference, can change it back after we have the other walls
	working as intended.

10. Changed the right wall to an outlet.  This was done similarly to the atmosphere.
	1) Changed the rightWall face type from wall -> patch in system/blockMeshDict.
	2) Changed alpha.water boundary condition to type inletOutlet, just like atmosphere, in 0/alpha.water.orig.
	3) Changed velocity boundary condition to pressureInletOutletVelocity (0 0 0).
	4) Changed pressure boundary condition to totalPressure; uniform 0; (NB, also did this to the atmosphere boundary condition.)
	5) NB This setup eventually causes the system to "run dry" (I think), because everything gets thrown away due to the velocity.  Need to make the left wall an inlet to account for this (I hope)

11. Changed the left wall to an inletOutlet (same as the top and right walls.)  Hopefully this will stop the timesteps becoming erroneously small.
	1) Changed leftWall faces to type patch.
	2) Changed pressure boundary condition from zeroGradient -> totalPressure; uniform 0;
	3) Changed velocity boundary condition from noSlip -> pressureInletOutletVelocity;
	4) Changed alpha.water boundary condition to inletoutlet; uniform 0;
	4) NB This has maybe worked.
