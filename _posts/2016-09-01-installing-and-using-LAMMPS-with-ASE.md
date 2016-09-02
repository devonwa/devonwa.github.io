---
layout: post
title: A condensed installation guide for using LAMMPS with ASE
tags: ASE, LAMMPS, Python
type: article
description: This article is an abbreviated installation guide for quickly building LAMMPS and running it through ASE.
---

<div id="table-of-contents">
<h2>Table of Contents</h2>
<div id="text-table-of-contents">
<ul>
<li><a href="#Dependencies">1. Dependencies</a></li>
<li><a href="#Making_LAMMPS">2. Making LAMMPS</a>
<ul>
<li><a href="#Python_and_optional_packages">2.1. Python and optional packages</a>
<ul>
<li><a href="#ReaxFF">2.1.1. ReaxFF</a></li>
</ul>
</li>
<li><a href="#LAMMPS_as_a_shared_library">2.2. LAMMPS as a shared library</a></li>
</ul>
</li>
<li><a href="#Connecting_to_ASE_with_LAMMPSrun">3. Connecting to ASE with LAMMPSrun</a></li>
<li><a href="#Testing_it_out">4. Testing it out</a>
<ul>
<li><a href="#LAMMPS_basic_test">4.1. LAMMPS basic test</a></li>
<li><a href="#MPI_test">4.2. MPI test</a></li>
<li><a href="#LAMMPS_with_Python_test">4.3. LAMMPS with Python test</a></li>
<li><a href="#mpi4py_with_LAMMPS">4.4. mpi4py with LAMMPS</a></li>
<li><a href="#LAMMPSrun_calculator_test">4.5. LAMMPSrun calculator test</a></li>
</ul>
</li>
<li><a href="#An_advanced_calculation">5. An advanced calculation</a></li>
<li><a href="#Closing">6. Closing</a>
<ul>
<li><a href="#A_condensed_form_of_the_condensed_guide">6.1. A condensed form of the condensed guide (tl;dr)</a></li>
<li><a href="#Notes_for_users_on_Gilgamesh_at_Carnegie_Mellon">6.2. Notes for users on Gilgamesh at Carnegie Mellon</a></li>
</ul>
</li>
</ul>
</div>
</div>
<br />

In my research group, we rely heavily on using [ASE](https://wiki.fysik.dtu.dk/ase/index.html) to define, visualize, and work with our atomic structures in Python. We perform calculations with the quantum mechanics simulation package [VASP](http://www.vasp.at/) through an [ASE calculator that my advisor created](https://github.com/jkitchin/vasp), which nicely wraps everything we need from VASP into something we can use in Python. So we don't have to worry about constructing or parsing all of the Fortran input/output files from VASP.

But VASP, while exceptional for density functional theory (DFT), isn't as commonly used for molecular dynamics (MD) simulations. That's where [LAMMPS](http://lammps.sandia.gov/) shines, but it can be a bit of a bear to compile and get setup with ASE if you are not experienced with compiling and linking packages.

So this post is a condensed form of what you can find in the [LAMMPS](http://lammps.sandia.gov/doc/Manual.html) and [ASE](https://wiki.fysik.dtu.dk/ase/index.html) documentation, for people looking to get setup as quickly as possible. *You can [skip to the end](#A_condensed_form_of_the_condensed_guide) for a script that will run all the steps of this guide if your environment is setup correctly (good luck :).*

## <a id="Dependencies"></a>Dependencies

Before we begin, I will assume that you have the following packages installed on your machine:

-   [MPI](https://www.open-mpi.org/)
-   [Python 2](https://www.python.org/download/releases/2.7.2/)
-   [mpi4py](https://pypi.python.org/pypi/mpi4py)
-   [ASE](https://wiki.fysik.dtu.dk/ase/index.html)

The guide was written for Linux (I've used it with Arch, Ubuntu, and CentOS) and should be similar for Mac. Unfortunately for Windows users, you will have to read the official documentation to build LAMMPS, but [it is possible](http://rpm.lammps.org/windows.html). 

## <a id="Making_LAMMPS"></a>Making LAMMPS

First we need to [download LAMMPS](http://lammps.sandia.gov/download.html). This can be done in many ways, but perhaps the simplest is with git.

``` sh
git clone git://git.lammps.org/lammps-ro.git
```

The git repository may be on an unstable release when you download it. Stable versions of LAMMPS are listed on [this page](http://lammps.sandia.gov/bug.html) with a corresponding git tag that you can checkout. For example, I'm currently using the stable release from July 2016 with the git tag `r15407`.

``` sh
git checkout tags/r15407
```

Wherever you've downloaded LAMMPS, let's set an environment to denote it's path in the system. We will use this variable several times in the following scripts.

``` sh
export LAMMPSPATH="$HOME/lib/lammps-ro"
```

Now, change into the `src` directory where we will start to build the LAMMPS package.

``` sh
cd $LAMMPSPATH/src
```

### <a id="Python_and_optional_packages"></a>Python and optional packages

LAMMPS requires that you specify what [optional packages](http://lammps.sandia.gov/doc/Section_packages.html) you would like to use before you build. They often contain potentials or advanced functionality beyond the base package. One such package that we definitely want is Python. The following command tells the Makefile to include the Python package when it comes time to make LAMMPS. It does not actually build the package yet.

``` sh
make yes-python
```

My current research uses the AIREBO potential which is contained in the MANYBODY package. The MANYBODY package contains several other useful potentials, such as EAM and Tersoff. I've found that test scripts like to use these potentials and suggest that you include this package.

``` sh
make yes-manybody
```

If you decide you no longer want an optional package, simply change `make yes-manybody` to `make no-manybody`. If you decide you want another package after LAMMPS is built, simply run the `make yes-package` command. Then build LAMMPS again. Nothing special required.

#### <a id="ReaxFF"></a>ReaxFF

The Reax optional package requires an additional dependency that you likely already have: a Fortran compiler, like `gfortran`. You must specify this compiler using a Makefile in the `lib/reax` directory of LAMMPS before adding it to the LAMMPS build.

``` sh
cd $LAMMPSPATH/lib/reax
make -f Makefile.gfortran
cd $LAMMPSPATH/src
make yes-reax
```

### <a id="LAMMPS_as_a_shared_library"></a>LAMMPS as a shared library

We will be building LAMMPS twice. The first time to generate a binary for the traditional means of running LAMMPS and the second time to build the shared library that Python can communicate with.

``` sh
make mpi
make mpi mode=shlib
```

This should create a binary called `lmp_mpi`, which you can run in the shell with `./lmp_mpi`. You can exit LAMMPS in the shell with `Ctrl+d`. Now is a good time to add a link to `lmp_mpi` somewhere in your `$PATH`, so you can call it anywhere.

Python will also need to know where you've built LAMMPS. To do this, update your `$PYTHONPATH` to include the `python` directory in LAMMPS. We also need to include the `src` directory in `$LD_LIBRARY_PATH` to run LAMMPS as shared library.

``` sh
export PYTHONPATH="$LAMMPSPATH/python:$PYTHONPATH"
export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$LAMMPSPATH/src"
```

## <a id="Connecting_to_ASE_with_LAMMPSrun"></a>Connecting to ASE with LAMMPSrun

[LAMMPSrun](https://wiki.fysik.dtu.dk/ase/ase/calculators/lammpsrun.html) is a [calculator](https://wiki.fysik.dtu.dk/ase/ase/calculators/lammps.html?highlight=lammps) provided by ASE and is currently in early development. I also host a fork of the calculator [on Github](https://github.com/devonwa/LAMMPSrun). LAMMPSrun requires an evironment variable called `$LAMMPS_COMMAND`, which tells the calculator how to call LAMMPS.

``` sh
export LAMMPS_COMMAND="/usr/bin/mpirun -np 4 lmp_mpi"
```

This will call `mpirun` to run the `lmp_mpi` binary over 4 processes (`-np` = number of processes). Note that `mpirun` must be specified with an absolute path for LAMMPSrun to read it properly.

## <a id="Testing_it_out"></a>Testing it out

So that's it. Think everything built correctly? Here are some tests to check your progress.

### <a id="LAMMPS_basic_test"></a>LAMMPS basic test

The following command will run a simple Lennard-Jones (LJ) calculation.

``` sh
lmp_mpi -in $LAMMPSPATH/bench/in.lj
```

Yields (truncated):

``` txt
LAMMPS (30 Jul 2016)
...
Performance: 26229.986 tau/day, 60.718 timesteps/s
100.0% CPU use with 1 MPI tasks x no OpenMP threads
...
Total # of neighbors = 1202833
...
Total wall time: 0:00:01
```

### <a id="MPI_test"></a>MPI test

We can run the same simulation over 4 processes with MPI.

``` sh
mpirun -np 4 lmp_mpi -in $LAMMPSPATH/bench/in.lj
```

Yields (truncated): 

``` txt
LAMMPS (30 Jul 2016)
...
Performance: 65476.470 tau/day, 151.566 timesteps/s
99.5% CPU use with 4 MPI tasks x no OpenMP threads
...
Total # of neighbors = 1202833
...
Total wall time: 0:00:00
```

### <a id="LAMMPS_with_Python_test"></a>LAMMPS with Python test

The following should load LAMMPS and then exit immediately after, when the python script completes.

``` python
from lammps import lammps
lmp = lammps()
```

Yields:

``` txt
LAMMPS (30 Jul 2016)
Total wall time: 0:00:00
```

### <a id="mpi4py_with_LAMMPS"></a>mpi4py with LAMMPS

This will run the previous LJ simulation with MPI and one process, but from Python.

This script is from the [LAMMPS Python interface docs](http://lammps.sandia.gov/doc/Section_python.html).

``` python
from lammps import lammps
from mpi4py import MPI
import os

lmp = lammps()
lmp_path = os.environ['LAMMPSPATH']
lmp.file(os.path.join(lmp_path, "bench/in.lj"))
me = MPI.COMM_WORLD.Get_rank()
nprocs = MPI.COMM_WORLD.Get_size()

print "Proc %d out of %d procs has" % (me,nprocs),lmp
MPI.Finalize()
```

Yields (truncated):

``` txt
LAMMPS (30 Jul 2016)
...
Performance: 26944.125 tau/day, 62.371 timesteps/s
100.0% CPU use with 1 MPI tasks x no OpenMP threads
...
Total # of neighbors = 1202833
...
Total wall time: 0:00:01
```

### <a id="LAMMPSrun_calculator_test"></a>LAMMPSrun calculator test

This is your first calculation with LAMMPS and ASE! It's all coming together! Let's get the stress on a molecule of table salt.

This script is from the [LAMMPSrun docs](https://wiki.fysik.dtu.dk/ase/ase/calculators/lammpsrun.html).

``` python
from ase import Atoms, Atom
from ase.calculators.lammpsrun import LAMMPS

a = [6.5, 6.5, 7.7]
d = 2.3608
NaCl = Atoms([Atom('Na', [0, 0, 0]),
                Atom('Cl', [0, 0, d])],
                cell=a, pbc=True)

calc = LAMMPS(tmp_dir="/home/devonwa/tmp/lammpstmp")
NaCl.set_calculator(calc)

print(NaCl.get_stress())
```

Yeilds:

``` txt
[-0.  -0.  0.0004212  -0.  -0.  -0.]
```

## <a id="An_advanced_calculation"></a>An advanced calculation

Here is a calculation for a pristine graphene sheet that uses the AIREBO potential. It calls a few instructions using a pseudo-LAMMPS syntax, which LAMMPSrun parses and writes to a LAMMPS input file. 

``` python
from ase import Atoms, Atom
from ase.calculators.lammpsrun import LAMMPS
from ase.visualize import view
import numpy as np
import os

## LAMMPS context information
lmp_path = os.getenv("LAMMPSPATH")
potential = os.path.join(lmp_path, "potentials/CH.airebo")
files = [potential]
parameters = {"mass": ["* 1.0"],
                "pair_style": "airebo 6.5 1 1",
                "pair_coeff": ['* * ' + potential + ' C']}
calc = LAMMPS(parameters=parameters, files=files)

## Graphene structure
a = 2.46
a1 = a * np.array([3.0**0.5/2., -1./2., 0.])
a2 = a * np.array([3.0**0.5/2., 1./2., 0.])
a3 = np.array([0., 0., 18.])
atoms = Atoms([Atom('C', 1./2. * a3),
                Atom('C', 1./3. * a1 + 1./3. * a2 + 1./2. * a3)],
                cell=[a1, a2, a3], pbc=True)

## Calculate the energy
atoms.set_calculator(calc)
energy = atoms.get_potential_energy()

print("Energy: {:0.3f} eV".format(energy))
```

Yields:

``` txt
Energy: -14.816 eV
```

## <a id="Closing"></a>Closing

I am not the most experienced developer when it comes to building packages, so please let me know of any improvements that would make the guide simpler or more clear. Also, a thank you to Kevin Parrish in the McGaughey group for helping me build LAMMPS with Reax on Gilgamesh for the first time.

### <a id="A_condensed_form_of_the_condensed_guide"></a>A condensed form of the condensed guide (tl;dr)

Environment variables required in your shell:

``` sh
#!/usr/bin/bash

# ASE LAMMPSRun
export LAMMPS_COMMAND="/usr/bin/mpirun -np 4 lmp_mpi"

# LAMMPS
export LAMMPSPATH="$HOME/lib/lammps-ro"
export PYTHONPATH="$LAMMPSPATH/python:$PYTHONPATH"
export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$LAMMPSPATH/src"
```

Installation script:

``` sh
#!/usr/bin/bash
git clone git://git.lammps.org/lammps-ro.git $LAMMPSPATH
cd $LAMMPSPATH/src
make yes-python
make yes-manybody
make mpi
make mpi mode=shlib
```

### <a id="Notes_for_users_on_Gilgamesh_at_Carnegie_Mellon"></a>Notes for users on Gilgamesh at Carnegie Mellon

- Since the standard `mpirun` is a bit out of date, I define `$LAMMPS_COMMAND` using a different binary: `export LAMMPS\_COMMAND="/usr/mpi/gcc/openmpi-1.4/bin/mpirun -np 4 lmp\_mpi"`.
- If you are installing Reax, you will need to use an Intel compiler.
- For running jobs in the queue, I needed to define one more evironment variable: `export OMPI_MCA_mtl=^psm`. I believe this affects the messaging protocol of MPI.
