<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-generate-toc again -->
**Table of Contents**

- [Software](#software)
    - [pnlpipe](#pnlpipe)
    - [pnldash](#pnldash)
    - [nrrdchecker](#nrrdchecker)
    - [Matlab](#matlab)
    - [Testing DWIConvert](#testing-dwiconvert)
    - [dwipipeline multi-shell](#dwipipeline-multi-shell)
- [Disk usage logging](#disk-usage-logging)
- [Ad-hoc Software Installation](#ad-hoc-software-installation)
    - [UKFTractography Issues](#ukftractography-issues)
- [Conda: Software and Python Environments](#conda-software-and-python-environments)
    - [Further Reading](#further-reading)
- [Creating a Whitematteranalysis Environment](#creating-a-whitematteranalysis-environment)
    - [Using Conda](#using-conda)
    - [Using pnlpipe](#using-pnlpipe)
- [Cluster](#cluster)
    - [Bashrc](#bashrc)
- [unison](#unison)

<!-- markdown-toc end -->


# Software

## pnlpipe

Software app to run our pipelines.

* homepage: https://github.com/reckbo/pnlpipe


## pnldash

Project tracker, to run in your project directories.

* homepage: https://github.com/reckbo/pnldash
* cluster: /data/pnl/soft/pnldash
* network: /rfanfs/pnl-zorro/software/pnldash

The database of projects that people push to is on the cluster at `export
PNLDASH_DB=/data/pnl/soft/pnldash/db`.

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

# Disk usage logging

A report of the PNL's disk usage is generated by the software package
at https://github.com/pnlbwh/diskusage-logging.  It is installed on
the local network at

    /rfanfs/pnl-zorro/software/diskusage-logging

The scripts in here are called daily and weekly by a cron job that is defined
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

If not cron job is detected and you want to start one on a particular machine,
run

    crontab ./pnl.crontab

Make sure to check regularly that the report is being generated every week.
If

# Ad-hoc Software Installation


    git clone git://github.com/reckbo/pnlpipe && cd pnlpipe

To get a list of available software packages, run

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


To install one of them, running

    ./pnlpipe install <software>

For example,

    ./pnlpiipe install UKFTractography --version DiffusionPropagator

installs the `DiffusionPropagator` branch of `UKFTractography`.
The software is installed in `$PNLPIPE_SOFT`.

## UKFTractography Issues

Sometimes installing `UKFTractography` will give a build error, in this case
delete `$soft/UKFTractography-build` and try installing again.


# Conda: Software and Python Environments

It is recommended that each project have its own isolated software environment.
The advantages of this over a global environment are that it protects the
project from changes to the global environment, allows projects to use different
software configurations, and records what software was used to generate the
data.

To help with this, we are transitioning to using [pnlpipe](https://github.com/reckbo/pnlpipe)
to run our pipelines and install MRI processing software, and conda for setting up
our python environments.

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

For running some of our standard pipelines, see [pnlpipe](https://github.com/reckbo/pnlpipe).


## Further Reading

* [Conda Docs: Managing Environments](https://conda.io/docs/using/envs.html)
* [Conda: Myths and Misconcpetions](https://jakevdp.github.io/blog/2016/08/25/conda-myths-and-misconceptions/)


# Creating a Whitematteranalysis Environment

## Using Conda

In your project directory, make a file called something like `environment.yml`.
In this file put the following:

    name: wma-664bb45
    dependencies:
      - nibabel
      - numpy
      - vtk
      - joblib
      - matplotlib
      - pip:
         - git+https://github.com/SlicerDMRI/whitematteranalysis.git@664bb45b34003689f0dccbed45cf864bb11ce4a5

If `whitematteranalysis` has been updated recently and you want to use the most recent
version, visit https://github.com/SlicerDMRI/whitematteranalysis/commits/master and
copy the commit hash to the clipboard by clicking on the clipboard icon next to the
latest commit. Then delete the `664bb45...` part and paste the new commit.  Also
change the name to reflect this new commit.

Now run

    conda env create -f  environment.yml
    source activate wma-664bb45  # or wma-<chosen_commit>

and you should be able to run the whitematteranalysis scripts. If it
complains about a missing dependency, add it to `environment.yml` and
update the environment:

    conda env update -n <name> -f environment.yml

For more info on managing conda environments,
see [Managing Environments](https://conda.io/docs/using/envs.html).

## Using pnlpipe

    git clone git://github.com/reckbo/pnlpipe && cd pnlpipe
    source activate pnlpipe
    ./pnlpipe install whitematteranalysis --version <github_commit>
    source $soft/whitematteranalysis-<github_commit>/env.sh


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
