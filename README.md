# qt-build

[![Build Status](https://travis-ci.com/darkmattercoder/qt-build.svg?branch=deploy)](https://travis-ci.com/darkmattercoder/qt-build)

A (nearly) full qt build environment docker. Using multi-stage build to decouple build from actual image.
Can build a `qmake` project with one single command.

Find it also on [https://hub.docker.com/r/darkmattercoder/qt-build](dockerhub).

## Usage (short)

Download `qt-build` from dockerhub:

	docker pull darkmattercoder/qt-build:latest

Provide your qmake project that you want to build in a separate directory and build it with `qt-build`:

	docker run --rm -u $UID -v /path/to/your/project/directory:/var/build darkmattercoder/qt:build:latest build

You will find your built project in a new sub directory named `build` in your project directory. You can safely omit the `-u $UID` part in most environments, where your user has the standard `UID=1000`.

## Usage (detailed)

The `latest`-tag will give you the latest released `qt` version for your build environment. For different versions available, refer to the [available tags](#available-tags)-section.

Generally you will find full version tags like `5.11.3` as well as minor version tags that refer to the last patch release for the given minor release like `5.11` or `5.12`. There will be some tags listed that are named like `builder-x.y.z`, where `x,y,z` represent qt version numbers. Those images are used for ci builds of the images and contain the whole qt source overhead. They are used as caches in the ci environment. You normally do not want to use them for any production stuff, because they are fairly large.

The `build` command for docker run will build any qmake project that is mounted to `/var/build`. However, you can get an interactive session as well for manual adjustments or examinations:

	docker run --rm -it -u $UID -v /path/to/your/project/directory:/var/build darkmattercoder/qt:build:latest bash

If you pass additional arguments to `build` they will be taken into account as arguments to `qmake` which allows you to modify your build if needed.

## Building it yourself

	git clone https://github.com/darkmattercoder/qt-build.git
	cd qt-build
	docker build --build-arg QT_VERSION_MAJOR=X QT_VERSION_MINOR=Y QT_VERSION_PATCH=Z --build-arg CORE_COUNT=N --target=qt -t qt-build:X.Y.Z

Replace `X,Y,Z` according to your desired qt version. You have to provide a build configuration as a very simple `configure`-script in the `buildconfig` directory to make the build succeed. The script has to be named after your desired `QT` version, e.g. `configure-5.11.3.sh`. Example content:

	#!/bin/sh
	../configure -prefix $QT_PREFIX -opensource -confirm-license -nomake examples -nomake tests

### Build arguments

The build arguments can entirely be omitted, resulting in a build with some default values from the `Dockerfile`. However, I do not promise that those default values for the `Qt`-Version that are hardcoded in the `Dockerfile` will get updated on a regular basis, because I inject the desired versions all in my ci build matrix. Available build arguments are:

* `QT_VERSION_MAJOR`
* `QT_VERSION_MINOR`
* `QT_VERSION_PATCH`
* `CI_BUILD` -- will suppress regular `make` output and show only warnings. When the compiling fails, `make` is run again to give you the whole output, skipping everything that has been built before, to give you the possibility to see the actual behaviour.
* `CORE_COUNT` -- determines the number of parallel make jobs. Adjust it to fit your machine.

## Available tags

See the list in the dockerhub repo. More details will be added later.

## Contributions

I highly appreciate any contributions to this project. I will add contribution guidelines later on. As a short summary here is what you could do:

* Provide new or changed documentation
* File issues against the project
* Tinker nice badges to give a visual overview of the docker image structure or the build status for individual tags
* Open pull requests, for example
    + add new qt version build configurations
	+ add more qt features
	+ add tests

For opening pull requests, please keep the following in mind:

* Pull requests for the `master` branch will be rejected
* Pull requests must be made for the `deploy` branch only
* Pull requests that alter the build configuration or the dependencies in the base image to compile something that did not compile before have to provide a test that represents the changes. The test, when added without changes to build configs or docker file has to fail.
* Pull requests should be made with the fact in mind, that we want to provide a general multi purpose build environment that should not get bloated more than necessary. 

## License

Any directly written content in this repo is licensed under the `GPL v3`. Software parts that are produced during the image build and resulting docker images are of course a composition of components that probably carry their own licenses.
