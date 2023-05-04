## Using channels

A package may not be available in the default Conda channel. In that case, it's possible to
still install the package by specifying the name of the channel that has it available. However, please make sure that
it's actually required to do it that way, since it's also possible to install packages using pip directly, even if that
means compiling the specific packages.

```shell
# conda create -n YOUR_ENV_NAME SPECIAL_PACKAGE -c CHANNEL_NAME
conda create -n pytorch -c pytorch
```

Finally, make sure that the channel you are trying to use is an entity you can trust, since ill-intended individuals
could make modified packages available in an attempt to get researchers to install those Trojan-horse packages, providing
them with a way to access and infect even more HPC environments.

