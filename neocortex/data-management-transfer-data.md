

### Transferring Data
#### How to move datasets into the Neocortex system and back
You can use the Neocortex Data Transfer Nodes (DTN) for transferring data to and from the Neocortex system. You can use rsync (preferred) or sftp commands for the same. Using scp is not recommended.

Every grant folder <code>/ocean/projects/<em>groupname</em>/</code> has a shared file space (for everyone in the group to use) and personal user space. To use the shared space for storing datasets and files you want to share with your team, look for the shared folder under your group folder ($PROJECT/../shared/).

There are three ways to transfer files to/from Neocortex: rsync (preferred), sftp or scp (not recommended). Below are some sample commands you can refer to.

**rsync**
<pre>rsync -PaL --chmod u+w /local-path/to/dataset <em>username</em>@data.neocortex.psc.edu:/ocean/projects/<em>groupname</em>/shared/</pre>
where *groupname* is your grant id and *username* is your PSC username.

Note that by default, rsync will not copy older files with the same name in place of newer files in the target directory.

You can find the grant id by running the `projects` command. Please refer to the [Account administration section of the Bridges-2 User Guide](https://www.psc.edu/resources/bridges-2/user-guide/#account-administration) for more information.

**sftp**

sftp file transfer is also supported. For more details, please visit the [Transferring files section of the Bridges-2 User Guide](https://www.psc.edu/resources/bridges-2/user-guide/#transferring-files).
