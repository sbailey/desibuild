# Scripts and Tools for Installation of DESI Software

The idea behind these scripts is to keep them as simple as possible, but
no simpler.

## Use Cases

Hopefully one of these situations describes what you are looking for...

### Someone Already Installed Everything, but I Want to Modify Package "X"

This is easy.  Load the full software stack installed by someone else, then
unload the package you want to work on.  Say that you are working on package
"desiblat"::

    $>  module load desi
    $>  module unload desiblat

Now git clone desiblat somewhere and install it somewhere in your PATH / 
PYTHONPATH.

### I Want to Install Everything, but with Custom Versions of Packages

No problem.  You should copy one of the text files in the "versions"
directory.  Name it something you like.  Now edit the versions of each package
that you want to install.  For example, you might start with a file that
has the latest stable tagged versions of all packages and change one or
more of the package versions to "master".  Now proceed with the installation
instructions below.

### I Want to Install Everything, but Using Different Dependencies

First, select / edit the text file of package versions you want.  Now decide
what commands you need to run inside a module file in order to load all the
python stack and other dependencies (e.g CFITSIO, HARP, etc).  Paste those
commands in a text file.  Look in the modulefiles directory for example 
initialization commands used at NERSC.  Use this custom module init snippet
when running desi_setup below.  Now follow the installation instructions.


## Installation

Before installation, you will need to have all the external dependencies
installed on your system.  You also need to create / use a text file listing
the git branch/tag of each package.  Since we will be running many git
commands (when using the desi_source script), I highly recommend setting
up your ssh keys with a keychain, so that you don't have to type your
password dozens of times.

### Dependencies

Get all the dependencies into your environment before using these tools.
At NERSC, you can load the "desi-conda" module.  On other systems, you 
will need a full python stack as well as some compiled packages (CFITSIO,
BOOST, LAPACK, mpi4py).

### Get the Source

Now make a working directory.  For this example, just put it in the top
of the desibuild source tree, but it could be anywhere.  We will use the
"desi_source" script and our text file of versions to get everything::

    $>  mkdir build
    $>  cd build
    $>  ../desi_source versions.txt

Where "versions.txt" is the file you have copied / modified that lists the
packages and their versions to install.  When this finishes, you should
have a git clone of every repo, and the working directory should be set
to a local copy of the specified branch/tag.

NOTE:  At this point you can go into those git clones and make new branches,
etc.  Whatever branch is checked out in each package is the one that will
be installed.

### Install the Packages

Decide where you want to install things.  At NERSC, you should put everything
in your ${SCRATCH} directory somewhere for performance.  Also, if there are
any module commands needed to set up your dependencies, then those commands
should be in a snippet of text that will be inserted into the top level module
file.  For NERSC systems, you should use one of the examples in the "modulefiles"
directory unless you know what you are doing.  On a personal system or other
HPC center, ensure that you know what module commands are needed to get the
python stack and other dependencies into your environment.  Put those into a
module file snippet.  Now we install all packages.  This command will use the
*current state* of all the git clones.  So if you made a local branch after 
getting the source with "desi_source", then that is what will be installed::

    $>  ../desi_setup \
        -p <prefix> \
        -v versions.txt \
        -e <my module snippet>

This installs everything.  If you don't use modules on your system (and 
therefore are managing your dependencies through other shell functions or
techniques), then you can just source the top-level "setup.sh" file that was
created in the installation prefix.  Otherwise, you can now do::

    $>  module use <prefix>/modulefiles
    $>  module load desi

Where "prefix" is obviously what you specified with the "-p" option to desi_setup.
