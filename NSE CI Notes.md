#nse #ci

## Setting up CI for a project on EGS Gitlab

There are 3 pieces to setting up CI:

- Repo CI configuration
- CI runner creation on the `ci1` machine
- The `.gitlab-ci.yml` file

## Creating a gitlab runner

Log in to `ci1.ne.anl.gov` and make sure you can run the following successfully

```bash
$ which gitlab-runnner
```

This is the command we'll be using to connect the runner on the CI machine with the repo on gitlab.

To do this we'll need 

1. The URL of the gitlab server (https://git-out.gss.anl.gov/)
2. A registration token from the repository. This can be found under `CI/CD` in the 

I'd recommend setting the registration token using an 

```bash
$ gitlab-runner
```

## Finding your repo's registration token

The registr

