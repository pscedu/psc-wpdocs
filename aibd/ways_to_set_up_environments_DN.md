There are multiple ways to set up custom development environments on Bridges-2. 
They include:

* [Using Singularity containers](#using-singularity-containers)
* [Using predefined Bridges-2 environment modules](#using-predefined-bridges-2-environment-modules)
* [Using a Conda module environment](#using-a-conda-module-environment)
* [Using the default Python installation](#using-the-default-python). This method is not supported and not recommended unless you are familiar with virtualenvs and pip.

We recommend using [Singularity](https://sylabs.io/singularity/) containers, especially the ones from the
the [NVIDIA NGC catalog](https://catalog.ngc.nvidia.com/) if there is one that fits your needs, as those are
curated by NVIDIA and optimized for their GPUs. Otherwise, try using the predefined
Bridges-2 modules, or creating a custom Anaconda environment.

### Using Singularity containers

Bridges-2 supports running Singularity containers, allowing
encapsulated environments to be built from scratch. You cannot use
Docker containers on Bridges-2, but you can download a Docker container
and convert it to Singularity format. Examples are given below
showing how to convert containers.

There are many containers for AI/BD applications already installed on Bridges-2 in directory `/ocean/containers/ngc`. These are already in Singularity format and ready to use. You can use one of these containers or you can create a container of your own.

### 
<table>
<thead>
<tr>
<th>When to use Singularity containers</th><th>Advantages</th><th>Disadvantages</th>
</tr>
</thead>
<tbody>
<tr>
<td style="vertical-align:top;">
<ul>
<li>When a pre-configured Docker container already exists
<li>When superuser privileges are needed or setting up the environment, like installing an OS library
</li>
</ul>
</td>
<td style="vertical-align:top;">
<ul>
<li>Offers flexibility for installing more libraries and software
<li>Allows reproducible results since the container can be reused across hosts
<li>Can be easily placed and used from I/O optimized storage
</li>
</ul>
   </td>
   <td style="vertical-align:top;">
<ul>
<li>The singularity module must be loaded every time.</li>
<li>Adds another layer of complexity when developing and troubleshooting code
<li>Uses at least a couple of gigabytes of disk space
<li>Singularity and Docker are not 100% compatible but only Singularity is available on Bridges-2
<li>Extra steps are required for modifying containers
</li>
</ul>
   </td>
   </tr>
   </tbody>
   </table>

#### Pulling and converting Docker containers to Singularity

You can pull a Docker container into Bridges-2 and convert it to
Singularity format with the <code>Singularity pull</code> command. 

**Note** This should be done in an interactive session on Bridges-2.  See the [Interactive sessions section in the Bruidges-2 User Guide](https://www.psc.edu/resources/bridges-2/user-guide/#interactive-sessions) for more information.

To pull a container from [DockerHub](https://hub.docker.com/) and convert it to Singularity:
```shell
interact  # Start an interactive session on a Regular Memory node.
singularity pull --disable-cache docker://alpine:latest  # Pull the latest "alpine" container from DockerHub.
```
You should now have a ".sif" file. That's the container converted into Singularity Image Format (SIF).


To pull a container from the [NVIDIA NGC library](https://catalog.ngc.nvidia.com/) and convert it to Singularity:
```shell
interact  # Start an interactive session on a Regular Memory node.
singularity pull --disable-cache docker://nvcr.io/nvidia/pytorch:22.12-py3` # Pull the 22.12 PyTorch container from NGC.
```
You should now have a ".sif" file. That's the container converted into Singularity Image Format (SIF).

These examples pulled a container from DockerHub, using "docker://" as the origin string in the <code>singularity pull</code> command, but there are other
valid container origin points to pull containers from:

- The [Singularity Container Library](https://cloud.sylabs.io/library)
  * Use "library://" as the origin string in the <code>singularity pull</code> command
- [Singularity Hub](https://singularity-hub.org/)
  * Use "shub://" as the origin string in the <code>singularity pull</code> command

#### Using a Singularity container

Once you have a Singularity container, start an interactive session on
Bridges-2 and start your container. See the section on [interactive
sessions in the Bridges-2 User Guide](https://www.psc.edu/resources/bridges-2/user-guide/#interactive-sessions)
 for details on the interact command.

```shell
interact   # Start an interactive session. 
singularity shell --nv /path/to/CONTAINER.sif
```

[More information on using Singularity at PSC can be found
in the PSC Singularity documentation.](https://www.psc.edu/resources/software/singularity/)

##### Example: Use a container already on Bridges-2
```shell
interact  # Start an interactive session.
# The path to the container is long. Let’s use a variable for readability.
CONTAINER=/ocean/containers/ngc/tensorflow/tensorflow_latest.sif

# Pull the container. Specify no cache dir to be used so only the local disk is used.
# Then use pip freeze to confirm what is installed
singularity exec --nv ${CONTAINER} pip freeze | grep tensorflow    
    tensorflow @ file:/// [...] 2.10.1 [...]
    tensorflow-addons==0.11.2
    tensorflow-datasets==3.2.1
    tensorflow-estimator==2.10.0
    tensorflow-metadata==1.12.0
    tensorflow-nv-norms @ file:/// [...]
    tensorflow-probability==0.11.1
```

##### Example: Pull a container into Bridges-2

When the container you need is not present on Bridges-2 already, you
can pull one from a given URI. Run the following commands in an interactive session to pull a
container to Bridges-2. See the section on [interactive
sessions in the Bridges-2 User Guide](https://www.psc.edu/resources/bridges-2/user-guide/#interactive-sessions)
 for details on the interact command.

This example pulls a container from Docker
Hub and then saves it to $PROJECT for later use.

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


### Using predefined Bridges-2 environment modules 

PSC has built some environments which provide a rich, unified,
Anaconda-based environment for AI, Machine Learning, and Big Data
applications. Each environment includes several popular AI/ML/BD
packages, selected to work together well.

These environments are built for the GPU nodes on Bridges-2. Be sure
to use one of the GPU partitions. See the [Bridges-2 User
Guide](https://www.psc.edu/resources/bridges-2/user-guide/#partitions)
for information on Bridges-2 partitions and how to choose one to use.

To use an already existing environment from
Bridges-2, identify the environment module to use and load
it.

To see a list of the available environments, type
<pre class="sample">module spider AI</pre>

To see what is included in a given environment before you load it, you can use the <code>module show <i>module_name</i></code> command.

<table>
<thead>
<tr>
<th>When to use Bridges-2 modules</th><th>Advantages</th><th>Disadvantages</th>
</tr>
</thead>
<tbody>
<tr>
<td style="vertical-align:top;">
When using libraries that are popular for Data Science or Machine Learning, as those are most likely available on Bridges-2 as a module. 
</td>
<td style="vertical-align:top;">

The Bridges-2 modules available are installed, tested, and supported by PSC
staff, and are configured in a way to get the best performance
on Bridges-2.

</td>
   <td style="vertical-align:top;">
The modules cannot be modified unless a local copy for the user is created.
   </td>
   </tr>
   </tbody>
   </table>


#### Example: Use existing TensorFlow 2 module

```shell
interact   # Start an interactive session

module avail AI
    AI/anaconda3-tf2.2020.11
    AI/pytorch_22.07-1.12-py3
    AI/tensorflow_22.07-2.8-py3

module load AI/anaconda3-tf2.2020.11

# Check what version of tensorflow you have
pip freeze | grep tensorflow
    tensorflow==2.0.0
    tensorflow-estimator==2.0.0
```


### Using a Conda module environment

Using a Conda environment allows you to set up an environment from
scratch. First load an Anaconda module and then create a new
environment by specifying a name for your new environment and the
packages to include. 

Please note that there is a default
Anaconda environment with multiple packages already installed on Bridges-2 (base),
but that default environment cannot be extended. That is why
a new environment is being created from scratch.

We recommend that you install all of the packages at the same time,
since Conda tries to make the packages compatible as much as possible
every time a new one is installed. That is, if all of the packages
required are installed at the same time, only one
package-compatibility process is run; but if the packages are
installed one at a time, the package-compatibility process will have
to run once per package and the overall installation will take a lot
longer.

You can install as many packages as you like with one
command. Optionally, you can choose the versions of your
packages, although leaving versions unspecified allows Conda to
find the best option.

Examples  of the syntax of the command to create an environment and install
packages are given here. Refer to the [Conda documentation](https://docs.conda.io/projects/conda/en/latest/commands/create.html) for full details.

<pre class="sample">
conda create -n <i>ENV_NAME PACKAGE1</i>
conda create -n <i>ENV_NAME</i> python=3.<i>VERSION.MINORVERSION PACKAGE2 PACKAGE3</i>
</pre>

<table>
<thead>
<tr>
<th>When to use a Conda module</th><th>Advantages</th><th>Disadvantages</th>
</tr>
</thead>
<tbody>
<tr>
<td style="vertical-align:top;">
When the available Bridges-2 modules do not have a library that is also required for a project or the versions are slightly different as to what is needed (i.e. TensorFlow 2.1 instead of 2.2)
</td>
<td style="vertical-align:top;">
<ul><li>Self-contained environments can be created</li>
<li>Specific Python versions can be used for each installation</li>
<li>Offers performance-optimized packages compatible with each other</li>
<li>Packages can also be installed via pip if needed</li>
<li>Anaconda will provide a list of curated packages that are optimized for performance.</li>
<li>Environment reusability is robust with Anaconda, as environments can be cloned to other locations or created based on recipes that detail the specific versions used
</li>
</ul>
   </td>
   <td style="vertical-align:top;">
<ul><li>The Anaconda module has to be loaded and activated every time</li>
<li>The default destination folder for the environments uses the $HOME folder quota</li>
<li>It's impacted by Input/Output bottlenecks since it uses a lot of small files</li>
</li>
</ul>

   </td>
   </tr>
   </tbody>
   </table>


#### Example: Install TensorFlow 2

````shell
interact # Start an interactive session

module load anaconda3
conda activate

conda create -n my_tf2_env tensorflow>=2
conda activate my_tf2_env

# Check which version of tensorflow you have
pip freeze | grep tensorflow
    tensorflow==2.6.2
    tensorflow-estimator==2.6.0
````

**NOTE:** make sure that the target directory for the Anaconda
  environments is pointing to the `"$PROJECT"` folder. Your $PROJECT
  quota is much larger than your $HOME directory quota.

More information can be found
[in the PSC Anaconda documentation at https://www.psc.edu/resources/software/anaconda/](https://www.psc.edu/resources/software/anaconda/).

### Using the default Python

Both `"python, pip"` and `"python3, pip3"` are available on Bridges-2
by default when logging into the nodes. These distributions that
are available by default can be customized by installing packages,
although the base Python version cannot be changed.

**Note:** PSC **does not** offer support for user-customized python environments.

This way of customizing the default Python environment allows you to install packages using the `"--user"` flag, making it
possible to extend the base package list and install
libraries. Additionally, pip can be used both as the default binary or
as a Python module. The following example shows both ways, but it’s
recommended to use it as a Python module (with `"python -m pip")` so
the original default pip is not used anymore after updating the
package manager version.

```shell
interact   # Start an interactive session

python3 -m pip install PACKAGE1 --user
pip3 install PACKAGE2==VERSION --user
```

#### Example: Install TensorFlow 2
```shell
interact   # Start an interactive session

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

**Note:** The installed packages should have been stored under the following
directory: `$HOME/.local/lib/python3.6/site-packages/`

Additionally, installing tools such as `"virtualenv"` for managing different environments is also supported.

**Note:** Having locally installed libraries, and then running Python from inside a Singularity/AppTainer container, 
might create problems for your containerized jobs as the Python installation inside the container might try using your
`$HOME/.local/lib/` packages and thus create instability due to incompatible configurations (container + local packages 
mix).


<table>
<thead>
  <tr>
   <th>When to use Python</th>
   </th>
   <th>Advantages
   </th>
   <th>Disadvantages
   </th>
  </tr>
  </thead>
  <tbody>
  <tr>
      <td>
<ul>
<li>When only the libraries bundled with the default Python are required</li>
<li>When the user is used to using virtualenvs and pip instead of Anaconda environments</li>
</ul>
   </td>
<td>
Immediately available for simple tests.
   </td>
      <td>
<ul>
<li>The packages available are not optimized by default for performance purposes</li>
<li>Pip will run with default options when installing packages and will not try to optimize the performance of  Data Science or Machine Learning packages</li>
</ul>
   </td>
  </tr>
  </tbody>
</table>

