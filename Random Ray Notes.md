# Random Ray Notes
#research 

The question that often comes to mind about TRRM is "Isn't this a sort of low-fidelity Monte Carlo simulation?" and/or "Why not simply run a full Monte Carlo simulation?". While it's tempting to compare TRRM method directly to Monte Carlo, it's really better to look at's role in radiation transport simulations through two different lenses: 

  1. Monte Carlo vs. Tradiaional MOC
  2. Random Ray's advantages over 

## Monte Carlo vs. Traditional MOC

The comparison made here contains an answer to the second question above. Which is essentially, what is MOC's role in RT simulation?

John Tramm makes the following points to this effect:

  - **MOC is much faster. Monte Carlo isn’t too bad at converging global quantities like k-eff, but for converging the spatial power/flux distribution in a reactor, Monte Carlo is very very computationally expensive. MOC is a lot cheaper, on the scale of an order of magnitude or maybe two orders of magnitude.**
  - The main drawback is that MOC uses multigroup cross sections. There are a lot of different ways of generating accurate XS data though, and random ray is fast enough that it can use a lot of energy groups to mitigate those accuracy hits, so for a lot of cases it would probably be worth the extreme reductions in cost as compared to Monte Carlo. 
  - Having to rely on multigroup XS data is a problem that will reduce fidelity (or create a lot of challenges to get gowhich od XS data), which is why Monte Carlo can be preferable as it is a more general method.
  - Monte Carlo also is subject to some more serious correlation issues (e.g., particle clustering) that can greatly inhibit or prevent expected 1/sqrt(N) convergence. In practice this is not a major issue if the user is experienced, but it is still a thing to be aware of.

I'll throw in here that the goemetric discretization plays a role as well -- the use of FSRs inherently adds some spatial approximations to MOC simulations as well.

Alright, so the bottom line is that MOC is much faster and can provice good estimates of the flux distributions and globally integrated values (i.e. eigenvalue). So, knowing why MOC might be attractive for some purposes, what about Random Ray's role?

## Random Ray vs. Traditional MOC

Ok, now that we understand the role of MOC. Let's talk Random Ray. The bottom line here is that is essentially sovles a lot of problems MOC faces in terms of generality and allows this method to scale to full core problems. 

John's points, of which there are many:

  - While random ray requires more floating point operations than traditional MOC, it is still much faster than Monte Carlo, maintaining that 1-2 OM improvement. So the primary motiviation for using it vs. Monte Carlo remains.
  - Deterministic MOC MUST use non-linear acceleration schemes (like coarse mesh finite difference (CMFD)) to achieve acceptable performance on high dominance ratio full core simulation problems. No one has even tried to run a full core problem with standard power iteration MOC
  - Random ray works with just naive power iteration, no secondary acceleration scheme is required. This makes the code much simpler and avoids all the stability and user decisions that go into selecting a CMFD group structure, spatial homogenization scheme, stability constants, etc.
  - Random ray is able to do this because it can use very few random rays per iteration and still move the source distribution to a stationary state without biasing it.
  - Deterministic MOC on the other hand is stuck running O(1000) more rays per iteration than random ray in order to avoid bias when moving source to stationary. Using a sparser quadrature would result in bias, and given how power iteration works numerically, even just a modest global bias causes a ton of extra iterations to be used.
  - Long story short, random ray can converge full core problems in a very simple manner using only naive power iteration in equivalent or less runtime than a much more complex, harder to use, less stable, and less general MOC + CMFD code.  
  - Then, there are some other nice benefits. Random ray uses O(100-1000) less memory than deterministic MOC due to avoiding having to store boundary angular fluxes.
  - The stochastic quadrature in random ray avoids quadrature holes and other small biases that show up in deterministic quadratures, improving fidelity a bit. For detailed fine geometries, such as IFBA, MOC needs to be refined to an impractically fine quadrature level (50x finer than is typically run even on high-fidelity runs) to converge the solution. Random ray has a little more variance than usual on these problems but overall the increase is runtime is pretty minimal. That is, random ray is already faster on normal problems, but on more challenging problems like IFBA, the gap widens by another order of magnitude.


