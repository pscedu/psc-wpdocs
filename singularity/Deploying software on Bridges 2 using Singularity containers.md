# Deploying software on Bridges 2 (B2) using Singularity containers

## Summary
This document describes best practices when deploying software on Bridges2 using Singularity containers. This document mainly applies to Biomedical apps.

## Pre-requisites
- [ ] Account on GitHub
- [ ] Be part of the [PSC Organization](https://github.com/pscedu) on GitHub
- [ ] Account on B2
- [ ] Be a member of the `pscstaff` group
- [ ] Elevated access to run Singularity if you want to build containers on B2. If not, then have an account on SyLabs.io to build containers remotely.

## In a nutshell
End users will be loading applications into their workspace as they would any other tool. In the background, the modulefile will be calling scripts that in turn will be calling the containers.

If done properly, the end user will not be aware the tool was deployed in a Singularity container.

## Procedure
### Building the scripts, modulefiles and Singularity images

* Create a GitHub repository that will hold the recipes and scripts and add the repository to the `Biomedical Applications` team. For example, the repository below will be used in this document.

[![GitHub Card](https://i.imgur.com/gskffmP.png)](http://www.github.com/pscedu/singularity-blast)

* Create a folder within the repo that matches the version you want to install. There should be one folder per version in the repo. Avoiding multiple releases should make maintenance easier in the long run. For example, in the `singularity-blast` repository we have created two folders for versions `2.9.0` and `2.11.0`.

![](https://i.imgur.com/iiNnjj4.png)

* Search public registries for an existing Singularity image, for example [BioContainers](https://biocontainers.pro/), [SyLabs.io](https://sylabs.io/), [Quay.io](https://quay.io/), [Docker Hub](https://hub.docker.com/) or any other public registry.
* If an image exists, then create a script that pulls the image, named `pull.sh`,  and add it to the repo in the corresponding folder. For example, you can find an image for `BLAST 2.9.0` on Galaxy Depot and hence the `pull.sh` might look like

```
#!/bin/bash

singularity pull https://depot.galaxyproject.org/singularity/blast%3A2.9.0--pl526he19e7b1_7
```

* If an image does not exist, then create a Singularity definition file for the app and add to the repo in the corresponding folder. For example, for `BLAST 2.11.0` the definition file is 

```
Bootstrap: docker
From: debian:bullseye

%labels
    MAINTAINER icaoberg
    EMAIL icaoberg@psc.edu
    WEBSITE http://gitub.com/pscedu/singularity-blast
    COPYRIGHT Copyright © 2021 Pittsburgh Supercomputing Center. All Rights Reserved.    
    VERSION 2.11.0

%post
    apt update
    apt install -y ncbi-blast+
```

:::warning
:warning: Notice a COPYRIGHT string has been added to the definition file
:::

* Create a modulefile named `modulefile.lua`and add to the repo in the corresponding folder. For example, for `BLAST 2.11.0` the modulefile is 

```
--
-- BLAST+ 2.11.0 modulefile
--
-- "URL: https://www.psc.edu/resources/software"
-- "Category: Biological Sciences"
-- "Description: BLAST (basic local alignment search tool) is an algorithm and program for comparing primary biological sequence information, such as the amino-acid sequences of proteins or the nucleotides of DNA and/or RNA sequences."
-- "Keywords: singularity bioinformatics"

whatis("Name: BLAST+")
whatis("Version: 2.11.0")
whatis("Category: Biological Sciences")
whatis("URL: https://www.psc.edu/resources/software")
whatis("Description: BLAST (basic local alignment search tool) is an algorithm and program for comparing primary biological sequence information, such as the amino-acid sequences of proteins or the nucleotides of DNA and/or RNA sequences.")

help([[
Description
-----------
BLAST (basic local alignment search tool) is an algorithm and program for comparing primary biological sequence information, such as the amino-acid sequences of proteins or the nucleotides of DNA and/or RNA sequences.

To load the module type

> module load BLAST/2.11.0

To unload the module type

> module unload BLAST/2.11.0

Documentation
-------------
https://www.ncbi.nlm.nih.gov/books/NBK279690/

For help, type for example

> blastn -help

Tools included in this module are
* aalookup_unit_test
* msa2pssm_unit_test
* aascan_unit_test
* ntlookup_unit_test
* align_format_unit_test
* ntscan_unit_test
* bdbloader_unit_test
* optionshandle_unit_test
* bl2seq_unit_test
* phiblast_unit_test
* blastdb_aliastool
* prelimsearch_unit_test
* blastdbcheck
* project_tree_builder
* blastdbcmd
* psibl2seq_unit_test
* blastdbcp
* psiblast
* blastdb_format_unit_test
* psiblast_iteration_unit_test
* blastdiag_unit_test
* psiblast_unit_test
* blastengine_unit_test
* pssmcreate_unit_test
* blastextend_unit_test
* pssmenginefreqratios_unit_test
* blastfilter_unit_test
* querydata_unit_test
* blast_formatter
* queryinfo_unit_test
* blast_format_unit_test
* redoalignment_unit_test
* blasthits_unit_test
* remote_blast_unit_test
* blastinput_unit_test
* rpsblast
* blastn
* rpstblastn
* blastoptions_unit_test
* rps_unit_test
* blastp
* scoreblk_unit_test
* blast_services_unit_test
* search_strategy_unit_test
* blastsetup_unit_test
* seedtop
* blast_unit_test
* segmasker
* blastx
* seqdb_demo
* convert2blastmask
* seqdb_perf
* datatool
* seqdb_unit_test
* deltablast
* seqinfosrc_unit_test
* delta_unit_test
* seqsrc_unit_test
* dustmasker
* setupfactory_unit_test
* gapinfo_unit_test
* split_query_unit_test
* gencode_singleton_unit_test
* subj_ranges_unit_test
* gene_info_reader
* tblastn
* gene_info_unit_test
* tblastx
* hspfilter_besthit_unit_test
* tracebacksearch_unit_test
* hspfilter_culling_unit_test
* traceback_unit_test
* hspstream_unit_test
* uniform_search_unit_test
* legacy_blast.pl
* update_blastdb.pl
* linkhsp_unit_test
* version_reference_unit_test
* makeblastdb
* windowmasker
* makembindex
* makeprofiledb
* writedb_unit_test
]])

local package = "BLAST"
local version = "2.11.0"
local base    = pathJoin("/opt/packages",package,version)
prepend_path("PATH", base)
```

:::warning
:warning: Since the modulefile was build manually, then it is important that you follow the official template at the time of creation
:::

* It is very important that the modulefile is properly documented.
* Any changes to the modulefile template should be consulted with superiors.

## Add `build.sh` and `rbuild.sh` scripts
### `build.sh`
Add a script named `build.sh` that will be used to build the Singularity image locally. The file should remain the same in every reposity and the only line that should be different is the line holding the `IMAGE` variable. For example, for `BLAST 2.11.0` the file is

```
#!/bin/bash

# Copyright © 2021 Pittsburgh Supercomputing Center.
# All Rights Reserved.

IMAGE=singularity-BLAST-2.11.0.sif
DEFINITION=Singularity

if [ -f $IMAGE ]; then
	rm -fv $IMAGE
fi

sudo singularity build $IMAGE $DEFINITION

if [ -f $IMAGE ]; then
	exit 0
else
	exit 1
fi
```

### `rbuild.sh`
Add a script named `rbuild.sh` that will be used to build the Singularity image remotely. The file should remain the same in every reposity and the only line that should be different is the line holding the `IMAGE` variable. For example, for `BLAST 2.11.0` the file is

```
#!/bin/bash

# Copyright © 2021 Pittsburgh Supercomputing Center.
# All Rights Reserved.

IMAGE=singularity-BLAST-2.11.0.sif
DEFINITION=Singularity

if [ -f $IMAGE ]; then
	rm -fv $IMAGE
fi

singularity build --remote $IMAGE $DEFINITION

if [ -f $IMAGE ]; then
	exit 0
else
	exit 1
fi
```

## Add a script per binary in the container
For each binary in the container that you want to expose to the users, you will need to create a script that calls Singularity. For example, in `BLAST 2.11.0` there exists a binary called `blastn`, hence we will create a script named `blastn` that looks like

```
#!/bin/bash

VERSION=2.11.0
PACKAGE=BLAST
TOOL=blastn
DIRECTORY=$(dirname $0)

PERSISTENT_FILE_STORAGE=/ocean
if [ -d $PERSISTENT_FILE_STORAGE ]; then
	OPTIONS="-B $PERSISTENT_FILE_STORAGE"
fi

if [ -d /local ]; then
	OPTIONS=$OPTIONS" -B /local"
fi

singularity exec $OPTIONS $DIRECTORY/singularity-$PACKAGE-$VERSION.sif $TOOL "$@"
```

`BLAST` is not a good example in the sense that there are many scripts that will need to be created, i.e. 10+ scripts. To facilitate this process, some repositories have a `generate_scripts.sh` script that is used to generate the list of scripts.

## Add 'test.sh'
Add a file named `test.sh` that will be used to test the package. There should exist one file in each folder. For example, for `BLAST 2.11.0`, the file is empty and looks like

```
#!/bin/bash

echo "Nothing to see here... move along"
```

## Add `LICENSE`
Add a file named `LICENSE` to the top level of the repository. This file should remain the same in every repository.

```
Copyright © 2021, Pittsburgh Supercomputing Center. All Rights Reserved.

Permission to use, copy, and modify this software and its documentation without 
fee for personal use or non-commercial use within your organization is hereby
granted, provided that the above copyright notice is preserved in all copies and
that the copyright and this permission notice appear in supporting documentation.

Permission to redistribute this software to other organizations or individuals is not
permitted without the written permission of the Pittsburgh Supercomputing Center.
PSC makes no representations about the suitability of this software for any purpose.

It is provided "as is" without express or implied warranty.
```

## Building containers on Bridges2
### If you have elevated privileges to run Singularity
Make sure to run singularity builds as `sudo`, that’s all that matters.

:::warning
:warning: If you are constantly building containers, then run
```
singularity cache clean
```
often to clean cache
:::

### If you don’t have elevated privileges
If you do not have elevated privileges, you can still build the images remotely.

Follow these steps to do so
* Create an account on SyLabs.io.
* Click `Access Tokens` on the top-right menu

![](https://i.imgur.com/ILAwB6T.png)

* Click Create a `New Access Token`

![](https://i.imgur.com/HlKCHjo.png)

* Click `Copy token to Clipboard`

![](https://i.imgur.com/ztbbC8B.png)

* Login to Bridges2 and run the command

```
singularity remote login
```

* Paste the token and click `Enter`.

Just make sure to use the `--remote` flag when running singularity build.

:::warning
:warning: If you are constantly building containers remotely, make sure to erase them from your account to avoid running out of space.

If you are creating an image that copies a local file into the container, building the image remotely might not provide you with a solution.
:::

## Deploying software on B2
Once you have created all the files necessary for deployment copy over the files to B2.

### Singularity image and files
* First make sure the folder `/opt/packages/<package-name>` exists. Using the example above, make sure this folder exists

```
➜  pwd
/opt/packages/BLAST
```

if not, then create it. 

```
➜  pwd
/opt/packages/BLAST/2.11.0
```

* In this newly created folder copy over the Singularity image and all related scripts.

```
➜  ls
aalookup_unit_test        deltablast                      queryinfo_unit_test
aascan_unit_test          delta_unit_test                 redoalignment_unit_test
align_format_unit_test    dustmasker                      remote_blast_unit_test
bdbloader_unit_test       gapinfo_unit_test               rpsblast
bl2seq_unit_test          gencode_singleton_unit_test     rpstblastn
blastdb_aliastool         gene_info_reader                rps_unit_test
blastdbcheck              gene_info_unit_test             scoreblk_unit_test
blastdbcmd                generate_binaries.sh            search_strategy_unit_test
blastdb_convert           hspfilter_besthit_unit_test     seedtop
blastdbcp                 hspfilter_culling_unit_test     segmasker
blastdb_format_unit_test  hspstream_unit_test             seqdb_demo
blastdb_path              legacy_blast.pl                 seqdb_perf
blastdiag_unit_test       linkhsp_unit_test               seqdb_unit_test
blastengine_unit_test     makeblastdb                     seqinfosrc_unit_test
blastextend_unit_test     makembindex                     seqsrc_unit_test
blastfilter_unit_test     makeprofiledb                   setupfactory_unit_test
blast_formatter           msa2pssm_unit_test              singularity-BLAST-2.11.0.sif
blast_format_unit_test    ntlookup_unit_test              split_query_unit_test
blasthits_unit_test       ntscan_unit_test                subj_ranges_unit_test
blastinput_unit_test      optionshandle_unit_test         tblastn
blastn                    phiblast_unit_test              tblastx
blastoptions_unit_test    prelimsearch_unit_test          tracebacksearch_unit_test
blastp                    project_tree_builder            traceback_unit_test
blast_report              psibl2seq_unit_test             uniform_search_unit_test
blast_services_unit_test  psiblast                        update_blastdb.pl
blastsetup_unit_test      psiblast_iteration_unit_test    version_reference_unit_test
blast_unit_test           psiblast_unit_test              windowmasker
blastx                    pssmcreate_unit_test            writedb_unit_test
convert2blastmask         pssmenginefreqratios_unit_test
datatool                  querydata_unit_test
```

### Add modulefile
The modulefile will live in `/opt/modulefiles`. However, it is not our responsibility to add the modulefiles since these need to be reviewed first by another team member.

* Add to the `modulefile.lua` file to `http://gitlab.psc.edu/bridges2_modulefiles`
* Then create a merge request for review. 
* If approved, then the reviewer will copy over the modulefile to the appropriate location.

### What next?
Once the modulefile is in place, end users should be able to find and load the package

```
➜ module avail BLAST

------------------------------------------- /opt/modulefiles --------------------------------------------
   BLAST/2.9.0    BLAST/2.11.0 (D)

➜ module help BLAST/2.11.0

-------------------------------- Module Specific Help for "BLAST/2.11.0" --------------------------------
Description
-----------
BLAST (basic local alignment search tool) is an algorithm and program for comparing primary biological se
quence information, such as the amino-acid sequences of proteins or the nucleotides of DNA and/or RNA seq
uences.

To load the module type

> module load BLAST/2.11.0

To unload the module type

> module unload BLAST/2.11.0
```

# Advanced Topics
## Spack + Singularity
### Creating a container from an existing environment
If you know how to use Spack and you have created your own environments then you can follow these instructions to create a Dockerfile to create a Docker image. This image can then be used to create a Singularity image. 

This process is not recommended for Bridges 2 since we do not have access to Docker on any compute node.

However, you could convert your environment.yaml to a Dockerfile and then construct it locally.

## Best Practices
![Programming](https://media1.giphy.com/media/1C8bHHJturSx2/200w.webp?cid=ecf05e47wh6ph7qdvdukq9kjonbxyexvs0539i3ntu26leko&rid=200w.webp&ct=g)
* Avoid reinventing the wheel. Search public registries first.
* Do not reinvent the wheel again. You can modify existing images, no need to start from scratch.
* Avoid bloating images. In general avoid using Ubuntu as a starter image, however when testing and developing might be the best option (if not the only).
* Use small images for deploying packages with little or no dependencies. I particularly prefer [Alpine](https://www.alpinelinux.org/) for this purpose.
* If you wish to create a container from an existing Spack environment, then click [here](https://spack.readthedocs.io/en/latest/containers.html).
* If you wisht to create a container using Spack commands, then I’d recommend you start from scratch. Feel free to use this [template](https://gist.github.com/icaoberg/65118b1b27146a809e8165b55141a4f8).
* When deploying software in your home/projects directory Singularity >> Spack. Spack is easy to use but depending on the package you want to install there might be some dependencies that live outside of the scope of Spack. 
* It is okay to install multiple packages into a single container. For example I built a LaTeX container with LaTeX editors, Ghostscript and more.
* Make sure other users have access to these files, that is, set the right permissions. If you don’t, a reviewer will contact you about it so it is an easy fix.
* SyLabs.io is the easiest way to build containers remotely without hassling the systems team.
* Whenever an end user opens a ticket, attempt to build a user story from the ticket and build the appropriate examples for /opt/packages/examples.
* If possible, then add a link to `/opt/packages/examples` in the modulefile. Especially if examples already exist.

---

## List of Singularity containers
The table below list all the Singularity containers maintained by the Biomedical Apps team.
You can find all my public repositories [here](https://gitlab.psc.edu/icaoberg/singularity).


### STEM
| Name | Information |
| --- | --- |
| [abyss](http://github.com/pscedu/singularity-abyss) | ![Status](https://github.com/pscedu/singularity-abyss/actions/workflows/main.yml/badge.svg)![Issue](https://img.shields.io/github/issues/pscedu/singularity-abyss)![forks](https://img.shields.io/github/forks/pscedu/singularity-abyss)![Stars](https://img.shields.io/github/stars/pscedu/singularity-abyss)![License](https://img.shields.io/github/license/pscedu/singularity-abyss) |
| [blast](http://github.com/pscedu/singularity-blast) | ![Status](https://github.com/pscedu/singularity-blast/actions/workflows/main.yml/badge.svg)![Issue](https://img.shields.io/github/issues/pscedu/singularity-blast)![forks](https://img.shields.io/github/forks/pscedu/singularity-blast)![Stars](https://img.shields.io/github/stars/pscedu/singularity-blast)![License](https://img.shields.io/github/license/pscedu/singularity-blast) |
| [bowtie2](http://github.com/pscedu/singularity-bowtie2) | ![Status](https://github.com/pscedu/singularity-bowtie2/actions/workflows/main.yml/badge.svg)![Issue](https://img.shields.io/github/issues/pscedu/singularity-bowtie2)![forks](https://img.shields.io/github/forks/pscedu/singularity-bowtie2)![Stars](https://img.shields.io/github/stars/pscedu/singularity-bowtie2)![License](https://img.shields.io/github/license/pscedu/singularity-bowtie2) |
| [bwa](http://github.com/pscedu/singularity-bwa) | ![Status](https://github.com/pscedu/singularity-bwa/actions/workflows/main.yml/badge.svg)![Issue](https://img.shields.io/github/issues/pscedu/singularity-bwa)![forks](https://img.shields.io/github/forks/pscedu/singularity-bwa)![Stars](https://img.shields.io/github/stars/pscedu/singularity-bwa)![License](https://img.shields.io/github/license/pscedu/singularity-bwa) |
| [fastani](http://github.com/pscedu/singularity-fastani) | ![Status](https://github.com/pscedu/singularity-fastani/actions/workflows/main.yml/badge.svg)![Issue](https://img.shields.io/github/issues/pscedu/singularity-fastani)![forks](https://img.shields.io/github/forks/pscedu/singularity-fastani)![Stars](https://img.shields.io/github/stars/pscedu/singularity-fastani)![License](https://img.shields.io/github/license/pscedu/singularity-fastani) |
| [fasttree](http://github.com/pscedu/singularity-fasttree) | ![Status](https://github.com/pscedu/singularity-fasttree/actions/workflows/main.yml/badge.svg)![Issue](https://img.shields.io/github/issues/pscedu/singularity-fasttree)![forks](https://img.shields.io/github/forks/pscedu/singularity-fasttree)![Stars](https://img.shields.io/github/stars/pscedu/singularity-fasttree)![License](https://img.shields.io/github/license/pscedu/singularity-fasttree) |
| [ffmpeg](http://github.com/pscedu/singularity-ffmpeg) | ![Status](https://github.com/pscedu/singularity-ffmpeg/actions/workflows/main.yml/badge.svg)![Issue](https://img.shields.io/github/issues/pscedu/singularity-ffmpeg)![forks](https://img.shields.io/github/forks/pscedu/singularity-ffmpeg)![Stars](https://img.shields.io/github/stars/pscedu/singularity-ffmpeg)![License](https://img.shields.io/github/license/pscedu/singularity-ffmpeg) |
| [gent](http://github.com/pscedu/singularity-gent) | ![Status](https://github.com/pscedu/singularity-gent/actions/workflows/main.yml/badge.svg)![Issue](https://img.shields.io/github/issues/pscedu/singularity-gent)![forks](https://img.shields.io/github/forks/pscedu/singularity-gent)![Stars](https://img.shields.io/github/stars/pscedu/singularity-gent)![License](https://img.shields.io/github/license/pscedu/singularity-gent) |
| [hisat2](http://github.com/pscedu/singularity-hisat2) | ![Status](https://github.com/pscedu/singularity-hisat2/actions/workflows/main.yml/badge.svg)![Issue](https://img.shields.io/github/issues/pscedu/singularity-hisat2)![forks](https://img.shields.io/github/forks/pscedu/singularity-hisat2)![Stars](https://img.shields.io/github/stars/pscedu/singularity-hisat2)![License](https://img.shields.io/github/license/pscedu/singularity-hisat2) |
| [methylpy](http://github.com/pscedu/singularity-methylpy) | ![Status](https://github.com/pscedu/singularity-methylpy/actions/workflows/main.yml/badge.svg)![Issue](https://img.shields.io/github/issues/pscedu/singularity-methylpy)![forks](https://img.shields.io/github/forks/pscedu/singularity-methylpy)![Stars](https://img.shields.io/github/stars/pscedu/singularity-methylpy)![License](https://img.shields.io/github/license/pscedu/singularity-methylpy) |
| [octave](http://github.com/pscedu/singularity-octave) | ![Status](https://github.com/pscedu/singularity-octave/actions/workflows/main.yml/badge.svg)![Issue](https://img.shields.io/github/issues/pscedu/singularity-octave)![forks](https://img.shields.io/github/forks/pscedu/singularity-octave)![Stars](https://img.shields.io/github/stars/pscedu/singularity-octave)![License](https://img.shields.io/github/license/pscedu/singularity-octave) |
| [phylip-suite](http://github.com/pscedu/singularity-phylip-suite) | ![Status](https://github.com/pscedu/singularity-phylip-suite/actions/workflows/main.yml/badge.svg)![Issue](https://img.shields.io/github/issues/pscedu/singularity-phylip-suite)![forks](https://img.shields.io/github/forks/pscedu/singularity-phylip-suite)![Stars](https://img.shields.io/github/stars/pscedu/singularity-phylip-suite)![License](https://img.shields.io/github/license/pscedu/singularity-phylip-suite) |
| [picard](http://github.com/pscedu/singularity-picard) | ![Status](https://github.com/pscedu/singularity-picard/actions/workflows/main.yml/badge.svg)![Issue](https://img.shields.io/github/issues/pscedu/singularity-picard)![forks](https://img.shields.io/github/forks/pscedu/singularity-picard)![Stars](https://img.shields.io/github/stars/pscedu/singularity-picard)![License](https://img.shields.io/github/license/pscedu/singularity-picard) |
| [tiger](http://github.com/pscedu/singularity-tiger) | ![Status](https://github.com/pscedu/singularity-tiger/actions/workflows/main.yml/badge.svg)![Issue](https://img.shields.io/github/issues/pscedu/singularity-tiger)![forks](https://img.shields.io/github/forks/pscedu/singularity-tiger)![Stars](https://img.shields.io/github/stars/pscedu/singularity-tiger)![License](https://img.shields.io/github/license/pscedu/singularity-tiger) |
| [trimmomatic](http://github.com/pscedu/singularity-trimmomatic) | ![Status](https://github.com/pscedu/singularity-trimmomatic/actions/workflows/main.yml/badge.svg)![Issue](https://img.shields.io/github/issues/pscedu/singularity-trimmomatic)![forks](https://img.shields.io/github/forks/pscedu/singularity-trimmomatic)![Stars](https://img.shields.io/github/stars/pscedu/singularity-trimmomatic)![License](https://img.shields.io/github/license/pscedu/singularity-trimmomatic) |

### Utilities
| Name | Information |
| --- | --- |
| [asciinema](http://github.com/pscedu/singularity-asciinema) | ![Status](https://github.com/pscedu/singularity-asciinema/actions/workflows/main.yml/badge.svg)![Issue](https://img.shields.io/github/issues/pscedu/singularity-asciinema)![forks](https://img.shields.io/github/forks/pscedu/singularity-asciinema)![Stars](https://img.shields.io/github/stars/pscedu/singularity-asciinema)![License](https://img.shields.io/github/license/pscedu/singularity-asciinema) |
| [bat](http://github.com/pscedu/singularity-bat) | ![Status](https://github.com/pscedu/singularity-bat/actions/workflows/main.yml/badge.svg)![Issue](https://img.shields.io/github/issues/pscedu/singularity-bat)![forks](https://img.shields.io/github/forks/pscedu/singularity-bat)![Stars](https://img.shields.io/github/stars/pscedu/singularity-bat)![License](https://img.shields.io/github/license/pscedu/singularity-bat) |
| [dust](http://github.com/pscedu/singularity-dust) | ![Status](https://github.com/pscedu/singularity-dust/actions/workflows/main.yml/badge.svg)![Issue](https://img.shields.io/github/issues/pscedu/singularity-dust)![forks](https://img.shields.io/github/forks/pscedu/singularity-dust)![Stars](https://img.shields.io/github/stars/pscedu/singularity-dust)![License](https://img.shields.io/github/license/pscedu/singularity-dust) |
| [graphviz](http://github.com/pscedu/singularity-graphviz) | ![Status](https://github.com/pscedu/singularity-graphviz/actions/workflows/main.yml/badge.svg)![Issue](https://img.shields.io/github/issues/pscedu/singularity-graphviz)![forks](https://img.shields.io/github/forks/pscedu/singularity-graphviz)![Stars](https://img.shields.io/github/stars/pscedu/singularity-graphviz)![License](https://img.shields.io/github/license/pscedu/singularity-graphviz) |
| [hyperfine](http://github.com/pscedu/singularity-hyperfine) | ![Status](https://github.com/pscedu/singularity-hyperfine/actions/workflows/main.yml/badge.svg)![Issue](https://img.shields.io/github/issues/pscedu/singularity-hyperfine)![forks](https://img.shields.io/github/forks/pscedu/singularity-hyperfine)![Stars](https://img.shields.io/github/stars/pscedu/singularity-hyperfine)![License](https://img.shields.io/github/license/pscedu/singularity-hyperfine) |
| [rclone](http://github.com/pscedu/singularity-rclone) | ![Status](https://github.com/pscedu/singularity-rclone/actions/workflows/main.yml/badge.svg)![Issue](https://img.shields.io/github/issues/pscedu/singularity-rclone)![forks](https://img.shields.io/github/forks/pscedu/singularity-rclone)![Stars](https://img.shields.io/github/stars/pscedu/singularity-rclone)![License](https://img.shields.io/github/license/pscedu/singularity-rclone) |
