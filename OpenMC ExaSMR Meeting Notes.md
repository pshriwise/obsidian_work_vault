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

### 2022-12-14

#### `sunspot`: a pre-aurora cluster at ANL
 - For sunspot access (pre-aurora cluster): Haritha Siddabathuni Som (haritha@anl.gov)
 - `sunspot` module system is similar to JLSE for changing between compilers
 - `sunspot` limits how many resources are available on each node, which is why performance results there are a little slower than expected for a fully-enabled aurora node
 - `sunspot` there are also some issues with pinning MPI procs to certain cores/regions of the node
 - Performance:
	 - 750k pps (down from an expected 850k pps -- adjusted for the limited resources available on these nodes)
	 - seems to be some small change that's caused this
	 - tried rolling back, but there's been changes in the way we handle sorting on Intel