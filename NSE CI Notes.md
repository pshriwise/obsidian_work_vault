#nse #ci

## Setting up CI for a project on EGS Gitlab

There are 3 pieces to setting up CI:

- Repo CI configuration
- CI runner creation on the `ci1` machine
- The `.gitlab-ci.yml` file

## Creating a gitlab runner

Log in to `ci1.ne.anl.gov` and make sure you can run

```bash
$ which gitlab-runnner
```

## Repo CI Configuration

First the repo's CI needs to be activated and a runner added.

These options can be found under `settings -> CI/CD` in the repo. 

