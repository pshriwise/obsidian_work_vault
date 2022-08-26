#mesh #unstructured-mesh #ray-tracing 

## Installation Notes
  - Need to use `--enable-hdf5` flag to enable HDF5
	  - This is important to enable the 8.11 version of `exodusII` in libmesh and remove the character limit on block name length

## API Notes
  - Use `elem.node_id(i)` for connectivity instead of `elem.node_ref(i).id()`
	  - It seems some functions/methods will return temporary copies of objects and others will refer to global objects.


### Ray Tracing w/ the Octree

```cpp
struct Ray {

Ray(const Point& org, const Point& dir) : org(org), dir(dir) 
{
  dir_inv = {1.0 / dir(0), 1.0 / dir(1), 1.0 / dir(2)};
}
 
Point org;
Point dir;
Point dir_inv;
Real t_min {0.0};
Real t_max {-1.0};
Elem* hit_elem {nullptr};
};
```

At each node in the tree:

  1. check for containment
  2. check for intersection
  3. if intersected, add children
  4. intersect leaf
	  1. get sideset faces of leaf
	  2. intersect faces w/ ray
	  3. upate ray paramters if hit
  5. continue to next child in the stack

Future optimizations:
  - sort children by intersection distance
  - determine next child by which face of the box is hit

## Traversing the Octree
- 