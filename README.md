<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-generate-toc again -->
**Table of Contents**

- [Software](#software)
    - [Ad-hoc Software Installation](#ad-hoc-software-installation)
    - [pnldash](#pnldash)
    - [UKFTractography](#ukftractography)
    - [BRAINSTools (including ANTs)](#brainstools-including-ants)
    - [Slicer](#slicer)
    - [Whitematteranalysis](#whitematteranalysis)
    - [tract_querier](#tractquerier)
    - [Washington University HCPPipelines](#washington-university-hcppipelines)
    - [nrrdchecker](#nrrdchecker)
    - [MRtrix3](#mrtrix3)
    - [Matlab](#matlab)
    - [Testing DWIConvert](#testing-dwiconvert)
    - [dwipipeline multi-shell](#dwipipeline-multi-shell)
    - [Disk usage logging](#disk-usage-logging)
- [Conda: Software and Python Environments](#conda-software-and-python-environments)
    - [Further Reading](#further-reading)
- [Cluster](#cluster)
    - [Bashrc](#bashrc)
- [unison](#unison)

<!-- markdown-toc end -->


# Software

## Ad-hoc Software Installation

`pnlpipe` has a set of recipes for building most of the software used at the
PNL. It can install multiple versions of the same package, which makes it easy
to upgrade software for your project without affecting anyone else's currently
running projects. It installs the software to `$PNLPIPE_SOFT`, which on the cluster
is set to `/data/pnl/soft` and on the network is set to `/rfanfs/pnl-zorro/software`.

To get a list of available software packages:

    cd /data/pnl/soft/pnlpipe
    ./pnlpipe install -h

    Usage:
        pnlpipe install [SWITCHES] softwareModule

    where softwareModule is one of:
    BRAINSTools
    FreeSurfer
    HCPPipelines
    Slicer
    UKFTractography
    mrtrix3
    nrrdchecker
    tract_querier
    trainingDataT1AHCC
    trainingDataT2Masks
    whitematteranalysis


To install one of them:

    ./pnlpipe install <software> [--version <version>]

For example,

    ./pnlpipe install UKFTractography --version DiffusionPropagator
    ./pnlpipe install BRAINSTools --version master

installs the `DiffusionPropagator` branch of `UKFTractography` and the `master`
branch of `BRAINSTools`.

Each software module interprets version in its own way. Most of the time,
`--version` expects a Github revision, as for these examples. However
the switch is optional; running `install` without specifying a version will
install the software's default version.

Here's an example on how to install the Washington
University's
[HCP Pipeline scripts](https://github.com/Washington-University/Pipelines):

    ./pnlpipe install HCPPipelines --version 3.22.0


## pnldash

Project tracker, to run in your project directories.

* homepage: https://github.com/reckbo/pnldash
* cluster: /data/pnl/soft/pnldash
* network: /rfanfs/pnl-zorro/software/pnldash

The database of projects that people push to is on the cluster at `export
PNLDASH_DB=/data/pnl/soft/pnldash/db`.

## UKFTractography

* homepage: github.com/pnlbwh/ukftractography
* cluster: /data/pnl/soft/UKFTractography-*
* network: /rfanfs/pnl-zorro/software/UKFTractography-*

To install latest:

    cd /path/to/a/pnlpipe
    ./pnlpipe install UKFTractography --version master

Sometimes installing `UKFTractography` will give a build error, in this case
delete `$PNLPIPE_SOFT/UKFTractography-build` and try installing again.

## BRAINSTools (including ANTs)

* homepage: github.com/BRAINSia/BRAINSTools
* cluster: /data/pnl/soft/BRAINSTools-bin-*
* network: /rfanfs/pnl-zorro/software/BRAINSTools-bin-*

To install the latest and add it to your `PATH` and `ANTSPATH`:

    ./pnlpipe install BRAINSTools --version master
    source $PNLPIPE_SOFT/BRAINSTools-bin-<commit>/env.sh

Sometimes installing `BRAINSTools` will give a build error, in this case delete
`$PNLPIPE_SOFT/BRAINSTools-build` and try installing again.


## Slicer

* homepage: www.slicer.org
* cluster: /data/pnl/soft/Slicer-*-linux-amd64
* network: /rfanfs/pnl-zorro/software/Slicer-*-linux-amd64

To install, for example, version `4.7.0`:

    ./pnlpipe install Slicer --version 4.7.0

## Whitematteranalysis

* homepage: https://github.com/SlicerDMRI/whitematteranalysis
* cluster: /data/pnl/soft/whitematteranalysis-*
* network: /rfanfs/pnl-zorro/software/whitematteranalysis-*

To install the latest and add it to the `PATH` and `PYTHONPATH`:

    cd /path/to/a/pnlpipe
    ./pnlpipe install whitematteranalysis --version master
    source $soft/whitematteranalysis-<commit>/env.sh

## tract_querier

* homepage: https://github.com/demianw/tract_querier
* cluster: /data/pnl/soft/tract_querier-*
* network: /rfanfs/pnl-zorro/software/tract_querier-*

To install the latest and add it to the `PATH` and `PYTHONPATH`:

    cd /path/to/a/pnlpipe
    ./pnlpipe install tract_querier --version master
    source $soft/tract_querier-<commit>/env.sh


## Washington University HCPPipelines

* homepage: https://github.com/Washington-University/Pipelines
* cluster: /data/pnl/soft/HCPPipelines-*
* network: /rfanfs/pnl-zorro/software/HCPPipelines-*

To install version `3.22.0`:

    cd /path/to/a/pnlpipe
    ./pnlpipe install HCPPipelines --version master


## nrrdchecker

Haskell program that compares the nrrd headers of two or more nrrd images
and prints or saves a csv with one row per key.

* homepage: https://github.com/reckbo/nrrdchecker
* cluster: /data/pnl/soft/nrrdchecker-*/nrrdchecker  # binaries
* cluster: /data/pnl/soft/nrrdchecker # source directory
* network: /rfanfs/pnl-zorro/software/nrrdchecker-553c310/nrrdchecker

To install/update on the cluster:

    cd /path/to/a/pnlpipe
    ./pnlpipe install nrrdchecker

To manually build on the cluster:

    module load stack
    cd /data/pnl/soft/nrrdchecker
    stack build
    stack exec -- nrrdchecker -h

You can copy the output to the PNL local network to use it there (where `stack`
or `cabal` isn't installed).


## MRtrix3

* homepage: https://github.com/MRtrix3/mrtrix3
* cluster: /data/pnl/soft/mrtrix3-*
* network: /rfanfs/pnl-zorro/software/mrtrix3-*

To install latest version:

    cd /path/to/a/pnlpipe
    ./pnlpipe install mrtrix3 --version master


## Matlab

On the cluster, you can load software using `module`.  For example, for matlab:

    module unload matlab
    module load matlab/2017a

To see a list of available modules:

    module avail

## Testing DWIConvert

`pnlpipe` has a pipeline called `DWIConvertTest` that tests the `BRAINSTools` DWI conversion
binary by comparing the two nrrds that result from these conversions:

     DWI -> nrrd
     DWI -> nifti -> nrrd

They should be the same but this isn't the case (see https://github.com/BRAINSia/BRAINSTools/issues/350).
'space origin' and 'thicknesses' are not preserved in commits `2d5eccb`, `41353e8`, and `b195aae`, the
commits I tested.

I suggest that when upgrading to a new version of `DWIConvert` that you run this test first.
On the cluster:


     cd /data/pnl/soft/pnlpipe-dwiconverttest
     # add new commit to pnlpipe_params/DWIConvertTest.params
     ./runme.sh

This generates an html report `_data/DWIConvertTest.html`.  See the latest at


    /data/pnl/soft/pnlpipe-dwiconverttest/_data/DWIConvertTest.html


## dwipipeline multi-shell

These are some scripts I wrote long time ago that apply our eddy current
correction to multi-shell DWI's.

    /projects/schiz/software/scripts/diffusion/dwipipeline_multishell

## Disk usage logging

This software generates a report of the PNL's disk usage.

* homepage: https://github.com/pnlbwh/diskusage-logging
* network: /rfanfs/pnl-zorro/software/diskusage-logging

The scripts here are called daily and weekly by a cron job that is defined
in

    /rfanfs/pnl-zorro/software/cron/daily.sh
    /rfanfs/pnl-zorro/software/cron/weekly.sh

To make sure the cron job is active on a machine, run

    crontab -ls

If it is active and running correctly, every week a report of the PNL's disk
usage will be generated and saved as
`/rfanfs/pnl-zorro/software/diskusage-logging/_data/report.html`. The cron job
is also configured to email the report to the head RA and Sylvain, although this
can be unreliable at times.

If no cron job is detected and you want to start one on a particular machine,
run

    crontab ./pnl.crontab

Make sure to check regularly that the report is being generated every week.

# Conda: Software and Python Environments

It is recommended that each project have its own isolated software environment.
The advantages of this over a global environment are that it protects the
project from changes to the global environment, allows projects to use different
software configurations, and records what software was used to generate the
data.

To help with this, we are transitioning to
using [pnlpipe](https://github.com/reckbo/pnlpipe) to run our pipelines and
install MRI processing software, and conda for setting up our python
environments.

Conda is a general purpose package manager whose advantage over virtualenv is
that it is not limited to python packages. This helps managing python packages
that have external dependencies such as NumPy, SciPy, VTK, and Matplotlib.
Conda is installed on the cluster as well as the local network.

To setup a conda environment, each project lists its dependencies in a file
called `environment.yml`.  Here's an example for a project that uses
`whitematteranalysis`:

    name: wma-664bb45
        dependencies:
          - nibabel
          - numpy
          - vtk
          - joblib
          - matplotlib
          - pip:
            - git+https://github.com/SlicerDMRI/whitematteranalysis.git@664bb45b34003689f0dccbed45cf864bb11ce4a5

To create and activate this environment, you would run

    conda env create -f  environment.yml
    source activate wma-664bb45

Note you can install `whitematteranalysis` using `pnlpipe`, see above.

## Further Reading

* [Conda Docs: Managing Environments](https://conda.io/docs/using/envs.html)
* [Conda: Myths and Misconcpetions](https://jakevdp.github.io/blog/2016/08/25/conda-myths-and-misconceptions/)


# Cluster

Listing jobs:

    binfo # lists PNL nodes availability
    bjobs # lists your LSF jobs

See what software packages are available:

    module avail

Loading and unloading a software package:

    module unload matlab
    module load matlab/2017a

## Bashrc

The pnl wide bashrc is located in `/data/pnl/soft/bashrc`. Your personal
`~/.bashrc` should source this file, i.e. put `source /data/pnl/soft/bashrc` at
the end of `~/.bashrc`.


# unison

I used to use `unison` to sync data between the local PNL network and the cluster, primarily
for INTRuST but also for other datasets.  The configuration for each data set are saved in this
directory:

    /rfanfs/pnl-zorro/software/.unison

For example, to sync INTRuST, from anywhere on the network run

    unison -perms 0 int  # -perms 0 means ignore change in permissions

It is a bidirectional sync, so changes can be pushed in either direction.
Press '/' to skip a change, 'f' to accept a proposed change, and '<' or '>'
to push a change from or to the cluster.
