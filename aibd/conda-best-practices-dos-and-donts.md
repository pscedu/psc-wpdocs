
Please do:

* Use a compute node for the installation process, so you can make use of the bandwidth and the I/O available there, but
  be sure to request more than an hour for your session, so the progress is not lost if there are a lot of packages to install.
* Specify all packages at once when installing packages, so Conda doesn't have to run the full
  set of compatibility validations multiple times.
* Make sure that the destination folder for the packages is set to use the $PROJECT disk space, as the home
  folder ($HOME) quota is low and the envs and the cache are big.
* Try to always use Conda to install packages and not pip. Only use pip when Conda is not an option for installing those
  required packages.
* Try to only use the default Conda channel of the most popular and reputable ones. Install packages using pip if
  needed.
* Export the list of installed packages as soon as you confirm that an environment is working as expected. Set a
  mnemonic file name for that list, and save it in a secure place, in case you need to install the environment from
  PROJECT again.
* Consider renaming the Conda directory to something else if you think an environment is not going to be used anymore, but you are not completely sure. Compress/tar the contents, in case you need them again at some point.

Please don't:

* Use additional Conda channels unless you know they are trustworthy.
* Install packages unless you are going to use them.
* Create multiple copies of the same environment, or at least tar the dir so there are less files using the file
  system.
