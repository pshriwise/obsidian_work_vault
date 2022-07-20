#ukaea #fusion #moose

Ran into the following error message when trying to build the `OpenMCApp`

```shell
Linking Library /home/pshriwise/opt/aurora/src/moose/framework/contrib/pcre/libpcre-opt.la...
libtool:   error: require no space between '-L' and '-lopenmc'
```

Ran into a linking error related to legacy parameter definitions, had to roll back MOOSE to 
commit `d785bb5f9c`

```shell
/home/pshriwise/opt/aurora/src/moose/framework/build/header_symlinks/MooseTypes.h:485:17: error: static assertion failed: defineLegacyParams is no longer supported as legacy input parameter construction is no longer supported; see mooseframework.org/newsletter/2021_11.html#legacy-input-parameter-deprecation for more information
  485 |   static_assert(false,                                                                             \
      |                 ^~~~~
/home/pshriwise/opt/aurora/src/moose/framework/build/header_symlinks/MooseTypes.h:485:17: note: in definition of macro ‘defineLegacyParams’
  485 |   static_assert(false,                                                                             \
      |                 ^~~~~

```

