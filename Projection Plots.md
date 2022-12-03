
Tasks
- [ ] Test performance 
	- [ ] Slice plot
	- [ ] Voxel plot
- [ ] Connect to plotter

# Additional features to consider
- [ ] Cell/material masking
- [ ] Property-based images
- [ ] Proper path tracing with shadows, etc. by using surface normals
- [ ] Integrate colors as we go to avoid dynamic memory allocation?
- [ ] Common need for a lightweight particle class used to only traverse the geometry for both random ray and this
- [ ] could render with random ray with a "pixel quadrature"?
- [ ] Apply wireframe to pixel slice plots too?

# High-level description of the alg

#### Data structures:

3 dim vector with a set of track segments at the end

`TrackSegment` is a `struct` that stores:
  - id (material or cell ID)
  - length (length of segment)
  - surface at the end of the segment

#### Parallelism:

Each thread is responsible for determining the set of track segments along a row of the image

#### Algorithm:

As in the other image generation algorithms, a fake particle is created to to the tracking.

For each pixel, set the direction and traverse the geometry. It begins by assuming an intersection is found.

##### `intersection_found` loop

In the case that an intersection is found (the equivalent of a surface crossing in transport), a search for the particle's containing cell is performed. If a containing cell is found, the `inside_cell` branch is selected.

##### inside_cell

Is this is the first occurrance of the particle being inside a cell, then a new, zero-length `TrackSegment` is created with the surface crossing set to `-1`. (_This should probably happen befor the event loop).

Then a `distance_to_boundary` call is performed for the particle and a new `TrackSegment` is added to the image row. The particle is advanced to the next surface on all coordinate levels. (_We sould create a subroutine for that_). Other particle parameters are updated at the surface crossing (`surface`, `n_coord_last`, `n_coord`). A lattice crossing is performed if needed.

#### ! inside_cell

If the particle is not inside of a cell, a subroutine `advance_to_boundary_from_void` is called. (_This is likely the first branch to be traversed, so I'm thinking we should reverse the logic here for readability_).

###### `advance_to_boundary_from_void`

Here, we search for the minimum distance to intersection with any cell in the root universe. (_This is a little dicey because we're typically expecting that ray traces occur from inside a cell, so some of the logic may not be correct. I saw this in the "ray tracing in one weekend" renderer_). If the minimum distance is huge, it's assumed we didn't hit a surface and this is indicated by returning a surface value of `-1`. If a hit is found, the particle is advanced up to the surface plus a FP epsilon (_presumably to make the `find_cell` operation easier in the next step).

Back to the track intersection loop....

The `intersection_found` loop continues until an intersection is not found (_either in the first step b/c we miss the model entirely or after moving through the whole model).

### Pixel coloring

After eixting the `intersection_found` loop, a position is created to hold the current color (_We're using a position here, but we should probably alias it for clarity -- RGBColorf or something_). For each segment of the current pixel, the color is set by mixing the current color (_initialized as background white_) with the next track segment for the pixel based on the exponential of the length times the color for that domain ID.


### Wireframe coloring

Wireframes are added to the image by checking for changes in domain IDs. This is done for each horizonal row of pixels first. Then, when all threads are finished processing their vertical task, the vertical comparison is performed. (_I think this should be parallelized separately to simplify things_).

_`wireframe_initial` is an allocated data structure. Why not just use the image data?_








