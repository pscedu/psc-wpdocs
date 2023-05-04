## Create a backup of your environment

Backups should be created as soon as a new functional environment is successfully created. Backups allow your new environment to be easily
recreated if accidental modifications are performed, access to the actual env directory is lost, or the environment
has to be deployed on a different host. Creating backups involves generating a detailed list of installed
packages that can be used to recreate an environment using those values as inputs.

__Warning__: restoring backups of environments depends on the origin and
target Operating Systems being (roughly) the same. The environments will likely malfunction if they are not the same.
Examples of incompatibilities:

* CPU architectures differ (x86_64 vs ppc64)
* Operating Systems differ (CenOS 6 vs CentOS 7, CentOS/RHEL vs Ubuntu/Debian)
* Compilers or system libraries not available on the target system (gcc 4.x vs gcc 5.x, 6.x)
* Package distribution channels not being available (private Conda channels)

There are two main ways in which a backup can be created:
 * Export the environment
 * Pack the environment
 

### Export the environment
Export the list of packages and then create a new environment when needed using that list as
the input.

#### Steps for backing up the env

Activate the env to generate a list of the packages in it, then export the list of packages to a plain text file.

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

#### Steps for creating (or restoring) the env

You can now create environments using the Yaml file with the list of packages from the original env.

```shell
conda env create -f conda_env_export.yaml --prefix /PATH/TO/NEW_CONDA_ENV

# It's also possible to clone an existing environment, instead of using the yaml file.
conda create --clone ORIGIN_CONDA_ENV --prefix=/PATH/TO/NEW_CONDA_ENV
```

**Note:** These steps might not work as expected when using a shared target folder (prefix). The env could be created
using the regular location first for testing purposes and generating the spec file.

### Pack the environment
Pack the whole environment into a compressed tar file, then decompress the file and unpack it
when needed.

```shell
# Install "conda-pack". This can be done with either the same or a new env.
conda install conda-pack -c conda-forge

# Pack the environment by specifying the location it's stored.
# conda pack -p /PATH/TO/ORIGINAL_ENV_DIR/
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

