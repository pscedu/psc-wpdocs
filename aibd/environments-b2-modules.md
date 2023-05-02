
### Using predefined Bridges-2 environment modules 

PSC has built some environments which provide a rich, unified,
Anaconda-based environment for AI, Machine Learning, and Big Data
applications. Each environment includes several popular AI/ML/BD
packages, selected to work together well.

These environments are built for the GPU nodes on Bridges-2. Be sure
to use one of the GPU partitions. See the [Bridges-2 User
Guide](https://www.psc.edu/resources/bridges-2/user-guide#partitions)
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

