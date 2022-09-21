#openmc #ecp

## 2022-09-21
- Handling errors for the GPU version of OpenMC
- [ ] check on 2D tally results w/ tally branch

Error checking on GPU branch
  CUDA assert if on NVIDIA
  if not, openmp specialization to implement printf and/or other assertion
  

## 2022-09-07
- tallies on device are working, about a 35% slowdown inactive vs. active
	- 6M tally bins for depletion
- additional depletion XS data loaded does slow us down a bit
- SHIFT was about 50% slower inactive vs. active

Next steps:
  - FY problems should be coupled
  - need to compile ENRICO w/ NekRS
  - mi100 runs have been broken for some time now