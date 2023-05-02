Bridges-2 supports running Singularity containers, allowing
encapsulated environments to be built from scratch. You cannot use
Docker containers on Bridges-2, but you can download a Docker container
and convert it to Singularity format. Examples are given below
showing how to convert containers.

There are many containers for AI/BD applications already installed on Bridges-2 in directory `/ocean/containers/ngc`. These are already in Singularity format and ready to use. You can use one of these containers or you can create a container of your own.

### 
<table>
<thead>
<tr>
<th>When to use Singularity containers</th><th>Advantages</th><th>Disadvantages</th>
</tr>
</thead>
<tbody>
<tr>
<td style="vertical-align:top;">
<ul>
<li>When a pre-configured Docker container already exists
<li>When superuser privileges are needed or setting up the environment, like installing an OS library
</li>
</ul>
</td>
<td style="vertical-align:top;">
<ul>
<li>Offers flexibility for installing more libraries and software
<li>Allows reproducible results since the container can be reused across hosts
<li>Can be easily placed and used from I/O optimized storage
</li>
</ul>
   </td>
   <td style="vertical-align:top;">
<ul>
<li>The singularity module must be loaded every time.</li>
<li>Adds another layer of complexity when developing and troubleshooting code
<li>Uses at least a couple of gigabytes of disk space
<li>Singularity and Docker are not 100% compatible but only Singularity is available on Bridges-2
<li>Extra steps are required for modifying containers
</li>
</ul>
   </td>
   </tr>
   </tbody>
   </table>

#### Pulling and converting Docker containers to Singularity

You can pull a Docker container into Bridges-2 and convert it to
Singularity format with the <code>Singularity pull</code> command. 

**Note** This should be done in an interactive session on Bridges-2.  See the [Interactive sessions section in the Bruidges-2 User Guide](https://www.psc.edu/resources/bridges-2/user-guide#interactive-sessions) for more information.

To pull a container from [DockerHub](https://hub.docker.com/) and convert it to Singularity:
```shell
interact  # Start an interactive session on a Regular Memory node.
singularity pull --disable-cache docker://alpine:latest  # Pull the latest "alpine" container from DockerHub.
```
You should now have a ".sif" file. That's the container converted into Singularity Image Format (SIF).


To pull a container from the [NVIDIA NGC library](https://catalog.ngc.nvidia.com/) and convert it to Singularity:
```shell
interact  # Start an interactive session on a Regular Memory node.
singularity pull --disable-cache docker://nvcr.io/nvidia/pytorch:22.12-py3` # Pull the 22.12 PyTorch container from NGC.
```
You should now have a ".sif" file. That's the container converted into Singularity Image Format (SIF).

These examples pulled a container from DockerHub, using "docker://" as the origin string in the <code>singularity pull</code> command, but there are other
valid container origin points to pull containers from:

- The [Singularity Container Library](https://cloud.sylabs.io/library)
  * Use "library://" as the origin string in the <code>singularity pull</code> command
- [Singularity Hub](https://singularity-hub.org/)
  * Use "shub://" as the origin string in the <code>singularity pull</code> command

#### Using a Singularity container

Once you have a Singularity container, start an interactive session on
Bridges-2 and start your container. See the section on [interactive
sessions in the Bridges-2 User Guide](https://www.psc.edu/resources/bridges-2/user-guide#interactive-sessions)
 for details on the interact command.

```shell
interact   # Start an interactive session. 
singularity shell --nv /path/to/CONTAINER.sif
```

[More information on using Singularity at PSC can be found
in the PSC Singularity documentation.](https://www.psc.edu/resources/software/singularity/)

##### Example: Use a container already on Bridges-2
```shell
interact  # Start an interactive session.
# The path to the container is long. Let’s use a variable for readability.
CONTAINER=/ocean/containers/ngc/tensorflow/tensorflow_latest.sif

# Pull the container. Specify no cache dir to be used so only the local disk is used.
# Then use pip freeze to confirm what is installed
singularity exec --nv ${CONTAINER} pip freeze | grep tensorflow    
    tensorflow @ file:/// [...] 2.10.1 [...]
    tensorflow-addons==0.11.2
    tensorflow-datasets==3.2.1
    tensorflow-estimator==2.10.0
    tensorflow-metadata==1.12.0
    tensorflow-nv-norms @ file:/// [...]
    tensorflow-probability==0.11.1
```

##### Example: Pull a container into Bridges-2

When the container you need is not present on Bridges-2 already, you
can pull one from a given URI. Run the following commands in an interactive session to pull a
container to Bridges-2. See the section on [interactive
sessions in the Bridges-2 User Guide](https://www.psc.edu/resources/bridges-2/user-guide#interactive-sessions)
 for details on the interact command.

This example pulls a container from Docker
Hub and then saves it to $PROJECT for later use.

```shell
# Start a job for building the container faster.
interact

# Change to the high-speed flash storage folder.
cd $LOCAL

# Pull the external container by specifying the origin right before the tag.
# i.e. for pulling Docker containers, use “docker://”
singularity pull --disable-cache docker://USERNAME/CONTAINER

# Finally, since the $LOCAL storage is fast but ephemeral, copy the container back to your file space.
cp CONTAINER.sif $PROJECT/ # Or $HOME

```

