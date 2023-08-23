## Current Known Issues (technical)
These are some technical issues our team is working on right now. We will update this guide as soon as they are resolved, adding a "SOLVED" to the title every time the changes are reflecting on Neocortex.
<h3>Error 1 (<span class="green-success">SOLVED 2021-02-14</span>): /usr/bin/id: cannot find name for group ID</h3>

  **Error message:** 
  ```
  /usr/bin/id: cannot find name for group ID 24809 /usr/bin/id: cannot find name for user ID 74742 
  [I have no name!@sdf-1 ~]$
  ```
  **Triggered:** when running the command for starting an interact session from the Neocortex login node. Any of the following: <br />
  ```
  srun --nodes=1 -w sdf-1 --pty bash -i
  ```
  ```
  srun --nodes=1 --pty bash -i
  ```
  ```
  srun --pty bash -i
  ```
  **Cause:** this was a network communication issue for reaching the LDAP server.

### Error 2 <span style="color:green;">(SOLVED 2021-02-14)</span>: More processors requested than permitted
**Error message:**
```
More processors requested than permitted
```
**Triggered:** when running the command for starting a singularity shell form the Neocortex login node:
```
srun --pty --nodelist sdf-1 --cpus-per-task=28 --kill-on-bad-exit singularity shell --cleanenv -B /local1/cerebras/data,/local2/cerebras/data,/local3/cerebras/data,/local4/cerebras/data,/jet/home/PSC_USERNAME/modelzoo /local1/cerebras/cbcore_latest.sif
```
### Error 3 (SOLVED 2021-02-14): Could not lookup the current user's information
**Error message:**
```
WARNING: Could not lookup the current user's information: user: unknown userid 73858
FATAL:  Couldn't determine user account information: user: unknown userid 73858
srun: error: sdf-1: tasks 0-1: Exited with exit code 255
srun: launch/slurm: _step_signal: Terminating StepId=332.0
```
**Triggered:** when running the command for starting a singularity shell form the Neocortex login node:
```
srun --pty --nodelist sdf-1 --cpus-per-task=1 --kill-on-bad-exit singularity shell --cleanenv -B /local1/cerebras/data,/local2/cerebras/data,/local3/cerebras/data,/local4/cerebras/data,/jet/home/PSC_USERNAME/modelzoo /local1/cerebras/cbcore_latest.sif
```
**Cause:** this was a network communication issue for reaching the LDAP server.

### Error 4 (SOLVED 2021-02-15): Slowness when using files on Jet or Ocean 
**Error message:** No error messages are shown, but it will take a long time for any operations to start or finish.

**Cause:** this was a network communication issue for reaching the Jet and Ocean filesystem servers across multiple InfiniBand interfaces as opposed to using a single interface.

### Error 5: Failure to login into https://portal.neocortex.psc.edu/home
**Error message:** the page takes a minute to load and an error page from the webserver is shown.

**Cause:** it seems the authentication is timing out. Probably something changed when the migration from Bridges to Bridges2 started.

**Workaround:** try logging in once again.
* [Continuous development]

