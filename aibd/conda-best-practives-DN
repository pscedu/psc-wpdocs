[//]: # (Status: Draft)

# Conda Best Practices

## Introduction

Conda is a Python package distribution that allows setting up development environments and handling dependencies for a
curated set of packages that are widely used for Data Science and Machine Learning tasks. It is similar to using pip and
virtual environments, but it differs from those by providing a way to select performance-optimized packages (optimized
for CPU or GPU processing elements) to be installed based on the requirements of the task that has to be performed.

Additionally, using Conda (be it the full distribution "anaconda" or just the bare system "mini-conda"), still allows
users to keep using pip or virtualenv if needed.

Some other advantages of using Conda include:

* Access to performance optimized packages (MKL).
* Precompiled versions of packages, such as TensorFlow and PyTorch.
* Package compatibility lists, so installed packages are compatible with each other.
* Self-contained environments that can be maintained and used individually.

  More information can be found on their [website](https://www.anaconda.com/products/individual).

The [main difference between Conda and pip](https://www.anaconda.com/blog/understanding-conda-and-pip) is that Conda
installs (any) software binaries (no compilation required) while Pip compiles sources or wheels for (only) Python
packages;

## Before you start

Before using Conda on Bridges, please have in mind that each of the different Conda environments will be created using
an individual environment folder; and even if there is a cache folder for downloaded packages, it will still have to
download any dependencies that are missing. That will use a lot of disk space and will get your HOME folder quota to
100% utilization pretty easily. For that not to happen, set the files on the Ocean disk using of the following options:

```shell
# If you already have a conda folder, move it to PROJECT.
mv ~/.conda $PROJECT/ 

# Create a symlink from your HOME to the moved folder.
ln -s $PROJECT/.conda ~/.conda
```

## Load and activate the Conda modules

Next, load the Conda module by loading either the Community or the Enterprise version commands. After that, activate the
base conda environment for any of them if you want to use the base packages included with Anaconda; or create a new
environment yourself:

```shell
# Python 3
module load anaconda3
conda activate
```

Note: The `"anaconda3"` module makes use of Python 3. If the project to set up uses Python 2, then `"anaconda2"` should
be loaded, but it would be better to convert the project to Python 3 instead. The specific instructions for activating
each module can be found when running the command "`module help MODULENAME`".

## Create new environment

When creating a new environment with Conda, there are two main ways to do it:

1. To use the regular "conda create" command, which can create a blank environment if no packages are specified, or it
   could start by installing a list of packages (and it's dependencies) passed as an argument.

    ```shell
    # This will create an empty environment, but it's not recommended as is because the command is not specific.
    conda create -n YOUR_ENV_NAME python
    
    # The packages to install should be specified when creating the environment.
    conda create -n YOUR_ENV_NAME python PACKAGE1
    
    # The specific Python version can also be specified.
    conda create -n YOUR_ENV_NAME python=M.N PACKAGE1
    
    # Example: install the latest TensorFlow 2 that is compatible with Python 3.8
    conda create -n YOUR_ENV_NAME python=3.8 tensorflow=2 scipy
    ```    

2. To use the "conda env create" command, which uses a structured yaml file for installing an environment based on the
   complete list of packages generated from a different Conda environment. In this case, the file extension seems to be
   important as well, and it should be "yaml", since using a ".txt" extension triggered errors even if the content was
   still in yaml format.

    Example `PACKAGES_LIST.yaml` file for a regular `pandas` installation:
    ```yaml
    name: pandas_test
    channels:
      - defaults
    dependencies:
      - _libgcc_mutex=0.1=main
      - _openmp_mutex=5.1=1_gnu
      - blas=1.0=mkl
      - bottleneck=1.3.5=py310ha9d4c09_0
      - bzip2=1.0.8=h7b6447c_0
      - ca-certificates=2023.01.10=h06a4308_0
      - certifi=2022.12.7=py310h06a4308_0
      - intel-openmp=2021.4.0=h06a4308_3561
      - ld_impl_linux-64=2.38=h1181459_1
      - libffi=3.4.2=h6a678d5_6
      - libgcc-ng=11.2.0=h1234567_1
      - libgomp=11.2.0=h1234567_1
      - libstdcxx-ng=11.2.0=h1234567_1
      - libuuid=1.41.5=h5eee18b_0
      - mkl=2021.4.0=h06a4308_640
      - mkl-service=2.4.0=py310h7f8727e_0
      - mkl_fft=1.3.1=py310hd6ae3a3_0
      - mkl_random=1.2.2=py310h00e6091_0
      - ncurses=6.4=h6a678d5_0
      - numexpr=2.8.4=py310h8879344_0
      - numpy=1.23.5=py310hd5efca6_0
      - numpy-base=1.23.5=py310h8e6c178_0
      - openssl=1.1.1s=h7f8727e_0
      - packaging=22.0=py310h06a4308_0
      - pandas=1.5.2=py310h1128e8f_0
      - pip=22.3.1=py310h06a4308_0
      - python=3.10.9=h7a1cb2a_0
      - python-dateutil=2.8.2=pyhd3eb1b0_0
      - pytz=2022.7=py310h06a4308_0
      - readline=8.2=h5eee18b_0
      - setuptools=65.6.3=py310h06a4308_0
      - six=1.16.0=pyhd3eb1b0_1
      - sqlite=3.40.1=h5082296_0
      - tk=8.6.12=h1ccaba5_0
      - tzdata=2022g=h04d1e81_0
      - wheel=0.37.1=pyhd3eb1b0_0
      - xz=5.2.10=h5eee18b_1
      - zlib=1.2.13=h5eee18b_0
    prefix: $HOME/.conda/envs/pandas_test 
    ```
    ```shell
    conda env create -f PACKAGES_LIST.yaml --prefix /PATH/TO/NEW_CONDA_ENV
    
    # Example:
    conda env create -f packages_list.yaml --prefix $PROJECT/conda_envs/project_1
    source activate $PROJECT/conda_envs/project_1
    ```

__Note:__ Specifying a package version on Conda is not the same as when using pip. The syntax is similar but different. 
For  example, two equal signs are used with pip for specifying the version to use; but with Conda, one equal sign is
required.

For example:
```shell
pip install PACKAGE_NAME==VERSION_NUMBER
# Or
conda install PACKAGE_NAME=VERSION_NUMBER

# Correct usage, as pip uses two equal symbols.
pip install torch==1.0

# Incorrect, as conda uses only one equal symbol.
conda install torch==1.0

# Correct, as conda uses only one equal symbol.
conda install torch=1.0
```

## Using channels

It's possible that a package is not available in the regular Conda channel (default). For those cases, it's possible to
still install the package by specifying the name of the channel that has it available; however, please make sure that
it's actually required to do it that way, since it's also possible to install packages using pip directly, even it that
means compiling the specific packages.

```shell
# conda create -n YOUR_ENV_NAME SPECIAL_PACKAGE -c CHANNEL_NAME
conda create -n pytorch -c pytorch
```

Finally, make sure that the channel you are trying to use is an entity you can trust, since ill-intended individuals
could set modified packages available in an attempt to get researchers to install those Troyan-horse packages, providing
them with a way to access and infect even more HPC environments.

## Create a backup of your environment

Backups should be created as soon as a new functional environment is successfully created, so they can be easily
recreated in case accidental modifications are performed, access to the actual env directory is lost, or the environment
has to be deployed on a different host; and the steps for creating them involve generating a detailed list of installed
package, so it can be used for creating new environments using those values as inputs.

__Warning__: restoring backups of environments depends on the origin and
target Operative Systems being (roughly) the same. The environments will likely malfunction if they are not the same.
Examples for incompatibilities:

* CPU architectures differ (x86_64 vs ppc64).
* Operating Systems differ (CenOS 6 vs CentOS 7, CentOS/RHEL vs Ubuntu/Debian).
* Compilers or system libraries not available on the target system (gcc 4.x vs gcc 5.x, 6.x).
* Package distribution channels not being available (private Conda channels).

There are two main ways in which a backup can be created:

A. Export the environment: export the list of packages and then create a new environment when needed using that list as
the input.

### Steps for backing-up the env

Activate the env to generate the list of packages for, then export the list of packages to plain text files.

```shell
# This will create the Yaml file to use for creating the new environment. Refer to the examplel Yaml file under 
# the "Create new environment" section for reference.
conda env export >> conda_env_export.yaml

# This will generate a similar list, but it might have additional details.
conda list > conda_list.txt
```

Example `conda_list.txt` file for a `pandas` environment:
```yaml
# packages in environment at $HOME/.conda/envs/pandas_test:
#
# Name                    Version                   Build  Channel
_libgcc_mutex             0.1                        main  
_openmp_mutex             5.1                       1_gnu  
blas                      1.0                         mkl  
bottleneck                1.3.5           py310ha9d4c09_0  
bzip2                     1.0.8                h7b6447c_0  
ca-certificates           2023.01.10           h06a4308_0  
certifi                   2022.12.7       py310h06a4308_0  
intel-openmp              2021.4.0          h06a4308_3561  
ld_impl_linux-64          2.38                 h1181459_1  
libffi                    3.4.2                h6a678d5_6  
libgcc-ng                 11.2.0               h1234567_1  
libgomp                   11.2.0               h1234567_1  
libstdcxx-ng              11.2.0               h1234567_1  
libuuid                   1.41.5               h5eee18b_0  
mkl                       2021.4.0           h06a4308_640  
mkl-service               2.4.0           py310h7f8727e_0  
mkl_fft                   1.3.1           py310hd6ae3a3_0  
mkl_random                1.2.2           py310h00e6091_0  
ncurses                   6.4                  h6a678d5_0  
numexpr                   2.8.4           py310h8879344_0  
numpy                     1.23.5          py310hd5efca6_0  
numpy-base                1.23.5          py310h8e6c178_0  
openssl                   1.1.1s               h7f8727e_0  
packaging                 22.0            py310h06a4308_0  
pandas                    1.5.2           py310h1128e8f_0  
pip                       22.3.1          py310h06a4308_0  
python                    3.10.9               h7a1cb2a_0  
python-dateutil           2.8.2              pyhd3eb1b0_0  
pytz                      2022.7          py310h06a4308_0  
readline                  8.2                  h5eee18b_0  
setuptools                65.6.3          py310h06a4308_0  
six                       1.16.0             pyhd3eb1b0_1  
sqlite                    3.40.1               h5082296_0  
tk                        8.6.12               h1ccaba5_0  
tzdata                    2022g                h04d1e81_0  
wheel                     0.37.1             pyhd3eb1b0_0  
xz                        5.2.10               h5eee18b_1  
zlib                      1.2.13               h5eee18b_0
```

### Steps for creating (or restoring) the env

You can now create environments using the Yaml file with the list of packages from the original env.

```shell
conda env create -f conda_env_export.yaml --prefix /PATH/TO/NEW_CONDA_ENV

# It's also possible to clone an existing environment, instead of using the yaml file.
conda create --clone ORIGIN_CONDA_ENV --prefix=/PATH/TO/NEW_CONDA_ENV
```

**Note:** These steps might not work as expected when using a shared target folder (prefix). The env could be created
using the regular location first for testing purposes and generating the spec file.

B. Pack the environment: pack the whole environment into a compressed tar file, then decompress the file and unpack it
when needed.

```shell
# Install "conda-pack". This can be done with either the same or a new env.
conda install conda-pack -c conda-forge

# Pack the environment by specifying the location it's stored.
conda pack -p /PATH/TO/ORIGINAL_ENV_DIR/
conda pack -p /home/USER/.conda/envs/pytorch_22.12-py3

Collecting packages...
Packing environment to 'pytorch_22.12-py3.tar.gz'
[########################################] | 100% Completed |  11min 52.2s

# Create a new target directory for unpacking the environment.
# mkdir /PATH/TO/NEW_ENV_DIR/
mkdir $PROJECT/.conda_mlperf/pytorch_22.12-py3

# Unpack the environment into that target directory.
# tar -xzf ENV_NAME.tar.gz -C /PATH/TO/NEW_ENV_DIR/
tar -xzf pytorch_22.12-py3.tar.gz -C /home/USER/pytorch_22.12-py3

# Activate the environment
# conda activate /PATH/TO/NEW_ENV_DIR/
conda activate $PROJECT/.conda_mlperf/pytorch_22.12-py3

# Unpack the environment by cleaning-up the prefixes.
conda-unpack
```

## Use different directories when needed

Similar to when the .conda symlink from HOME to PROJECT was created in the "Before you start" section (and the
"[Storing your Anaconda environments](http://www.psc.edu/resources/software/anaconda)" of the Bridges User Guide), it is
possible to
have multiple directories for the different conda environments, and use that to have a way to archive the different
environment configurations across time.

For example, if a project was started and other existing environments are not going to be used for a while, a new conda
project could be created as a way to make sure those other environments will be safe from any modifications.

Please have in mind that the path names in the environments should not be changed, thus the name used the first time
should be kept unchanged over time; and names should be restored to their original when the directories have been
renamed.

Example: Switch from one conda directory to a new one.

```shell

# Unlinking the current directory.
unlink ~/.conda

# Rename the old directory. This is the one that should be returned to the original directory name if needed.
mv $PROJECT/.conda $PROJECT/conda_OLD_PROJECT_NAME_ARCHIVE  

# Create a new directory for conda under PROJECT.
mkdir $PROJECT/.conda

# Create the symlink again. This step could be ignored if the first one is ignored as well.
ln -s $PROJECT/.conda ~/.conda  
```

## Create environments on shared locations

Another approach for using different directories is to specify a prefixes to denote where in the filesystem a Conda
environment should be set

```shell

# Specify a location for the environment
conda create --prefix /path/to/env PACKAGE1 PACKAGE2

# Example:
conda create --prefix ~/.conda/envs/MY_ENV jupyterlab=0.35 matplotlib=3.1 numpy

# Create an environment in a shared location. It would only be available to the owner by default.
conda create --prefix /ocean/group/conda_envs/GROUP_ENV jupyterlab=0.35 matplotlib=3.1 numpy  
```

Create an environment in a shared location so it's available and writable to the team members.

```shell

# Identify the group to map the environment to.
groups 

# Log in to that group, so all files and folders created this session get associated to it.
newgrp  

# Set new files and directories as writable to the group. Add the same to ~/.bashrc for a lasting effect.
umask 002  

# Create a base directory for the environment to be at.
mkdir /ocean/GROUP/conda_envs/  

# Set sticky group permissions (s) for the environment directory.
chmod g+rwxs /ocean/GROUP/conda_envs/  


# env is used for specifying the spec (packages recipe) file.
conda env create -f conda/keras-retinanet4_conda_env_export.yaml --prefix $PROJECT/conda_envs/GROUP_ENV  
```

## Dos and Don'ts

Please do:

* Use a compute node for the installation process, so you can make use of the bandwidth and the I/O available there, but
  try to get the node for more than an hour so the progress is not lost if there are a lot of packages to install.
* When installing packages, try to specify the different packages at once, so Conda doesn't have to run the full
  set of compatibility validations every time.
* Make sure that the destination folder for the packages is set to use the $PROJECT disk space, as the home
  folder ($HOME) quota is low and the envs + cache are big.
* Try to always use Conda to install packages and not pip. Only use pip when Conda is not an option for installing those
  required packages.
* Try to only use the default Conda channel of the most popular and reputable ones. Install packages using pip if
  needed.
* Export the list of installed packages as soon as you confirm that an environment is working as expected. Set a
  mnemonic file name for that list, and save it in a secure place, in case you need to install the environment from
  PROJECT again.
* If you think an environment is not going to be used anymore, but you are not completely sure, consider renaming the
  Conda directory to something else, and then compress/tar the contents, in case you need them again at some point.

Please don't:

* Don't use additional Conda channels unless you know they are trustworthy.
* Don't install packages unless you are going to use them.
* Don't create multiple copies of the same environment, or at least tar the dir so there are less files using the file
  system.
