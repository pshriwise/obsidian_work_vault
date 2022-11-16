#monte-carlo #celeritas #hep

## A. Lund Presentation -- 2022-09-02
- Macro-decorated, header-only inlined C++ code for kernels
- Composition-based objects -- do we need to talk about a similar transition in OpenMC?
- MIMD --> event-based; SIMD --> history-based
- Secondary particle stack allocator to manage buffer
	- underlying secondary limit is fixed
	- secondary particle allocation is managed in the underlying buffer by the stack class
	- secondary tracks are sorted by thread ID
- RNG
	- unique RNG state for each thread
	- no calls to RNG before allocating storage for secondaries (must know max N of secondaries a priori)
- No spline interpolation with current interpolation approach!!
