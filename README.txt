This is a case containing a rough setup for the single riblet system.  This is based on the damBreak tutorial case with the following modifications:

1. Replaced the geometry from the damBreak one with the solo riblet one.  All boundary conditions preserved.

2. Added dynamic meshing.  This requires 4 things to be done.  
	1) Add "libs ("libdynamicRefine2D.so");" to the top of controlDict.  
	2) Create a dynamicMeshDict file in the constant/ directory. 
	3) Make sure the dynamicFVMesh method is "dynamicRefine2DFvMesh;".
	4) In the dynamicMeshDict, make sure everything is contained within a "dynamicRefine2DFvMeshCoeffs{};" dictionary/list thingy.

3. Changed the parallel decomposition method from simple to scotch in system/decomposeParDict.
