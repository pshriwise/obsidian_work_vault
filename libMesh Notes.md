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
Using `TreeBase::find_element` as a guide here. This algorithm uses a root tree node to start the search for an element containing the search point.

First, a containment check is performed on the root node:

```cpp
template <unsigned int N>
const Elem *
TreeNode<N>::find_element (const Point & p,
                           const std::set<subdomain_id_type> * allowed_subdomains,
                           Real relative_tol) const
{
  if (this->active())
    {
      // Only check our children if the point is in our bounding box
      // or if the node contains infinite elements
      if (this->bounds_point(p, relative_tol) || this->contains_ifems)
        // Search the active elements in the active TreeNode.
        for (const auto & elem : elements)
          if (!allowed_subdomains || allowed_subdomains->count(elem->subdomain_id()))
            if (elem->active() && elem->contains_point(p, relative_tol))
              return elem;

      // The point was not found in any element
      return nullptr;
    }
  else
    return this->find_element_in_children(p,allowed_subdomains,
                                          relative_tol);
}
```

Then a recursive search is performed using `TreeBase::find_element_in_children`. This is where the traversal occurs.

```cpp
template <unsigned int N>
const Elem * TreeNode<N>::find_element_in_children (const Point & p,
                                                    const std::set<subdomain_id_type> * allowed_subdomains,
                                                    Real relative_tol) const
{
  libmesh_assert (!this->active());

  // value-initialization sets all array members to false
  auto searched_child = std::array<bool, N>();

  // First only look in the children whose bounding box
  // contain the point p.
  for (auto c : index_range(children))
    if (children[c]->bounds_point(p, relative_tol))
      {
        const Elem * e =
          children[c]->find_element(p,allowed_subdomains,
                                    relative_tol);

        if (e != nullptr)
          return e;

        // If we get here then a child that bounds the
        // point does not have any elements that contain
        // the point.  So, we will search all our children.
        // However, we have already searched child c so there
        // is no use searching her again.
        searched_child[c] = true;
      }


  // If we get here then our child whose bounding box
  // was searched and did not find any elements containing
  // the point p.  So, let's look at the other children
  // but exclude the one we have already searched.
  for (auto c : index_range(children))
    if (!searched_child[c])
      {
        const Elem * e =
          children[c]->find_element(p,allowed_subdomains,
                                    relative_tol);

        if (e != nullptr)
          return e;
      }

  // If we get here we have searched all our children.
  // Since this process was started at the root node then
  // we have searched all the elements in the tree without
  // success.  So, we should return nullptr since at this point
  // _no_ elements in the tree claim to contain point p.
  return nullptr;
}
```