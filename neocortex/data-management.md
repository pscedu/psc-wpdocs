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

<div class="warning">
  <blockquote>
    <strong>Warning</strong>
    <p>This storage space is subject to the <a href="https://www.psc.edu/resources/bridges-2/userguide#inode-quota">Bridges-2 inode quota</a></p>
  </blockquote>
Your $HOME directory is <code>/jet/home/<i>username</i></code>, where *username* is your PSC username. This is your personal storage space and has a cap of 25 GB. This should be used for personal files only, such as system configurations or maybe private files like experimental Conda environments.

<div class="danger">
  <blockquote>
    <strong>Danger</strong> 
<p>Maxing out your $HOME quota (storing >25GB under $HOME) will lock you out of the system, as you might not be able to login anymore.
  </blockquote>
</div>


