<h3 name="custom-env">Custom conda environments with OnDemand</h3>

You can  use the Python “ipykernel” package to create a definition file for  Jupyter Notebook which
uses a custom Python installation rather than the default one. After creating that file and launching Jupyter Notebooks via
OnDemand, you can use your custom environment.

The steps to do this are:
<ol>
    <li>Install “ipykernel”</li>
    <li>Create the custom Jupyter Notebook kernel</li>
    <li>Start the custom Jupyter Notebook kernel</li>
</ol>

<h4>Install “ipykernel”</h4>

This process can be performed with a custom conda environment, with the Python installation in the Bridges-2 nodes, or
with any other Python installation available; the important thing is to run it from the Python environment that will 
be used with OnDemand.

The “ipykernel” package must be available in this environment to generate the custom kernel. The
“ipykernel” package can be removed after that.


<h5>Load and activate the Anaconda3 module</h5>

In [an interactive session on Bridges-2](https://www.psc.edu/resources/bridges-2/user-guide-2/#interactive-sessions), load and activate anaconda3. 

```
module load anaconda3
conda activate # source /opt/packages/anaconda3/etc/profile.d/conda.sh
```


<h4>Add ipykernel to an environment</h4>

Create a new environment and install the “ipykernel” package along with any other packages you might need, or install “ipykernel” to any existing Conda environment you have.

<h5>To create a new environment which includes ipykernel</h5>

Use a command like

```
conda create --name ENVIRONMENT_NAME ipykernel
conda activate ENVIRONMENT_NAME
```


<h5>To add ipykernel to an existing environment</h5>


Use a command like one below, depending on your specific case:

If you are using conda:
```
conda install ipykernel
```

If you are NOT using conda, but in a Python environment in which you have write permission
```
python3 -m pip install ipykernel
```

To use the default Bridges-2 Python installation or modules
```
python3 -m pip install ipykernel --user
```

<h4>Create the custom Jupyter Notebook kernel</h4>

Run “ipykernel” to create the custom Jupyter Notebook kernel, so that the Python installation is mapped using
a definition file. This can be done by either running the “ipykernel” module from the environment that is going to be
used, or by running the module while specifying the full path to reach that environment location.

**Note:** The environment must be activated before running ipykernel.

After running this command, a file is created  which specifies the location of the Python environment. That file will be created under one of the following locations. 

```
$HOME/.local/share/jupyter/kernels/ENVIRONMENT_NAME
$HOME/.ipython/kernels/ENVIRONMENT_NAME
```
The output of the command shows the location of this file.

```
(base) [user@r001 custom-kernel]$ conda activate NEW_ENV
(NEW_ENV) [user@r001 custom-kernel]$

(NEW_ENV) [user@r001 custom-kernel]$ python3 -m ipykernel install --user --name NEW_ENV --display-name "PYTHON-ENV-NAME"
Installed kernelspec NEW_ENV in /jet/home/user/.local/share/jupyter/kernels/new_env
(NEW_ENV) [user@r001 custom-kernel]$
```

**Note:** The “ipykernel” can be removed from the environment after the custom kernel is created.

<h4>Start the custom Jupyter Notebook kernel</h4>

Now you are ready to start your custom Jupyter notebook kernel from an interactive session in OnDemand.

<h5>Log in to OnDemand</h5>
In a browser window, go to ondemand.bridges2.psc.edu and log in with your PSC credentials.

<h5>Request an interactive session for your Notebook.</h5>

Navigate to **Interactive Apps** > Jupyter Notebook

![OnDemand at Bridges-2. Interactive Apps tab, Jupyter Notebook item](https://user-images.githubusercontent.com/30911386/161278718-2a29df90-cd2d-4b08-a986-58951b2e714b.png)

A new screen will open which allows you to specify the paramters for your interactive session (number of hours, nodes, partition, etc.) Set the parameters for your session as needed.

Click the blue Launch button. You may have to wait for resources to be allocated to you. Once your session has started, click the blue **Connect to Jupyter** button.

At this point, you can start a new notebook or open an existing one.

<h5>Start a new Notebook</h5>


Under the **New** dropdown in the upper right, choose the name of the new environment that you created.

![OnDemand at Bridges-2. Jupyter Notebook is running, and a new Notebook is being started while selecting the custom Python environment just created](https://www.psc.edu/wp-content/uploads/2022/03/image2.png)

Your new Notebook will open.

**Note:** For installing new packages, you will have to do it from the terminal in the interactive session and NOT from the Jupyter notebook itself
as it will try to use the base different conda binaries and not the ones you set in the new custom environment kernel.



<h5>Use an existing Notebook</h5>


If you already have a notebook, find it in the file list and click on it to open it.

Change the Python environment to use by navigating to **Kernel > Change kernel**.  Choose the new Python environment to use.


![change-kernel](https://user-images.githubusercontent.com/30911386/161283360-69c822bf-f438-46e4-b58b-411df42ce794.jpeg)


**Note:** For installing new packages, you will have to do it from the terminal in the interactive session and NOT from the Jupyter notebook itself
as it will try to use the base different conda binaries and not the ones you set in the new custom environment kernel.


<h4>Additional content: using Python from Singularity containers</h4>


Similar to the process described
on  [https://stackoverflow.com/questions/63702536/jupyter-starting-a-kernel-in-a-docker-container](https://stackoverflow.com/questions/63702536/jupyter-starting-a-kernel-in-a-docker-container), a Python installation inside a Singularity container can be used from Jupyter Notebook as well, although the process
is somewhat manual for now.

Create a new directory under `$HOME/.local/share/jupyter/kernels/` and add a kernel.json file 
there with the commands needed for Singularity to start the python binary it has inside.

Example:

```
mkdir -p $HOME/.local/share/jupyter/kernels/tensorflow_latest/
vim $HOME/.local/share/jupyter/kernels/tensorflow_latest/kernel.json
```

```
{
    "argv": [
        "/usr/bin/singularity",
        "exec",
        "--bind",
        "/ocean,{connection_file}:/connection-spec",
        "/ocean/containers/ngc/tensorflow/tensorflow_latest.sif",
        "python",
        "-m",
        "ipykernel_launcher",
        "-f",
        "/connection-spec"
    ],
    "display_name": "tensorflow_latest",
    "language": "python"
}
```

Then start Jupyter Notebook and select the newly created kernel. The libraries inside the container should be there.
