
Tasks
- [ ] Test performance 
	- [ ] Slice plot
	- [ ] Voxel plot
- [ ] Connect to plotter


High-level description of the alg

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

In the case that an intersection is found (the equivalent of a surface crossing in transport), a search for the particle's containing cell is performed. If a containing cell is found, the `inside_cell` branch is selected.

##### inside_cell

Is this is the first occurrance of the particle being inside a cell, then a new, zero-length `TrackSegment` is created with the surface crossing set to `-1`. (_This should probably happen befor the event loop).

Then a `distance_to_boundary` call is performed for the particle and a new `TrackSegment` is added to the image row. The particle is advanced to the next surface on all coordinate levels. (_We sould create a subroutine for that_). Other particle parameters are updated at the surface crossing ( )


