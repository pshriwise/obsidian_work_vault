# Debugging
#debug

## MPI Programs

```shell
$ mpirun -np 2 xterm -e gdb <program_name>
```


## Loading a core file

Any program you wish to produce a `core` file should be compiled with the `-ggdb3` flag.

Remove the limit on the size of the core file with 

```shell
$ ulimit -c unlimited
```

A dumped `core` file from the program can then be opened in `gdb` with

```shell
$ gdb <program> <core_file>
```
