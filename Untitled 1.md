#fusion #openmc #dagmc

### Summary
Generally performing TBR/heating validation of previous work at UW - Madison on the FNSF model. It seems everyone started by doing this with MCNP and are now working on reproducing those results with OpenMC using DAGMC. Generally are using Jon's `cad_to_h5m` package for model conversion. (Concerned about model validity here)

Filipe
  - Maybe we can help narrow the descrepancies on TBR between MCNP and OpenMC calcs?
  - Are the volumes/surfaces coincident?
  - What was the error you saw when trying to run the DAGMC model from the DAGMC plugin in OpenMC?
  - Version of Cubit being used at UTK?
Marina
  - Using Cubit to convert CAD models
Son
  - using `stl_to_h5m` and `cad_to_h5m` to generate DAGMC models
  - suggestions on missing shared library in Jon's packages
  - `vtk` module installation problem for VTK file generation
Nick Meehan
  - 
