#anl #cps

## 2022-11-28
Title: Parallel in Time simulations – An overview of applications of the Parareal algorithm
Presener: Debasmita Samaddar (University of Chicago)
https://events.cels.anl.gov/event/209/

#### Notes
Reason sims diverge w/ large coarse solvers: Physics is represented well enough on small time/domain scales by coarse solver, but if the chunks are too large the LOM physics start to break down and give the wrong answer
	  - There seems to be a need for fast scoping studies to determine $N_{fine}$ and $N_{coarse}$
	  - ELMs: Creating chunks based on repeated processes wasn't as effective as selecting chunks based on selecting chunks based on individual physical phenomenon -- granularity matters a lot here! This mostly has to do with number of iterations req'd to converge and not as much with introducing bias?
	  - Factors to consider when selecting time chunks
		  - Time-dependence of simulated physics (immutable)
		  - Accuracy of coarse solver
		  - Coarse solver that's accurate in time more important than in space?
		  - Can be quantified by exploring how the stability region is affected by applying the parareal algorithm