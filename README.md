# mpact-dev-env
<b>Overview</b>

This repository contains scripts to install the MPACT development environment into a specified directory. The following tools are installed, using user-specified versions: autoconf, cmake, gitdist, gcc, mpi (mpich or mvapich). Additionally, the VERA third party libraries (tpls) are included as a submodule and installed. Installation of VERA tpls requires Python 2 and will fail if Python 3 is used. Lastly, a Dockerfile is written and optionally built into a container with an MPACT development environment with SSH access. 

The installation creates the following directory structure:

* <install_dir>/
  * common_tools/
    * autoconf-\<autoconf-version>/
    * cmake-\<cmake-version>/
    * gitdist/
  * gcc-\<gcc-version>/
    * load_dev_env.[sh,csh]
    * toolset/
      * gcc-\<gcc-version>/
      * mpich-\<mpich-version>/
    * tpls/
  * images/
    * dev_env/
    * install/

    
<b>Versioning</b>

mpact-dev-env-\<major_version>.\<minor_version>.\<patch>-tag

Major version: incremented if TPLs or toolchain changes

Minor version: incremented if toolchain changes

Patch: incremented if a TPL version changes

Tag: set for any devations from major.minor.patch

<b>Docker and SSH Information</b>

Running install_devtools.py will create the images directory with two subdirectories, each containing a Dockerfile.

Within /images/dev_env will be a Dockerfile to create a containerized mpact development environment with the same parameters that were passed to install_devtools.py (i.e., same versions of gcc, mpi, etc). The image can be built by passing install_devtools the flag -b or --build, or by running "docker build -t \<dev-env image name> ." from within the /images/dev_env/ directory. The container can then be accessed interactively by running "docker run -it \<dev-env image name>". 
 
Within /images/install will be a Dockerfile to create an image that builds off an mpact-dev-env image and allows ssh access into the development environment to test installation of software. By default, the image builds off an image named "mpact-dev-env:latest". To specify a different image, edit the Dockerfile and change "FROM mpact-dev-env:latest" to "FROM \<dev-env image name>". The image can be built by running "docker build -t \<ssh image name> ." from within the /images/install directory. Note that \<dev-env image name> must have been previously built.
 
To then ssh into the container, run the following commands:
docker run -d -p \<port>:22 \<ssh image name>
ssh mpact-user@\<virtual machine IP> -p \<port>
 
\<port> must be an open port on the virtual machine being used by Docker, and \<virtual machine IP> is the address of that virtual   machine, which can be found by running "docker-machine ip". Alternatively, run "docker run -d -P \<ssh image name>", which causes Docker to automatically map port 22 on the container to an open port on the machine, then run "docker ps -a" to see which port was mapped, then ssh using that port. 

