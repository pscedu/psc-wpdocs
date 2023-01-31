[//]: # (Status: Draft)
# Options available for setting up development environments on Bridges-2

## Table of Contents

- Default Python Installation
- Environment Modules
- Conda Environments
- Singularity Containers

## Introduction

When using Bridges-2, there are multiple ways to set up a development environment.
If we were to take a look at the different ways available for starting a Python project, the following would work out of
the box:

* Using the default Python 3 installation.
* Using Bridges-2 environment modules.
* Using Conda module environments .
* Using Singularity containers.

## Using the default Python

Both `"python, pip"` and `"python3, pip3"` are available on Bridges-2 by default when logging into the nodes, and these
distributions that are available by default can be customized by installing packages, although the base Python version
cannot be changed.

This way of customizing the default Python environment allows regular users to install packages using the `"--user"`
flag, making it possible to extend the base package list and install libraries. Additionally, pip can be used both as
the default binary or as a Python module. The following example shows both ways, but it’s recommended to use it as a
Python module (with `"python -m pip")` so the original default pip is not used anymore after updating the package
manager version.

```shell

python -m “pip3” install PACKAGE1 --user
pip3 install PACKAGE2==VERSION --user
```

Example, install TensorFlow 2:

```shell
# Add the local Python-binaries path to your PATH environment variable.
# This line could also be added to your local ~/.bashrc file.
export PATH=”${PATH}:${HOME}/.local/bin”

# Install TensorFlow
python3 -m "pip" install tensorflow-gpu --user
	Collecting tensorflow-gpu [...]
	Successfully installed [...]

# Double-check if TensorFlow was indeed installed.
python3 -m "pip" freeze | grep tensorflow
    tensorflow==2.3.0
    tensorflow-estimator==2.3.0
	tensorflow-gpu==2.3.0

# Upgrade pip for getting rid of the package-manager-related warnings.
python3 -m "pip" install --upgrade pip --user
```

**Note:** The installed packages should have been stored under the following
directory:  `~/.local/lib/python3.6/site-packages/`

Additionally, installing tools such as `"virtualenv"` for managing different environments is also supported.

## Using Bridges-2 environment modules

This option allows us to load an already existing environment from Bridges-2. For using it, identify the environment
module to use, load it, and then activate it.

Example, use existing TensorFlow 2 Bridges module:

```shell
module avail tensorflow
        tensorflow/1.15_py3_gpu
        tensorflow/2.1_py3_gpu

module load tensorflow/2.1_py3_gpu
conda activate
conda activate /opt/packages/TensorFlow/gnu/tf2.1.0_py3_conda

pip freeze | grep tensorflow
    tensorflow==2.1.0
    tensorflow-datasets==1.2.0
    tensorflow-estimator==2.1.0
    tensorflow-metadata==0.14.0
```

## Using the Conda module

This way allows us to set up an environment right from the start. For using it, an Anaconda module has to be loaded
first, and then a new environment can be created by specifying a name and the Python along with package versions to use,
although the versions can be left unspecified for Conda to find the best option.

Please note that in this scenario, there is going to be a base Anaconda environment with multiple packages already
installed (base), but that env cannot be extended unless it’s first closed, that is why a new environment is being
created from zero.

Additionally, the idea is to try and install all of the packages at the same time, since Conda tries to make them match
as much as possible every time a new package is installed. That is, if all of the packages required are installed at the
same time, only one package-compatibility process is run; but if the packages are installed one at a time, the
package-compatibility process will have to run once per package and the overall installation will take a lot longer

Example, install TensorFlow 2:

```shell

conda create -n ENV_NAME PACKAGE1 PACKAGE2
conda create -n ENV_NAME PACKAGE1 PACKAGE2=VERSION
conda create -n ENV_NAME python=3.VERSION PACKAGE1 PACKAGE2
conda create -n ENV_NAME python=3.VERSION.MINORVERSION PACKAGE1 PACKAGE2

module load anaconda3
conda activate

conda create -n my_tf2_env tensorflow-gpu>=2
conda activate my_tf2_env

pip freeze | grep tensorflow
	tensorflow==2.2.0
	tensorflow-base==2.2.0
	tensorflow-gpu==2.2.0
	tensorflow-estimator==2.2.0
```

**NOTE:** make sure that the target directory for the Anaconda environments is pointing to the `"$PROJECT"` folder.

More information can be found
here: [https://www.psc.edu/resources/software/anaconda/](https://www.psc.edu/resources/software/anaconda/)

## Using a Singularity container

Bridges-2 supports running Singularity containers, allowing encapsulated environments to be built from scratch, or
Docker containers to be pulled (and transformed into Singularity ones) from the Docker registry.

For using singularity containers, first load the module, and then either use a container already present in Bridges-2 or
create (convert) your own:

```shell

module load singularity

# Optional: download the container from Docker and convert it to Singularity format.

singularity exec --nv /path/to/CONTAINER.sif

Example 1, Use a container already on Bridges:


# The path to the container is long. Let’s use a variable for readability.
CONTAINER=/ocean/containers/ngc/tensorflow/tensorflow_latest.sif

# Pull the container. Specify no cache dir to be used so only the local disk is used.
singularity exec --nv ${CONTAINER} pip freeze | grep tensorflow    
    tensorflow @ file:/// [...] 2.10.1 [...]
    tensorflow-addons==0.11.2
    tensorflow-datasets==3.2.1
    tensorflow-estimator==2.10.0
    tensorflow-metadata==1.12.0
    tensorflow-nv-norms @ file:/// [...]
    tensorflow-probability==0.11.1

```

For building a container from the ground up, in case it’s not present on Bridges-2 already, run the following commands.

```shell

# Start a job for building the container faster.
interact

# Change to the high-speed flash storage folder.
cd $LOCAL

# Pull the external container by specifying the origin right before the tag.
# i.e. for pulling Docker containers, use “docker://”
singularity pull --disable-cache docker://USERNAME/CONTAINER

# Finally, since the $LOCAL storage is fast but ephemeral, copy the container back to your file space.
cp CONTAINER.sif $PROJECT/ # Or $HOME

```

The example above pulled a container from Docker, but there are other valid container origin points to pull containers
from:

- [Singularity Container Library](https://cloud.sylabs.io/library)
  * library://
- [Singularity Hub](https://singularity-hub.org/)
  * shub://
- [Docker Hub](https://hub.docker.com)
  * docker://
- Container already on Docker infrastructure
  * Local container
- Blueprint with container to use and customizations
  * Recipe files
- Tar file with container files
  * Local archive

More information can be found
here: [https://www.psc.edu/resources/software/singularity/](https://www.psc.edu/resources/software/singularity/)

<table>
  <tr>
   <td>
   </td>
   <td><strong>Default Python</strong>
   </td>
   <td><strong>Bridges-2 environment modules</strong>
   </td>
   <td><strong>Anaconda module</strong>
   </td>
   <td><strong>Singularity container</strong>
   </td>
  </tr>
  <tr>
   <td><strong>When to use</strong>
   </td>
   <td>
<ul>

<li>When only the libraries bundled with the default Python are required.

<li>When the user is used to using virtualenvs and pip instead of Anaconda environments.
</li>
</ul>
   </td>
   <td>
<ul>

<li>When requiring to use libraries that are popular for Data Science or Machine Learning, as those are most likely available on Bridges-2 as a module. 
</li>
</ul>
   </td>
   <td>
<ul>

<li>When the available Bridges-2 modules do not have a library that is also required for a project or the versions are slightly different as to what is needed (i.e. TensorFlow 2.1 instead of 2.2)
</li>
</ul>
   </td>
   <td>
<ul>

<li>When a pre-configured Docker container already exists.

<li>When superuser privileges are needed or setting up the environment, like installing an OS library.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td><strong>Advantages</strong>
   </td>
   <td>
<ul>

<li>Immediately available for simple tests.
</li>
</ul>
   </td>
   <td>
<ul>

<li>The Bridges-2 modules available are installed and tested by PSC Staff, are technically-supported by them, and are configured in a way in which the most performance should be available from Bridges-2 by default (CPU-level instructions like MKL, GPUs as targets).
</li>
</ul>
   </td>
   <td>
<ul>

<li>Self-contained environments can be created.

<li>Specific Python versions can be used for each installation.

<li>Offers performance-optimized packages compatible with each other.

<li>Packages can also be installed via pip if needed.

<li>Anaconda will provide a list of curated packages that are optimized for performance (CPU-level instructions like MKL, GPUs as targets).

<li>Environment reusability is robust with Anaconda, as environments can be cloned to other locations or created based on recipes that detail the specific versions used.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Offers flexibility for installing more libraries and software.

<li>Allows reproducible results since the container can be reused across hosts.

<li>Can be easily placed and used from I/O optimized storage.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td><strong>Disadvantages</strong>
   </td>
   <td>
<ul>

<li>The packages available are not optimized by default for performance purposes.

<li>Pip will run with default options when installing packages and will not try to optimize the performance of  Data Science or Machine Learning packages.
</li>
</ul>
   </td>
   <td>
<ul>

<li>The modules cannot be modified unless a local copy for the user is created.
</li>
</ul>
   </td>
   <td>
<ul>

<li>The Anaconda module has to be loaded and activated every time.

<li>The default destination folder for the environments uses the $HOME folder quota.

<li>It's impacted by Input/Output bottlenecks since it uses a lot of small files.
</li>
</ul>
   </td>
   <td>
<ul>

<li>The singularity module has to be loaded every time.

<li>Adds another layer of complexity when developing and troubleshooting code.

<li>Uses at least a couple of gigabytes of disk space.

<li>Singularity and Docker are not 100% compatible but only Singularity is available on Bridges-2.

<li>Extra steps are required for modifying containers.
</li>
</ul>
   </td>
  </tr>
</table>

In the end, the recommendation is to use the Bridges-2 modules if there is already one available for a specific package;
else try using a Singularity container or creating a custom Anaconda environment.