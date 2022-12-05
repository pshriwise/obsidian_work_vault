#ray-tracing 
# GPRT Notes



### Meeting w/ HACC team 2022-08-15
  - KNN / range for clustering algs
  - Visualization and rendering for HACC datasets
  - Extend Aaron Knoll's algorithms to RT hardware
  - "Friends of friends" -- simplest version of DBScan alg
	  - ARBRX DBScan paper
  - All particles w/in fixed distance (big win!!)
  - Issue w/ observer (ray coherence)
  - New space
	  - Fast, local voronoi tessellation algs
	  - Local Voronoi volume
	  - Point containment to KNN

## Meeting w/ Intel RT 2022-09-09

Relevant links for Embree implementation:

- https://github.com/Twinklebear/ChameleonRT/blob/embree-sbt/embree/render_embree.cpp#L183
- https://github.com/Twinklebear/ChameleonRT/blob/embree-sbt/embree/embree_utils.h#L163
- https://github.com/Twinklebear/ChameleonRT/blob/embree-sbt/embree/render_embree.ispc#L110
- https://github.com/Twinklebear/ChameleonRT-lang/blob/main/src/renderer/main.cpp
- https://github.com/Twinklebear/ChameleonRT-lang/tree/main/src/crtl/core/include/crtl
- https://github.com/Twinklebear/ChameleonRT/tree/hyperrender/kernels
- 