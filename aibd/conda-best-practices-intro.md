Conda is a Python package distribution that allows you to set up development environments and handle dependencies for a
curated set of packages that are widely used for Data Science and Machine Learning tasks. It is similar to using pip and
virtual environments, but differs by providing a way to select performance-optimized packages (optimized
for CPU or GPU processing elements) to be installed based on the requirements of your task.

Additionally, using Conda (be it the full distribution "anaconda" or just the bare system "mini-conda"), allows
you to use pip or virtualenv if needed.

Other advantages of using Conda include:

* Access to performance optimized packages (MKL)
* Precompiled versions of packages, such as TensorFlow and PyTorch
* Package compatibility lists, so installed packages are compatible with each other
* Self-contained environments that can be maintained and used individually

More information can be found on [the Anaconda website](https://www.anaconda.com/products/individual).

The [main difference between Conda and pip](https://www.anaconda.com/blog/understanding-conda-and-pip) is that Conda
installs (any) software binary (no compilation required) while Pip compiles sources or wheels for (only) Python
packages.

This document explains these best practices for using Conda:
* [Use $PROJECT space for your conda folder](#use-project-space-for-your-conda-folder)
* [Load and activate the Conda modules](#load-and-activate-the-conda-modules)
* [Create a new environment](#create-new-environment)
* [Using channels](#using-channels)
* [Create a backup of your environment](#create-a-backup-of-your-environment)
* [Use different directories when needed](#use-different-directories-when-needed)
* [General dos and don'ts](#dos-and-donts)
