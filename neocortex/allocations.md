# Allocations
## Call For Participation (CFP)
For the duration of the CFP, every project has been granted an allocation to be shared among team members. The resources that are initially enabled **do not** include access to the SDF or CS-2s, as those are enabled only after finishing the initial training material and submitting the Key Metrics of Interest form.
The grants include the following resources:

**Neocortex**
* Neocortex SDF - 1000 core-hours
* Neocortex CS-2 - 100 CS-2 hours

**Bridges-2**
* Bridges-2 Regular Memory - 50000 core-hours
* Bridges-2 Extreme Memory - 1000 core-hours
* Bridges-2 GPU-AI - 2500 GPU-hours
* Bridges-2 Ocean - 1000 GB
  
## How to check your allocation's SLURM charge id

You can use the `projects` or `groups` command to find the SLURM charge id for your allocation from Neocortex (or Bridges-2).

`$ projects`

`$ groups`

Please keep your charge id handy as you will need it for accessing your project folder.

## Getting a grant extension
If you need more than the initial resources awarded, you are encouraged to apply for an extension.

Please send a request to [neocortex@psc.edu](mailto:neocortex.edu) including a detailed summary of how the resources were used and the plan for the additional resources requested.

## Managing multiple grants
**Changing your SLURM charge id**

If you are part of multiple grants, then make sure your SLURM charge id is correctly set before beginning work. To find your default charge id, use the `projects` command. To see all of your groups:

<pre>change_primary_group -l</pre>

To **temporarily** change your charge id for the current login session, please run the following command:

<pre>newgrp <em>charge-id</em></pre>

The effects of this command take place immediately. Your charge id will revert to your default charge id upon logout.


To **permanently** make a charge id your default charge id, please run the following command:
<pre>change_primary_group <i>charge-id</i></pre>

It may take up to an hour for the default charge id change to propagate. The change will last across login sessions.

