# `cgp-vec`: Vectorized Cartesian Genetic Programming

`cgp-vec` is a small library implementing Cartesian Genetic Programming (CGP)
parallelized over multiple independent populations, using PyTorch as a backend 
for basic vectorized operations.

# Benchmarks

More detailed benchmarks will be available soon. Testing the examples on a 
Google Colab GPU runtime led to execution times in the order of minutes for 
1000 runs of 500 evolution steps of CGP on Koza symbolic regression problems, 
with configurations similar to those in [this GECCO '07 paper](https://dl.acm.org/doi/10.1145/1276958.1277276), which uses bigger populations than 
most CGP literature.

# Development status

The library is still in a very early development stage. The provided 
functionality works as intended, and is sufficient to implement common CGP 
experiments; however, for now it only supports CGP configurations with 1-row 
genotypes and no "levels back" parameter. This is the most common CGP 
configuration, since it can encode any (bounded) directed acyclic graph. Some 
minor unit tests are also missing.

# Planned features

- support for all classical CGP configurations (multiple rows, levels back 
  parameter);

- explicit generation of efficient (vectorized) phenotypes, in the form of 
  either python callables or PyTorch modules;

- notable CGP variants from J. Miller's book "Cartesian Genetic Programming";

- notable CGP crossover variants;

- convenience functions (vectorized) for common CGP problems: symbolic 
  regression losses, etc.

# Installation

While this repository contains a proper Python package, it is not yet 
registered on PyPy, so it must be installed via cloning and `pip install .` for 
now; the only external dependency is the PyTorch library. This library has only 
been tested on the major Python versions 3.7 and 3.10.

Unit tests are in the `tests` directory; to run all of them, one can run `python -m unittest -v "tests.test_cgpv"` at the top directory. 

# Usage

Proper API documentation will be available soon. For now, an overview of the 
API is given below. Full symbolic regression examples are also available in the 
`examples` directory.

The whole API is contained in the `cgpv` package. The vectorized CGP operations 
are available either as simple functions, or as methods of the `Populations` 
class; most users may find the latter more convenient.

The following (vectorized) operations are available:

- counting the number of (valid) alleles for each locus: `count_alleles` (performed automatically when creating new `Populations` objects, if `n_alleles` is not provided);

- generation of multiple random populations: `random_populations` or `Populations.random` (static method);

- random mutation: `mutate` and `Populations.mutate`;

- evaluating populations on a tensor input: `eval_populations` or simply calling a `Populations` object (since it implements `__call__`);

- roulette-wheel selection: `roulette_wheel` or `Populations.roulette_wheel`;

- plus-selection: `plus_selection` or `Populations.plus_selection`;

## More on `Populations` objects

`Populations` objects also provide a `fitnesses` tensor attribute for convenience, used to store fitness matrices for the populations. If set, this attribute is then used by the selection methods.

To avoid doing a lot of things twice or copy the same tensors around too much, 
most methods that return new populations (including the `__init__` method) 
don't make any attempt to deepcopy the given objects, so the configuration 
attributes of different populations may point to the same objects/tensors. This 
is not a problem for intended use cases, but users should be aware of it; it 
also may change in the future.

Seed parity between the methods of `Populations` and corresponding functions 
can be explicitely tested on CPU devices in the unit tests; note, however, that 
running on CUDA devices may break reproducibility anyway (see the [PyTorch 
documentation on reproducibility](https://pytorch.org/docs/stable/notes/randomness.html).
