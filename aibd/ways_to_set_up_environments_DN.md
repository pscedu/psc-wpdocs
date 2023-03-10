[//]: # (Status: Draft)
# Options available for setting up development environments on Bridges-2

## Introduction

There are multiple ways to set up  development environments on Bridges-2. 
The ways available for starting a Python project include:

* [Using predefined Bridges-2 environment modules](https://github.com/pscedu/psc-wpdocs/blob/dev/aibd/ways_to_set_up_environments_DN.md#using-predefined-bridges-2-environment-modules-predefined)
* Using Conda module environments 
* Using Singularity containers
* Using the default Python 3 installation

We recommend  using the Bridges-2 modules if
there is already one available for a specific package; else try using
a Singularity container or creating a custom Anaconda environment.

## Using predefined Bridges-2 environment modules 

PSC has built some environments which provide a rich, unified,
Anaconda-based environment for AI, Machine Learning, and Big Data
applications. Each environment includes several popular AI/ML/BD
packages, selected to work together well.

These environments are built for the GPU nodes on Bridges-2. Be sure
to use one of the GPU partitions. See the [Bridges-2 User
Guide](https://www.psc.edu/resources/bridges-2/user-guide#partitions)
for information on Bridges-2 partitions and how to choose one to use.


To use an already existing environment from
Bridges-2, identify the environment module to use, load
it, and then activate it.

To see a list of the available environments, type
<pre class="sample">module spider AI</pre>

To see what is included in a given environment before you load it, you can use the <code>module show <i>module_name</i></code> command.

### Example: use existing TensorFlow 2 module

```shell
module avail AI
    AI/anaconda3-tf1.2020.11
    AI/anaconda3-tf2.2020.11

module load AI/anaconda3-tf2.2020.11

pip freeze | grep tensorflow
    tensorflow==2.0.0
    tensorflow-estimator==2.0.0
```

## Using a Conda module environment

Using a Conda environment allows you to set up an environment from
scratch. First load an Anaconda module and then create a new
environment by specifying a name for your new environment and the
packages to include. Optionally, you can choose the versions of your
packages, although the versions can be left unspecified for Conda to
find the best option.

Please note that in this scenario, there is going to be a base
Anaconda environment with multiple packages already installed (base),
but that env cannot be extended unless it’s first closed, that is why
a new environment is being created from scratch.

We recommend that you install all of the packages at the same time,
since Conda tries to make the packages compatible as much as possible
every time a new one is installed. That is, if all of the packages
required are installed at the same time, only one
package-compatibility process is run; but if the packages are
installed one at a time, the package-compatibility process will have
to run once per package and the overall installation will take a lot
longer.

The syntax of the command to create an environment and install packages is:

<pre class="sample">
conda create -n <i>ENV_NAME PACKAGE1 PACKAGE2</i>
conda create -n <i>ENV_NAME PACKAGE1 PACKAGE2=VERSION</i>
conda create -n <i>ENV_NAME</i> python=3.<i>VERSION PACKAGE1 PACKAGE2</i>
conda create -n <i>ENV_NAME</i> python=3.<i>VERSION.MINORVERSION PACKAGE1 PACKAGE2</i>
</pre>

### Example: install TensorFlow 2

````shell
module load anaconda3
conda activate

conda create -n my_tf2_env tensorflow>=2
conda activate my_tf2_env

pip freeze | grep tensorflow
    tensorflow==2.6.2
    tensorflow-estimator==2.6.0
````

**NOTE:** make sure that the target directory for the Anaconda
  environments is pointing to the `"$PROJECT"` folder. Your $PROJECT
  quota is much larger than your $HOME directory quota.

More information can be found
[in the PSC Anaconda documentation at https://www.psc.edu/resources/software/anaconda/](https://www.psc.edu/resources/software/anaconda/).

## Using a Singularity container

Bridges-2 supports running Singularity containers, allowing
encapsulated environments to be built from scratch, or Docker
containers to be pulled (and transformed into Singularity ones) from
the Docker registry.

To use singularity containers, **first load the module, (which?)** and then
either use a container already present in Bridges-2 or create
(convert) your own.

You cannot use Docker containers on Bridges-2, but you can download
a container from Docker and convert it to Singularity format.

To pull a container from [DockerHub](https://hub.docker.com/):
```shell
singularity pull --disable-cache docker://alpine:latest
```
To pull a container from the [NVIDIA NGC library](https://catalog.ngc.nvidia.com/):
```shell
singularity pull --disable-cache docker://nvcr.io/nvidia/pytorch:22.12-py3` 
```

Convert the container to Singularity:
```shell
singularity exec --nv /path/to/CONTAINER.sif
```

### Example 1: Use a container already on Bridges-2
```shell
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

### Example 2: Create a container on Bridges-2
To build a container, when it’s not present on Bridges-2 already, run the following commands.

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

The example above pulled a container from Docker, but there are other
valid container origin points to pull containers from:

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

## Using the default Python

Both `"python, pip"` and `"python3, pip3"` are available on Bridges-2
by default when logging into the nodes. These distributions that
are available by default can be customized by installing packages,
although the base Python version cannot be changed.

This way of customizing the default Python environment allows you to install packages using the `"--user"` flag, making it
possible to extend the base package list and install
libraries. Additionally, pip can be used both as the default binary or
as a Python module. The following example shows both ways, but it’s
recommended to use it as a Python module (with `"python -m pip")` so
the original default pip is not used anymore after updating the
package manager version.

```shell

python3 -m pip install PACKAGE1 --user
pip3 install PACKAGE2==VERSION --user
```

### Example: install TensorFlow 2
```shell
# Add the local Python-binaries path to your PATH environment variable.
# This line could also be added to your local ~/.bashrc file.
export PATH=”${PATH}:${HOME}/.local/bin”

# Install TensorFlow
python3 -m pip install tensorflow --user
    Collecting tensorflow
      Downloading tensorflow-2.[...]
      
      [...]
      
	Successfully installed [...]

# Double-check if TensorFlow was indeed installed.
python3 -m pip freeze | grep tensorflow
    tensorflow==2.6.2
    tensorflow-estimator==2.6.0

# Upgrade pip for getting rid of the package-manager-related warnings.
python3 -m pip install --upgrade pip --user
```

__Note:__ The installed packages should have been stored under the following
directory: `$HOME/.local/lib/python3.6/site-packages/`

Additionally, installing tools such as `"virtualenv"` for managing different environments is also supported.

__Note:__ Having locally installed libraries, and then running Python from inside a Singularity/AppTainer container, 
might create problems for your containerized jobs as the Python installation inside the container might try using your
`$HOME/.local/lib/` packages and thus create instability due to incompatible configurations (container + local packages 
mix).

## Comparision of ways to create a development environment

<table>
<thead>
  <tr>
   <th>
   </th>
   <th><strong>Bridges-2 environment modules</strong>
   </th>
   <th><strong>Anaconda module</strong>
   </th>
   <th><strong>Singularity container</strong>
   </th>
      <th>Default Python
   </th>
  </tr>
  </thead>
  <tbody>
  <tr>
   <td><strong>When to use</strong>
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
      <td>
<ul>

<li>When only the libraries bundled with the default Python are required.

<li>When the user is used to using virtualenvs and pip instead of Anaconda environments.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td><strong>Advantages</strong>
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
      <td>
<ul>

<li>Immediately available for simple tests.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td><strong>Disadvantages</strong>
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
      <td>
<ul>

<li>The packages available are not optimized by default for performance purposes.

<li>Pip will run with default options when installing packages and will not try to optimize the performance of  Data Science or Machine Learning packages.
</li>
</ul>
   </td>
  </tr>
  </tbody>
</table>

