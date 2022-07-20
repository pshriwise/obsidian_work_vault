#openmc #gpu

# Faddeeva Function Performance and Evaluation

## On setting up the environment

```bash
$ module use /soft/restricted/CNDA/modulefiles
$ module load oneapi
```


Compilers:

  - `icx`: C compiler
  - `icpx`: C++ compiler  

Adding the MIT Implementation of the Faddeeva function:

I grabbed the following source files from the Ab Initio server at http://ab-initio.mit.edu/:

  - `Faddeeva.c`: includes the Faddeeva.cc file
  - `Faddeeva.cc`: function definitions and test program
  - `Faddeeva.h`: C header
  - `Faddeeva.hh`: C++ header  

This implementaiton can conveniently be compiled in either C or C++. If compiled in C, function names will appear as `Faddeeva_*`. If compiled in C++, the functions get added to a `Faddeeva` namespace and can be called using `Faddeeva::*`.

To call the appropriate function whem compiling in C or C++ I wrote a small wrapper to convert the C99 `double complex` and `std::complex` types to the `RSComplex` type used by `rsbench`.

```cpp
#ifdef __cplusplus

#include "Faddeeva.hh"

RSComplex mit_W( RSComplex z )
{
  std::complex<double>Z(z.r, z.i);
  std::complex<double> out = Faddeeva::w(Z, 0.0);
  z.r = out.real(); z.i = out.imag();
  return z;
}

#else

#include "Faddeeva.h"

RSComplex mit_W( RSComplex z )
{
  double complex Z = z.r + z.i * I;
  double complex out = Faddeeva_w(Z, 0.0);
  z.r = creal(out); z.i = cimag(out);
  return z;
}

#endif
```

These functions can compile sucessfully using both the C and C++ Intel compilers. When attempting to run, however, an offload error occurs. I started commenting branches and silo-ing the control flow to isolate the case of this. So far one issue I found was the `isnan` function. Here is it's definition in the C library:

```cpp
/* Return nonzero value if X is a NaN.  We could use `fpclassify' but
   we already have this functions `__isnan' and it is faster.  */
# ifdef __NO_LONG_DOUBLE_MATH
#  define isnan(x) \
     (sizeof (x) == sizeof (float) ? __isnanf (x) : __isnan (x))
# else
#  define isnan(x) \
     (sizeof (x) == sizeof (float)					      \
      ? __isnanf (x)							      \
      : sizeof (x) == sizeof (double)					      \
      ? __isnan (x) : __isnanl (x))
# endif
```


I created the following reproducer for this:

```c
#include <math.h>
#include <omp.h>
#include <stdio.h>
​
int main() {
​
  int offloaded_to_device = 0;
  double val = NAN;
  int was_nan = 0;
​
  #pragma omp target map(tofrom:offloaded_to_device) map(to:val) map(tofrom:was_nan)
  {
    was_nan = isnan(val);
    offloaded_to_device = !omp_is_initial_device();
  }
​
  if (was_nan) {
    printf("Value is NaN\n");
  }
​
  if (offloaded_to_device) {
    printf("Offload successful\n");
  } else {
    printf("Kernel ran on host\n);
  }
​
​
return 0;
}
```

which can be compiled with 

```bash
$ icx -std=gnu99 -Wall -fiopenmp -fopenmp-targets=spir64 -D__STRICT_ANSI__ -O3 main.c -o main
$ ./main
``` 
and it produces that same offload error.


## Creating a coverage database

We talked about this at the particle transport meeting a couple of weeks ago we all agreed it would be a good idea to examine the what portion/branches of the MIT Faddeeva function implementation are used in practice in Monte Carlo in addition to examining it's performance. It was determined that a good way to explore the breadth of the complex parameters passed into the Faddeeva function was to perform a set of infinite media problems for each nuclide in a given nuclear data set, with a separate simulation for each temperature available in the data evaluation.

John had the really good idea of representing the coverage as a heat map of the complex plane. The plane first needs to be broken up into different regions based on the branch conditions in the MIT implementation. The coverage results can then be used to provide weights for the heat map. This will provide valuable information in terms of what regions of the implementation are most-used and if any are not used at all, allowing for optimization of the function for Monte Carlo simulations. Regions of the implementation that are most often hit can be targeted for optimization efforts.

On thinking about this further, a lot of this work is going to boil down to a data management problem. I'd really like to be able to organize the data so it can be filtered and viewed by

  - nuclide,
  - temperature,
  - and dataset

to allow us to inspect which nuclides are perhaps causing us to use regions of the function that would otherwise go unused. If those nuclides are exceedingly rare, we may be able to alter the implementation to account for this and improve performance without loss of accuracy. Similarly, it would be interesting to see if there are regions of the implementation that change based on the dataset being used. A tool for visualizing combinations of this data with both the standard line-by-line report as well as the complex plane visualization descrbed above would be valuable contributions to the community. Ideally this project can exist separately from OpenMC in it's own repository, where the code used to examine the data will also be store. As a final product, it would be really nice to publish the final dataset and generate a website where this data can be interactively explored.

To do this properly, the data produced by Linux coverage tools will need to be reorganized into a more friendly format for these purposes. It's something I'll look into as I go.
