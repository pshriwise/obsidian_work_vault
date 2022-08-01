#fusion #multiphysics #ukaea #moose

## 2022-08-01


Helen:
  - Updates to MOOSE
  - Should be releasing a newly tagged version of Aurora soon
  - DAGMC model has to be in `cm` for Aurora simulations
April/Aya:
  - Updates to DAGMC pincell model for Aurora
  - OpenMC always as to be sub-app in Aurora
	  - causes OpenMC to run for every timestep for apps "above" it
  - Will Aurora add more materials to the updated DAGMC model after it's property discretization?
	  - All cells need to be coupled?
	  - List of assumptions made in coupling process
	  - Exercise in documenting assumptions
	  - Expects one-to-one mapping of material regions in OpenMC/MOOSE model
	  - Current issue:
		  - 
	- Longer-term design questions
		- How to suporrt more general transfers?
		- 
	- MHD model update

My action items:
  - [ ] Rebuild Aurora and Cardinal with the Petsc hdf5 installation
  - [ ] Cardinal PR review
  - [ ] Figure out how to share and collab on notes

### 2022-08-15
