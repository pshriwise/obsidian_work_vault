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

In Python:

```Python
openmc.lib.init()
for i in range(n_iterations):
    openmc.lib.run()
    openmc.lib.update_weight_windows();
    openmc.lib.reset()
openmc.lib.finalize()
```
