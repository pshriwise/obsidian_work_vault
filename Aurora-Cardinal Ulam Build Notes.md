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
$ export NEKRS_HOME=$HOME/aurora_build/cardinal/install
$ cd ../../
$ make -j10
```
7. Clone Aurora
```shell
$ cd ../
$ git clone https://github.com/aurora-multiphysics-aurora
```
8. 