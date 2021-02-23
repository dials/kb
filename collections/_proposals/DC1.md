---
name: DC1: per-module `setup.py`
tags: proposal
---

# DC1: per-module `setup.py`

The intention is to create a `setup.py` file for DIALS in the `modules/dials/` directory that is generated, installed, and `develop`'d through `dials/libtbx_refresh.py`

1. Making `import` work with standard `conda/bin/python` - no `libtbx.python` dispatcher required.
    * this means we no longer need to generate a `pytest` dispatcher
    * this makes all normal python dispatchers able to import dials/dxtbx/xia2/..., which is relevant for all external packages that make use of DIALS and enables them to be absolute standard Python packages with zero required additional dark magic. For example such packages are `DUI`, `fast_dp`, `iota`, `screen19`, ...
    * with this activating the conda environment would be sufficient to set everything required in the environment, thus there is no more need to generate special libtbx dispatchers
2. Metadata -- generating the dials and dxtbx format entrypoints as part of this new `setup.py`
    * this makes for example the dials version number accessible to other packages in the regular packaging way, which allows external packages to declare version dependencies (eg. `iota` V1.7 requiring `DIALS>=2.2<4.0`)
4. Generate standard python dispatchers from `command_line/*.py`
    * This requires restructuring code inside the `command_line/` files in order to provide a named entry point for the dispatcher to call into.
    * This can be done on a per-package basis, and is trivial for DIALS, xia2, and dxtbx.
1. Wherever we import `*ext*` modules, do it in a
```python
try:
    from . import dials_whatev_ext
except ModuleNotFoundError:
    import dials_whatev_ext
```
way. That allows us to build and/or distribute in-source builds at some point. This should be very straightforward as we already agreed that we only import the C++ libraries in a single place, and indirectly import from this place everywhere.

5. If we supported version numbered dependencies for DIALS then at that point we could transform xia2 into a regular python package with normal releases on pypi and conda-forge.

Considered alternatives:

* put `setup.py` in the `build/` directory. This is an option if the source repository can not be used for any particular reason (ie. incomplete environment available at the time). We would lose version control and entry points would not be in a module `setup.py` where people may expect them.

