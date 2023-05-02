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

