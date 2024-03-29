There are multiple ways to set up custom development environments on Bridges-2. 
They include:

* [Using Singularity containers](#using-singularity-containers)
* [Using predefined Bridges-2 environment modules](#using-predefined-bridges-2-environment-modules)
* [Using a conda module environment](#using-a-conda-module-environment)
* [Using the default Python installation](#using-the-default-python). This method is not supported and not recommended unless you are familiar with virtualenvs and pip.

We recommend using [Singularity](https://sylabs.io/singularity/) containers, especially the ones from the
the [NVIDIA NGC catalog](https://catalog.ngc.nvidia.com/) if there is one that fits your needs, as those are
curated by NVIDIA and optimized for their GPUs. Otherwise, try using the predefined
Bridges-2 modules, or creating a custom Anaconda environment.

