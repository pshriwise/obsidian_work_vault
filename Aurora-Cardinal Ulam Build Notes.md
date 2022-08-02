1. Clone Cardinal and pull submodules
```shell
$ git clone https://github.com/neams-th-coe/cardinal
$ cd cardinal
$ ./scripts/get-dependencies.sh
```
2. Set compiler environment variables
```shell
export CC=mpicc
export CXX=mpicxx
export FC=mpif90
export MPICH_FC=gfortran
```
3. Build Petsc inside Cardinal
```shell
$ cd contrib/moose
$ ./scripts/update_and_build_petsc.sh
```
4. Set the `HDF5_ROOT` env var
```shell
$ export HDF5_ROOT=$HOME/aurora_build/cardinal/contrib/moose/petsc/arch-moose
```
5. Build libmesh
```shell
$ ./scripts/update_and_build_libmesh.sh
```
6. Build Cardinal
```shell
$ export MOOSE_DIR=$HOME/aurora_build/cardinal/contrib/moose
$ export PETSC_DIR=$HOME/aurora_build/cardinal/contrib/moost/petsc/
$ export NEKRS_HOME=$HOME/aurora_build/cardinal/install
$ cd ../../
$ make -j10
$ ./run_tests
```
7. Clone Aurora
```shell
$ cd ../
$ git clone https://github.com/aurora-multiphysics/aurora
```
8. Create installation directory for dependencies
```shell
$ mkdir opt
```
9. Build Aurora dependencies, checking that HDF5 is found in the right location for MOAB and OpenMC
10. I did have to set my `LD_LIBRARY_PATH` so that the MOAB libraries could find the HDF5 libraries, but that shouldn't be necessary. I'm honestly not sure what's going on there.
```shell
$ export LD_LIBRARY_PATH=$HDF5_ROOT/lib:$LD_LIBRARY_PATH
```
12. Build OpenMC as described in Aurora Documentation (we might be able to use the version from Cardinal, but I wasn't brave enough to try that yet)
```shell
$ export PATH=$HOME/aurora_build/opt/bin/:$PATH
```
13. Skip step for building MOOSE
14. Build Aurora

Something I got burned by: Make sure thre isn't a trailing `/` in your `PATH` variable for the dependency installations.

