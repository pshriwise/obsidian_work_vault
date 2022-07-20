## OpenMC as MOOSE: MOOSE Ray Tracing + OpenMC Physics Kernels

#unstructured-mesh #ray-tracing #openmc

Collaborations:
  - Intersection code into libMESH
  - Changing some OpenMC namespaces into object
  - Break out parallel work into separate library
    - Next --> parallel work neighbor-to-neighbor


Suggestions:
  - Extra element IDs: add IDs to elements "extra_element_integers"
  - Will persist through file I/O
  - Can define new groups of elements this way ( talk to Andy about Aurora)
  - "backup format"/XDR/XDA (ASCII) into statepoint file to maintain metadata

## MOOSE Ray Tracing Notes

Nomenclature:
  -  "element side": face of an element
  -  "element node": vertex
  -  "element extrema": edge or vertex of an element
  -  "study": object which manages rays and directs them to be traced by `TraceRay` objects.

File notes:
  - `TraceRayTools.h`: Contains logic for determining what element a ray will enter if intersecting an extrema. Also contains the following list of supported element types:
    - HEX8
    - HEX20
    - HEX27
    - QUAD4
    - QUAD8
    - QUAD9
    - TET4
    - TET10
    - TRI3
    - TRI6
    - EDGE2
    - EDGE3
    - EDGE4
    - PYRAMID5
    - PYRAMID13
    - PYRAMID14
    - PRISM6
    - PRISM15
    - PRISM18
  
  - `ElemExtrema.h`: Structure used to classify an element extrema type (edge or vertex/node)
  - `TraceRay.h`: Executes ray tracing on a mesh. Has a ray as an attribute to operate on and update. Contains the following intersection types:
    - `TraceRayResult`
      - INTERSECTIONS
      - FACE_HITS
      - VERTEX_HITS
      - EDGE_HITS
      - MOVED_THROUGH_NEIGHBORS
      - BACKFACE_CULLING_SUCCESSES
      - BACKFACE_CULLING_FAILURES
      - INTERSECTION_CALLS
      - VERTEX_NEIGHBOR_BUILDS
      - VERTEX_NEIGHBOR_LOOKUPS
      - EDGE_NEIGHBOR_BUILDS
      - EDGE_NEIGHBOR_LOOKUPS
      - POINT_NEIGHBOR_LOOKUPS
      - FAILED_TRACES
  - Also contains the following element exit types:
    - NO_EXIT
    - HIT_FACE
    - HIT_VERTEX
    - HIT_EDGE

  - `ParallelRayStudy.h`: Used to manage rays and runs them on the correct proc ID. Also handles threading by storing a vector of instances of `TraceRay` (i.e. MOOSE "threading").
  - `RayTracingCommon.h`: Contains a `MAX_POINT_NEIGHBORS` parameter that is interesting.
  - `Ray.h`: MOOSE's ray class
    - Requires a lot of setup in a particular order
      1. `Ray::setStart`
      2. `Ray::setDirection`
      3. `Ray::setStartingEndPoint`
      4. `Ray::setStartingMaxDistance`
    - State information:
      - `Ray::_current_elem`:  element the ray is current in and will ray trace against
      - `Ray::_current_incoming_side`: Current side the ray is entering on (if any)
      - `Ray::_end_set`: Whether or not the user has an end set on the ray
      - various counters for the number of intersections, times passed between ranks, direction changes,
      - `Ray::_distance`: total distance the ray has traveled
      - `Ray::_max_distance`: distance the ray is allowed to travel
      - `Ray::_should_continue`: whether or not the ray should continue to be traced
      - `Ray::_data`/`Ray::_aux_data`: data that is carried with the ray (more on this below)
      - `Ray::_study`: the ray tracing study that owns this ray
      - `Ray::_extra_padding`: "to avoid false sharing"
    

    Ray data is either a `libMesh::Real` or a `float`. Each ray has a vector of this information that gets updated as the ray is traced. It is essentially the ray's output data.

    ### Starting at the top

    From the notes above, the `ParallelRayStudy` executes a set of ray traces. The `TraceRay` class executes the underlying ray trace. 


