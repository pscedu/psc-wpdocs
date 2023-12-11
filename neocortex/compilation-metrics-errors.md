
  
### Common Errors
* **FATAL: could not open image /path/to/cbcore-latest.sif**
   * Make sure you are pointing to the correct container location. The full path is “`/ocean/neocortex/cerebras/cbcore-latest.sif`".
* **srun: error: Unable to allocate resources: Invalid qos specification**
   * This will usually happen when the project allocation id in Neocortex has not been specified, or a different one (the default one) was used. To prevent this, make sure to add the `--account cis210012p` to the `interact` command, or to set your primary group with `newgrp cis210012p`.
* **404 Error when accessing the GitHub Cerebras/modelzoo repository**
   * Please make sure you are logged into GitHub. If you are already logged in and it doesn’t work, please check your email and make sure to accept the invite to join the repository. If you have not gotten an invite, please let us know by emailing us.
* **Can't login to neocortex.psc.edu**
   * Please make sure you are using the correct username. If needed, please visit the [PSC web password change site](https://apr.psc.edu/).
* **Permission denied error when trying to copy the code from the $CEREBRAS_DIR location**
   * Make sure your primary group is set correctly. You can check that by running the `groups` command and checking that “cis210012p” is showing first on the left. If it’s not showing in the first position, run the following command: `newgrp cis210012p`. If it’s not showing at all, please make sure you logged in using the correct account, or email us if everything looks good on your end.
* **Warning!** ***HDF5 library version mismatched error***
   * Please make sure you are trying to run the code using the software environment inside the container and that you are not using any other external software stacks, such as an external module or Conda environment.
* **Can’t login to the Neocortex portal page**
   * Please make sure that you are using your PSC Neocortex credentials. If you are still getting authentication errors but your account is new, please try again the next day since the initial process takes a while to be reflected all throughout Neocortex. If you have been using your Neocortex account for some days now but the Neocortex Portal login doesn’t work, please email us at neocortex@psc.edu.
