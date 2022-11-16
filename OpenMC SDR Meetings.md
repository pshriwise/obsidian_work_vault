#openmc #fusion #fes

## 2022-05-31
  - Project name
  - May report (Ethan to send report)
  - [PR #2005](https://github.com/openmc-dev/openmc/pull/2005) (merged)
  - Check boundary conditions of demo model

## 2022-07-25
  - Need support for testing/benchmarking
  - Specific activity for material sources
  - Update source distribution references
  - June report content:
	  - R2S toy problem demo
	  - FNG model
		  - cell-based 
		  - mesh-based
  - Timeline: milestone #4

## 2022-08-09
  - FNG dose model error
	  - compute R2S values both with and without the model error
  - Alex Valentine
	  - author of benchmarking codes for fusion applications
	  - they will provide an updated source for FNG
  - OpenMC source distribution refactor
- Discussion on control of when particles should check for an updated WW
  - collisions/virtual collisions
  - surface crossings
  - limited to pseudo-cells in MCNP
	- N MFP's
      - mitigate long history problem by forcing rouletting to happen more often
    - mesh crossings
	- control splitting vs. rouletting?
	- Look through MCNP manual and pick ones that are useful

## 2022-08-23
  - Continued suport for Cardinal-Aurora coupling via FES supplemental funds?

## 2022-09-06
- Coreform Cubit supplemental funding
	- abandon, not in political interests, suggest to Matt Sederberg that we re-apply under SBIR

## 2022-09-20
- Fusion neutronics meeting in Jan
- SOFE 2023 - Oxford, UK July 9-13
- ExaSMR meeting in Cambridge Dec. 1-2
	- Afternoon of Dec. 2

## 2022-10-04

FES Milestone FOA
  - Multiphysics whitepaper?
  - CFS not particularly interested in neutronics work
  - Mostly experimental work

## 2022-10-18

SciDAC -- multiphysics simulation of whole-facility. Need to define 
Software infrastructure -- OpenMC GPU version into main?


## Weight Window Generation Development

### Motivation for WW generation in C++
Many external applications are going to want to be able to apply weight window generation via the C++ API rather than control from the Python side.

Key capabilities in C++:
 - ability to translate tally data into weight window values
 - determine convergence of weight windows

With these capabilities in C++, any application can easily iterate OpenMC simulations and update the weight window values:

In C:

```c
openmc_init();
for (int i = 0; i < n_iterations; i++) {
  openmc_run();
  openmc_update_weight_windows(...);
  openmc_reset();
}
openmc_finalize();
```

In Python (`openmc-ww-gen`):

```Python
openmc.lib.init()
for i in range(n_iterations):
    openmc.lib.run()
    openmc.lib.update_weight_windows()
    openmc.lib.reset()
openmc.lib.finalize()
```

## The `openmc_update_weight_windows` function

Proposed signature:

```C
/**
Update the weight windows using data from the specified tally. The tally is required to have a MeshFilter applied. An EnergyFilter may also optionally be present.

\param tally_id ID of the tally to extract information from
\param ww_id ID of the set of weight windows to update. If null, a new weight winow class will be created.
\param score The score on the tally to use
\param value One of ('mean', 'rel_err'). Used to specify the type of tally information used when computing weight windows.
\param method The method used for computing weight windows (TBD)
*/
int openmc_update_weight_windows(int32_t tally_id,
	    						 int32_t* ww_id,
		    					 const char* score = "flux",
			    				 const char* value = "mean",
				    			 const char* method);
```

Add `openmc_weight_window_init(int32_t mesh_id, double* e_bounds)`

`WeightWindows::WeightWindows(int32_t mesh_id, std::vector<double> e_bounds) {...}`


Could break this up into a set of smaller functions:

```c
openmc_generate_weight_windows(int32_t tally_id, 
							   const char* score = "flux",
							   const char* value = "mean",
							   const char* method = "ratio")
```

```c
openmc_set_weight_windows(int32_t ww_id,
						  const double* lower_bounds,
						  const double* upper_bounds)
```

And on the C++ side (with r-value refs to avoid copying large data arrays)
```c++
WeightWindows::set_weight_windows(const std::vector<double>&& lower_bounds,
								  const std::vector<double>&& upper_bounds) {
	lower_ww_ = std::move(lower_bounds);
	upper_ww_ = std::move(upper_bounds);
}
```


OpenMC Dev Meeting 2022
  - Identify a set of tasks that would exercise the current API well, asssign teams to peform those workflows, and report back with proposed API changes
  - Cleanup of Statepoint object -- proxy
	  - Only claims file handle when reading data, checks timestamp hash for validity of statepoint file
	  - What to do about `summary.h5`?

## 2022-11-08

  - Topics for next time
	  - MHD LiPb transients in ITER-like devices or higher B fields (HTSC?)
	  - Magnet quenching - feedback effects and parametric studies

## 2022-11-16

  - Discussed milestones -- so far so good. Things need polishing, but we're on track
  - C/C++ testing platform.. Need to pick a platform and have students build out tests for 
