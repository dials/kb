---
number: DC2
name: DIALS installer
tags: proposal
---

# DC2: DIALS installer


Proposal | DIALS installer
--- | ---
Created | 2020-02-23
Status | [Accepted](https://dials.github.io/kb/core/20210225#dc2-dials-installer)

[![hackmd-github-sync-badge](https://hackmd.io/csjWOVK5Qz6MJ-52-TLfUQ/badge)](https://hackmd.io/csjWOVK5Qz6MJ-52-TLfUQ)
---


1. **WP1:** We can use [`conda-pack`](https://github.com/conda/conda-pack) to pack up the environment.
    * This does not depend on anything else really, so we can start here.
    * Having this would mean that we have a valid (non-relocated) conda environment in the installation, which would resolve eg. the readline/ncurses issues. So this has an immediate benefit.
    * The installer should be able to run offline.
    * Workflow would be:
        * use `conda-pack` to pack up the correct environment
        * unpack that manually
        * do the equivalent of the create-installer script using the `conda-pack`'d environment
    * And then on installation:
        * unpack the `conda-pack`'d environment
        * run the `conda-pack` post install fixer-upper
        * do the remainder of the DIALS installer bit
        * done
    * Also: disconnect the DIALS installer scripts from libtbx
2. **WP2:** Support an offline developer installation. Currently this is covered by downloading a release.
    * Add a `--download` option to bootstrap that
        * downloads and unpacks miniconda
        * tells miniconda to resolve and download the environment (not: install)
        * clones all sources repositories
    * Add an `--offline` option to bootstrap that
        * takes all the above files and does the base & build step
3. **WP3**: have a run-time spec and a development environment
    * Split up eg. `linux.txt` into run/dev, and then there will be some overlap between platforms and it can probably be simplified
    * Not on the critical path.
    * This would allow us to remove C compilers, alabaster, sphinx, scons, pytest, ..., and make
4. **WP4**: The installer should only install user installation, not development installation
    * Not reasonable to provide an 'upgrade' path, as that would need to rebuild everything anyway. We can provide manual instructions for converting.
    * With this we can remove all C++ source code and intermediate files

Ancillary:

* Make bootstrap print out '*you're in uncharted territory*' if you change the Python version on a release build that has otherwise pinned conda-forge packages. Unpin the CF packages.
