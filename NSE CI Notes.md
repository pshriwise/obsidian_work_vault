#nse #ci

## Setting up CI for a project on EGS Gitlab

There are 3 pieces to setting up CI:
  - Repo CI configuration
  - CI runner creation on the `ci1` machine
  - The `.gitlab-ci.yml` file

## Creating a gitlab runner

Log in to `ci1.ne.anl.gov` and make sure you can run the following successfully.

```bash
$ which gitlab-runnner
```

This is the command we'll be using to connect the runner on the CI machine with the repo on gitlab. If 

To do this we'll need 

1. The URL of the gitlab server (https://git-out.gss.anl.gov/)
2. A registration token from the repository. This can be found under `CI/CD` in the repo settings
![[Pasted image 20220824135221.png]]

I'd recommend creating an environment variable for the registration token in case the gitlab runner creation takes more than one try.

```bash
$ export REGISTRATION_TOKEN=<gitlab_repo_token>
```

To start the registration process, enter

```bash
$ gitlab-runner register --url https://git-out.gss.anl.gov --registration-token $REGISTRATION_TOKEN
```

This command is interactive and will ask for a series of fields. The first two you have already provided on the command line, so the default value (shown in brackets) can be left as-is.

The command will request the following pieces of information as well:

- Description: A name for the runner in the `gitlab-runner` system on the CI machine
- Tags: These are fairly important. They are how we'll identify the runner to use for CI in the `.gitlab-ci.yml` file.
- Maintenance Note: Not super important. You can set it to whatever you like.
- Executor: This indicates what environment the runner will use. The most common is shell, meaning that the initial test environment will be the same as a standard ssh into the CI machine. You can also tell the runner to execute everything inside a Docker container or ssh into another machine to run the tests.

If you specified "shell" for the executor, that's it for this step! The `CI/CD` page in the repo should now show the newly-registered runner.

![[Pasted image 20220824135249.png]]

Note the warining sign to the left of the runner ID. Now that the runner is registered, we need to start the runner on the CI machine. We can do this by running

```bash

$ gitlab-runner run ci-test
```

Be sure to use your ci descriptor here instead of `ci-test` if needed. 

Once that command is complete. A green status should appear next to the runner in the repo.

![[Pasted image 20220824141126.png]]


## Keeping the runner going after logging out

Once you've checked that all of this works, we'll want to get the runner setup to continue after logging out of the machine.

If the runner is still going from the previous steps, we'll want to shut down the runner (w/ `ctrl-c`).

Then we'll open a `screen` instance and start the runner there:

```bash
# screen will open a new prompt
$ screen
# start the runner just like we did last time
$ gitlab-runner run ci-test
```

To detatch from this `screen` instance, use `ctrl-a, d`. To reattach to this instance in the future, use `screen -r`. This shell will keep running even after logging out of the machine.

## Creating a `.gitlab-ci.yml` file

This file should be added to the top directory of the repository and contains the different steps for setup and build of the project as well as indicators for which runners the CI run should use. These indicators are provided in the file in the "default" section. The execution of different steps is indicated in the "stages". The following is a simple with two stages.

```sh
default:
  tags:
    - ci-test

stages:
  - build
  - test

build:
  stage: build
  script:
	 - echo "Building"

test:
  stage: test
  script:
    - echo "Testing"
    
```
For more options on how to run scripts and manage the CI environment, the documentation on this file can be found [here](https://docs.gitlab.com/ee/ci/yaml/gitlab_ci_yaml.html)
