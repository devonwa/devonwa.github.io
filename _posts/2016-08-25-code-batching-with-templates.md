---
layout: post
title: Code batching with Jinja2 templates
tags: workflow, Python
type: article
description: We can use Jinja2 templates to submit calculations to a job queue on a remote cluster. They improve code reuse and keep big ugly strings out of your scripts.
---

Calculations involving DFT or training a neural network can take upwards of a week to complete. In the Kitchin Group, we submit these jobs to a PBS queue on a remote server cluster for processing. Often the scripts we submit vary only by a few parameters and are otherwise the same. Consider the following example of calculating energies for the equation of state of fcc gold.

``` python
from ase.lattice.cubic import FaceCenteredCubic
import numpy as np
from vasp import Vasp
from vasp.vasprc import VASPRC
VASPRC['queue.walltime'] = '10:00:00'

A = np.linspace(3.5, 4.5, 10)
for a in A:
    atoms = FaceCenteredCubic(symbol='Au', latticeconstant=a)
    calc = Vasp('~/tmp/lc={}'.format(a), atoms=atoms)
    calc.calculate()
```

This will submit 10 new jobs to the queue. Each submitted job only differs in the lattice constant of the structure. This code is easily readable and we don't have to worry about the details of submitting to the queue. In fact, the [Vasp](https://github.com/jkitchin/vasp) module provides queue configuration options in the form of `VASPRC`, which we used to tell the queue that the calculations will take 10 hours at most.

## Submitting generic calculations

But the ease of submitting Vasp jobs to the queue does not translate to other types of calculations. This is because a lot of scripts useful to scientific research don't require an entire library dedicated to handling them on a cluster. Nevertheless, they take a long time to complete and you don't want them running on your laptop for the next three days! For instance, let's say we have an ASE database full of calculations and we want to use it to train a neural network with [Amp](https://bitbucket.org/andrewpeterson/amp).

``` python
#!/usr/bin/env python
from amp import Amp
from amp.descriptor.gaussian import Gaussian
from amp.model import LossFunction
from amp.model.neuralnetwork import NeuralNetwork
from amp.utilities import Annealer
from ase.db import connect

# Get atoms from the database
images = []
db = connect(db_path)
for d in db.select('train_set=True')):
    atoms = db.get_atoms(d.id)
    images.append(atoms)

# Build Amp object
calc = Amp(label="~/tmp/amp_training",
    descriptor=Gaussian(cutoff=6.5),
    model=NeuralNetwork(hiddenlayers=(2,2)),
    cores=1)
loss = LossFunction(convergence={'energy_rmse': energy_rmse,
                                'force_rmse': 1})
calc.model.lossfunction = loss

# Perform simulated annealing for global search
Annealer(calc=calc, images=images)

# Train the network
calc.train(images=images)
```

This will train our network, but how do we submit it to the queue? If we save the script to a .py file, we can submit that, but if we want to change any parameters, we will have to save a new copy of the script each time. That is not ideal for code reuse and will not be pleasant to maintain.

Another option is to wrap the script in a string and change parameters by formatting the string. Then we can save each string to a .py file and submit them to the queue in a batch. But I want my code to be free of big ugly strings being used in complicated ways. So let's use a template!

## A template for training a neural network

By using [Jinja2 templates](http://jinja.pocoo.org/docs/dev/) for the scripts we want to produce, we can pass parameters into the script with a dictionary. Below is the previous example written as a template.

``` python
#!/usr/bin/env python
from amp import Amp
from amp.descriptor.gaussian import Gaussian
from amp.model import LossFunction
from amp.model.neuralnetwork import NeuralNetwork
from amp.utilities import Annealer
from ase.db import connect
import os

# Required arguments from template
framework = {{ framework }}
work_dir = "{{ work_dir }}"
db_path = "{{ db_path }}"
selection = {{ selection }}
label = "{{ label }}"
dblabel = "{{ dblabel }}"
cutoff = {{ cutoff }}
energy_rmse = {{ energy_rmse }}
force_rmse = {{ force_rmse }}
cores = {{ cores }}

# Set working directory
os.chdir(work_dir)

# Get atoms from the database
images = []
db = connect(db_path)
for d in db.select(selection):
    atoms = db.get_atoms(d.id)
    del atoms.constraints
    images.append(atoms)

# Build Amp object
framework_str = "-".join([str(f) for f in framework])
label = label
dblabel = dblabel
desc = Gaussian(cutoff=cutoff)
model = NeuralNetwork(hiddenlayers=framework)
calc = Amp(label=label,
        dblabel=dblabel,
        descriptor=desc,
        model=model,
    cores=cores)
loss = LossFunction(convergence={'energy_rmse': energy_rmse,
                                'force_rmse': force_rmse})
calc.model.lossfunction = loss

# Perform simulated annealing for global search
Annealer(calc=calc, images=images)

# Train the network
calc.train(images=images)
```

The parameters at the top of the script require the variables in the double curly brackets to be provided at the time of rendering the template. Other than that, the template looks the same as the previous script.

## Submitting a job using a template

So we have a template that can take a few different parameters which will affect the training of our neural network. Let's submit some jobs to the queue using our template to train several networks with different hidden layer frameworks.

``` python
from jinja2 import Environment, PackageLoader
import os
import twodee as td

# Load the template from the templates folder
env = Environment(loader=FileSystemLoader('~/tmp/templates'))
template = env.get_template("nn_build.py")

# Create 10 NN frameworks
frameworks = []
for i in range(1,11):
    frameworks.append((i, i))

# Iterate over the NN frameworks
for framework in frameworks:
    # Create paths for directories for each NN framework
    framework_str = "-".join([str(f) for f in framework])
    nn_dir = os.path.join("networks/", framework_str)
    label = os.path.join(nn_dir, framework_str)
    dblabel = os.path.join(iter_dir, os.path.basename(iter_dir))

    # Template required parameters
    args = {'framework': framework,
            'work_dir': os.getcwd(),
            'db_path': "database/master.db",
            'selection': ["train_set=True"]
            'label': label,
            'dblabel': dblabel,
            'cutoff': 6.5,
            'energy_rmse': 1e-2,
            'force_rmse': 5e-2,
            'force_rmse': None,
            'cores': 1}

    # Build the script using the provided arguments
    code = template.render(**args)

    # Save the rendered template to a file
    submit_file = os.path.join(nn_dir, 'submit.py')
    with open(submit_file, 'w') as f:
        f.write(code)

    # Submit the file to the queue
    td.qsub(submit_file, walltime="168:00:00", ppn=args['cores'])
    print('{0} submitted.'.format(framework_str))
```

This script iterates over 10 hidden layer frameworks and renders a template for each of them. It saves the rendered template to a python file and then submits that file to the queue with the `td.qsub` function from the twodee module. [Twodee](https://github.com/devonwa/twodee) contains macros that I find useful in my research and is freely available on Github.

This post is meant to be an introduction to using templates for a single purpose but there is a lot more to Jinja2 templates than what I am showing here. They can include logic, like if statements and for loops, which you can use to create some really powerful generic templates. But that's for another day!
