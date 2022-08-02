1. Clone Cardinal and pull submodules
```shell
$ git clone https://github.com/neams-th-coe/cardinal
$ cd cardinal
$ ./scripts/get-dependencies.sh
```
3. Set compiler environment variables
```shell
export CC=mpicc
export CXX=mpicxx
export FC=mpif90
export MPICH_FC=gfortran
```
  - `CC=mpicc`
  - `CXX=mpicxx`
  - `FC=mpif90`
5. Build Petsc inside Cardinal
```shell
$ cd cardinal
$ 
```
6. 