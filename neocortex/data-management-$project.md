

### $PROJECT
Your $PROJECT directory is at /ocean/projects/*groupname*/*username*, where *groupname* is the Unix group id associated with your allocation, and *username* is your PSC username. 

This storage space is for storing large amounts of data and bigger files (>25GB) that are not required by the other team members. This space is the main userspace (private to each user) that charges against the group quota instead of the limited personal quota.

If you have access to multiple projects on PSC systems, please make sure to execute the `newgrp` command before using the $PROJECT variable, as described in the [Accounts section](https://www.psc.edu/resources/neocortex/docs/accounts).

<div class="warning">
<blockquote>
  <strong>Warning</strong>
  <p>This storage space is subject to the <a href="https://www.psc.edu/resources/bridges-2/user-guide/#inode-quota">Bridges-2 inode quota</a>.</p>
</blockquote>
</div>

##### $PROJECT/../shared
This is a space inside the project that is writable by everyone in your specific group. This location is created by default, and it is the same type of space that was available on Phase 0 with the P## folders (also referred to as team_folder).

This directory can also be referred to as <code>/ocean/projects/<em>groupname</em>/shared</code>, where *groupname* is the Unix group id associated with your allocation.

  <blockquote>
    <strong>Note</strong>
    <p>The $HOME and $PROJECTS spaces are persistent, but the /local{1,2,3,4} spaces (/local1/, /local2/, /local3/, /local4/) are more ephemeral.</p>
  </blockquote>


