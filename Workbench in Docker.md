#nse #pyarc 
# Running Workbench in Docker (Ubuntu 20.04)

Something to bring up with the Workbench dev team is that the `Workbench-Linux.zip` distribution only seems to work with specific Linux distributions, namely `centos7`. That left me with just a few options for setting up a development environment 

  1. Work on the project remotely on NSE machines
    
    If this weren't a project involving a GUI, this might be nice because I wouldn't have to worry about security as much, but I don't want to deal with the lag when interacting with the GUI or w/ loading large images onto my machine from theirs.

  2. Change to CentOS on my machine or add an installation alongside my Windows/Ubuntu installs.

    Nope.
  
  3. Use a Docker container with CentOS7 and figure out how to run Workbench from there.

    This ended up being what I went with. It wasn't terribly difficult. The main challenge was getting any X11 application to run from the Docker container. Each Host kernel-Docker kernel seems to have it's own quirks for how to get it running.

## Setting up the Docker image

First, I pulled the latest [CentOS7 docker image](https://hub.docker.com/_/centos?tab=tags&page=1&ordering=last_updated) from DockerHub


``` shell
$ docker pull centos:7
```

Next, I logged in and did my typicall development package installs: `git` and then my personal shell config.

```shell
$ yum install -y git
$ git clone https://github.com/pshriwise/shell_config ~/.bash
```

## X11 Application Dependenies and Setup

Next up was the set of missing dependencies to start the GUI:

  - mesa-libGL
  - mesa-libGL-devel
  - libGLU
  - libXt
  - libXext
  - libXrender
  - xorg-x11-apps

I then installed `xterm`  as a test app just to try it out. 

Running X11 applications in a Docker container is always tricky. Here's what I found to work for my Ubuntu 20.04 installation.

  1. Run `xhost +` from the host side in Ubuntu. This should allow external applicaitons to use the X11 server.

  ```shell
  $ xhost +
  ```

  2. A few environment variables and files need to be provided to the Docker image for it to run successfully. The following command should capture them all. (The appropriate `docker_image_id` shoud be replaced.)

 ``` shell
 $ docker run -it --net=host -e DISPLAY=$DISPLAY -v 
 "$HOME/.Xauthority:/root/.Xauthority/:rw" -v "$HOME/.Xauthority-
 n:/root/.Xauthority-n/:rw" -v "/tmp/.X11-unix:/tmp/.X11-unix:rw" --hostname 
 $(hostname) <docker_image_id> /bin/bash
 ```

## Workbench Installation

Then it was time to grab Workbench from the public xgitlab server:

    $ wget https://code.ornl.gov/neams-workbench/downloads/-/blob/master/Workbench-Linux.zip


Then came the list of missing dependencies for the `Worknbench` binary. The one that I didn't see show up (and the one that prompted me to go to these lengths to avoid trying to make it compatible with my native Linux install) was the `libstdc++.so.6: version `CXXABI_1.3.9' not found` error. The incompatibility with the version of the C++ standard library on my machine was a non-starter for me.

  - fontconfig
  - fontconfig-devel
  - freetype
  - freetype-devel
  - unzip 
  - tar
  - libSM
  - libpng-devel
  - libpng12-devel

The final piece was to set an environment variable for QT application compatibility between the Docker image and my Ubuntu installation.

```shell
$ export QT_X11_NO_MITSHM=1
```

*Note: this might not be necessary for all Ubuntu/CentOS combinations.*
