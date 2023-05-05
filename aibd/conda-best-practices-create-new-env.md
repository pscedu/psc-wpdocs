

There are two ways to create a new environment: use the `conda create` command or the `conda env create` command.

### Use the "conda create" command 
You will create a blank environment if no packages are specified, or you 
   can install a list of packages (and their dependencies) by passing those as an argument.

```shell
    # Consider renaming the conda directory to something else     
    # This will create an empty environment, but it's not recommended as is because the command is not specific.
    conda create -n YOUR_ENV_NAME python
    
    # The packages to install should be specified when creating the environment.
    conda create -n YOUR_ENV_NAME python PACKAGE1
    
    # The specific Python version can also be specified.
    conda create -n YOUR_ENV_NAME python=M.N PACKAGE1
    
    # Example: install the latest TensorFlow 2 that is compatible with Python 3.8
    conda create -n YOUR_ENV_NAME python=3.8 tensorflow=2 scipy
```    
   
### Use the "conda env create" command
The `conda env create` command uses a structured yaml file for installing an environment based on the
complete list of packages generated from a different conda environment. The file extension is important and it should be "yaml". 
Using a ".txt" extension triggers errors even if the content was in yaml format.

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

__Note:__ The syntax for specifying a package version on Conda is different than the syntax for pip. 
For  example, two equal signs are used with pip for specifying the version to use, but with Conda, one equal sign is
required.

For example:
```shell
pip install PACKAGE_NAME==VERSION_NUMBER
# Or
conda install PACKAGE_NAME=VERSION_NUMBER
```
 
