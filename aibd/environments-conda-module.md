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

