#opennmc

### Development Task List
- [ ] Refactor interpolation code
- [ ] Add remaining interpolation schemes to `EnergyFunctionFilter`


### `EnergyFunctionFiler` Interpolation

### Interpolation Refactor
Interpolation is used in many places in OpenMC. The formulat for linear interpolation appears many times. Something like:

```cpp
double f = (x - x0) / (x1 - x0);
double val = y0 + f * (y1 - y0);
```

I'd like to refactor this to minimize duplication of code. One challenge with this is that the interpolation factor, `f`, is often computed and used for multiple interpolations (for computing cross section values at multiple temperatures, for example).

```cpp
double f = (x - x0) / (x1 - x0);
double val1 = ya0 + f * (ya1 - ya0);
double val2 = yb0 + f * (yb1 - yb0);
...
```

So a function that performs the entire interpolation isn't paticularly useful as we'd be recomputing this interpolation factor many times. I think I'd like to try this with some kind of templated structure that is designed to be temporary.

```cpp
struct Interpolator<T> {

	// 
	Interpolator(xs, x);

	
	double interp
}
```

> This PR adds the log-log interpolation scheme to the  `EnergyFunctionFilter` class to support commonly used dose rate evaluations 
  Closes #1671.
  This PR was sponsored by our friends at First Light Fusion 💯 
![image](https://user-images.githubusercontent.com/4563941/182013014-94f0f348-9edc-4890-b1f1-3a275954c073.png)


#### Plan for adding CubicSpline interpolations in OpenMC

Cubic spline interpolation requires a transformation of the input data (x and y values of the interpolated function) to the set of coefficients used to represent the continuous cubic function spanning the points. This requires $4(n-1)$ constraining equations and, more importantly, a matrix solver to get those coefficients. Since we'd like to avoid adding a Matrix solver to OpenMC, my plan is to use the `scipy.interpolate` module to generate a `CubicSpline` class that contains the coefficients. These will be written to XML and read into the C++ code to be used for interpolation during simulation.

Update: it turns out that there are already several functions in OpenMC that provide cubic spline evaluation and interpolation of datasets. These are needed for the thick-target Bremsstrahlung evaluation in electron transport. Assuming that the correct interpolation is used here, I'll leverage those functions to generate the correct 2nd order derivatives at each data point that will allow for fast interpolation of the `EnergyFunctionFilter` values during tallying.

#### On the ICRP interpolation type

The [USER GUIDE TO THE ICRP CD AND THE DECDATA SOFTWARE](https://journals.sagepub.com/doi/10.1016/j.icrp.2008.10.001?icid=int.sj-abstract.similar-articles.2) indicates that a hermite cubic interpolation is used for some datasets. 