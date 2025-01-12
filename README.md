This repository contains Dockerfiles which will install all dependencies needed to build [Performous](https://github.com/performous/performous/wiki/Building-and-installing-from-source).  

These containers are to be used as `base images` to provide higher-level builds and packages and to produce artifacts for downstream consumption. These containers **do not** provide a running version of `Performous` or contain the project source in any usable form.  

## Building containers
The build is a pretty standard `docker build`, just make sure you explicitly call out a `Dockerfile` with `-f Dockerfile.<distro.version>`:  
```sh
docker build -t performous-docker-build:ubuntu20.04 -f Dockerfile.ubuntu20.04 .
```

## Running the containers
Once the `base-image` has been built, the container can be run interactively to build `Performous`:  
```sh
docker run -it performous-docker-build:ubuntu20.04
```  

From there, you can [follow the build instructions](https://github.com/performous/performous/wiki/Building-and-installing-from-source#downloading-and-installing-the-sources) to build performous.  


`build_performous.sh` is included in the containers for testing builds and creating OS packages.
```
Usage: ./build_performous.sh -a (build with all build systems)

Optional Arguments:
  -b <Git Branch>: Build the specified git branch, tag, or sha
  -p <Pull Request #>: Build the specified Github Pull Request number
  -m : Use Meson to build
  -c : Use Cmake to build
  -g : Generate Packages
  -r <Repository URL>: Git repository to pull from
  -R : Perform a 'Release' Cmake Build (Default is 'RelWithDebInfo')
  -h : Show this help message
```

To build a pull request using just cmake:
```
docker run performous-docker-build:ubuntu20.04 ./build_performous.sh -c -p 626
```

One-Liner to generate packages and copy them to `/tmp` on the running system:
```
mkdir /tmp/performous-packages && docker run --rm --mount type=bind,source=/tmp/performous-packages,target=/performous/packages performous-docker-build:ubuntu20.04 /bin/bash -c './build_performous.sh -c -R -g && cp performous/build/*.deb /performous/packages'
```
