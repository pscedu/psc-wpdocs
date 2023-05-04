## Use different directories when needed

Similar to when the .conda symlink from HOME to PROJECT was created in the "Before you start" section (and the
"[Storing your Anaconda environments](http://www.psc.edu/resources/software/anaconda)" of the Bridges User Guide), it is
possible to
have multiple directories for the different conda environments, and use that to have a way to archive the different
environment configurations across time.

For example, if existing environments are not going to be used for a while, a new conda
project could be created as a way to make sure those existing environments will be safe from any modifications.

Please have in mind that the path names in the environments should not be changed. Thus the name used the first time
should be kept unchanged over time, and names should be restored to their original when the directories have been
renamed.

### Create a new conda directory

Example: Switch from an existing conda directory to a new one.

```shell

# Rename the old directory. This is the one that should be returned to the original directory name if needed.
mv $PROJECT/.conda $PROJECT/conda_OLD_PROJECT_NAME_ARCHIVE  

# Create a new directory for conda under PROJECT.
mkdir $PROJECT/.conda

```

### Create environments on shared locations

Another approach for using different directories is to specify a prefix to denote where in the filesystem a Conda
environment should be set.

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

