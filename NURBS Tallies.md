#openmc #nurbs
## Background and Motivation
See notes in [[Functional Expansion Tally Notes]]

While FETs are great for providing a continuous tally representation over some region of phase space, they are typically very limited in that the analyst must have some knowledge of the function's shape and limits *a priori*. Sharp gradients in the polynomial caused by material boundaries or general forms that are not well-represented by the polynomial type can case large inaccuracies.

Non-rational uniform b-splines (NURBs) are a class of recursively-defined functions that can take on an arbitrary shape.


  - Find Python package for representing and plotting NURBs
  - Use particle track capability to manually set tally values
	  - Determine range of influence for node. If the particle is outside of this range, disregard.
	  - Weight NURBs nodes based on particle distance from node
	  - Weight NURBs nodes based on % of particle distance from node
  - Use gradients in NURB to refine the curve