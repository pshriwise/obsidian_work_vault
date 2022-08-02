#mesh #unstructured-mesh #ray-tracing 

## Installation Notes
  - Need to use `--enable-hdf5` flag to enable HDF5
	  - This is important to enable the 8.11 version of `exodusII` in libmesh and remove the character limit on block name length

## API Notes
  - Use `elem.node_id(i)` for connectivity instead of `elem.node_ref(i).id()`
	  - It seems some functions/methods will return temporary copies of objects and others will refer to global objects.


### Ray Tracing w/ the Octree

