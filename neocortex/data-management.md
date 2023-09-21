## Data Management and Storage 
This section talks about the various storage spaces on Neocortex and how to use them.

### In this section
* **Common storage spaces**
   * [$HOME](#$home)
   * [$PROJECT](#$project)
   * [/local](#local)
* [Reference resources](#reference-resources)
* [Transferring data](#transferring-data)

  
### Common storage spaces
#### $HOME
Your $HOME directory is <code>/jet/home/<i>username</i></code>, where *username* is your PSC username. This is your personal storage space and has a cap of 25 GB. This should be used for personal files only, such as system configurations or maybe private files like experimental Conda environments.

**Note:** The $HOME and $PROJECTS spaces are persistent, but the /local{1,2,3,4} spaces (/local1/, /local2/, /local3/, /local4/) are more ephemeral.

**Danger:** 
Maxing out your $HOME quota (storing 25GB+ under $HOME) will lock you out of the system, and you might not be able to login anymore.

