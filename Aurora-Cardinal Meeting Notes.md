#fusion #multiphysics #ukaea #moose

## 2022-08-01


Helen:
  - Updates to MOOSE
  - Should be releasing a newly tagged version of Aurora soon
April/Aya:
  - Updates to DAGMC pincell model for Aurora
  - OpenMC always as to be sub-app in Aurora
	  - causes OpenMC to run for every timestep for apps "above" it
  - Will Aurora add more materials to the updated DAGMC model after it's property discretization?
	  - All cells need to be coupled?
	  - List of assumptions made in coupling process
	  - Exercise in documenting assumptions
	  - Expects one-to-one mapping of material regions in OpenMC/MOOSE model
	  - Some naming assumptions in place??
		  - Can be named differently: need to specify 
  - MHD model update
	  - 