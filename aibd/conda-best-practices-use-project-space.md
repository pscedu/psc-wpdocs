Be aware that each different Conda environment will be created using
an individual environment folder. Even if there is a cache folder for downloaded packages, Conda will still have to
download any dependencies that are missing. That uses a lot of disk space and will fill your $HOME quota quickly. 

We recommend that you store those files in your $PROJECT space instead.

To do this, create a symlink to your $PROJECT space. If you already have a conda folder in $HOME, you must move it to $PROJECT first.

```shell
# If you already have a conda folder, move it to $PROJECT.
mv ~/.conda $PROJECT/ 

# Create a symlink from your HOME to the moved folder.
ln -s $PROJECT/.conda ~/.conda
```

