
# Disk usage logging

  soft=/rfanfs/pnl-zorro/software/
  $soft/diskusage-logging
  $soft/cron

  crontab -ls


# Conda: Software and Python Environments

Anaconda is installed on the cluster as well as the local network.
There are currently some virtualenv activation scripts on the cluster,
but we replacing them with conda as our python package manager.
The advantage of conda is that it is general purpose and not limited to python
only packages.  This helps managing python packages that have external dependencies,
such as NumPy, SciPy, VTK, and Matplotlib.

It is recommended that each project have its own isolated software environment.
The advantages of this over a global environment are that it protects the
project from changes to the global environment, allows projects to use different
software configurations, and records of what software was used to generate the
data.

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

To create and activate this environment, run

    conda env create -f  environment.yml
    source activate wma-664bb45


## Further Reading

* [Conda Docs: Managing Environments](https://conda.io/docs/using/envs.html)
* [Conda: Myths and Misconcpetions](https://jakevdp.github.io/blog/2016/08/25/conda-myths-and-misconceptions/)


# Creating a Whitematteranalysis Environment

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
    source activate <name>

and you should be able to run the whitematteranalsysis scripts. If it
complains about a missing dependency, add it to `environment.yml` and
update the environment:

    conda env update -n <name> -f environment.yml

For more info on managing conda environments,
see [Managing Environments](https://conda.io/docs/using/envs.html).
