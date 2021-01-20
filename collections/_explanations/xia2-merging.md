---
name: Merging in xia2 — the concept of `WAVELENGTH`
---

# Merging reflection intensities in xia2 — when a `WAVELENGTH` is not a wavelength

Many crystallographers who use DIALS to integrate their rotation data don't actually interact directly with the DIALS commands.
Instead, they often use [xia2](https://xia2.github.io) to co-ordinate the different components of the DIALS pipeline automatically.
Here, we explore the concept of `WAVELENGTH` in xia2 and discover that it doesn't necessarily mean what it says.
In fact, it turns out simply to be a label denoting a group of sweeps that should be merged together.
We also learn how to manually assign sweeps of images to such groups, when we want to control how reflection intensities should be merged.
By exploring what `WAVELENGTH` is, we will also learn a little about the key features of xia2's logical model of a diffraction experiment.

## The structure of a project in xia2

To use the terminology of the `.xinfo` file, which forms the instruction list for a data processing job, xia2 organises data by `PROJECT`, `CRYSTAL`, `WAVELENGTH` and `SWEEP`.
Each xia2 run consists of a `PROJECT`, which has one or more `CRYSTAL`s, each of which may have one or more `WAVELENGTH`s, each of which in turn may have one or more `SWEEP`s.
This much is detailed in the [xia2 paper](https://journals.iucr.org/j/issues/2010/01/00/ea5113).

What isn't so clear from the paper, is that integrated data are scaled per `CRYSTAL` and merged per `WAVELENGTH`.
This probably won't surprise you if you do multiple-wavelength studies like MAD, but may not be so obvious if you don't have that background.
If your work typically involves a single sample measured at a single wavelength, perhaps with multiple sweeps, the distinction (scaling by sample but merging by X-ray wavelength) may have passed you by.

## Exploiting `WAVELENGTH` to customise merging

There are really two different ways of running xia2:
1. The standard way.
   Simply pass in data and allow xia2 (through `xia2setup.py`) to automatically construct an `automatic.xinfo` instruction list for integration and reduction of the data.
   The `PROJECT` (named `AUTOMATIC`) will have a single `CRYSTAL` (named `DEFAULT`).
   Each sweep of images will be assigned to a `SWEEP` (named `SWEEP1`–`SWEEP<n>` for _n_ sweeps).
   The image headers will be interrogated to ascertain the set of unique measurement wavelengths used, according to some tolerance for determining equivalence.
   The `SWEEP`s will be grouped by wavelength, each group being assigned, unsurprisingly, to a separate `WAVELENGTH` (if there is only one `WAVELENGTH`, it is called `NATIVE`, unless you have told xia2 that the data are anomalous, in which case it is called `SAD`, or if there are _N_ > 1 wavelengths, they are named `WAVE1`–`WAVE<N>`).
   There is an implied equivalence here between the label `WAVELENGTH` and the physical wavelength at which all the constituent `SWEEP`s were measured.
2. The _I-know-what-I'm-doing-get-out-of-my_ way.
   Write or edit a `.xinfo` file yourself.
   This allows your `PROJECT` to have multiple `CRYSTAL`s and also allows you to apportion `SWEEP`s to different `WAVELENGTH` labels, irrespective of the actual physical wavelength used to collect the data.
   For example, this means that you can retain a distinction between different groups of sweeps that you wish to merge group-by-group, rather than all together.
   You can retain this distinction even though all the data may have been collected using the same physical X-ray wavelength.
   As such, `WAVELENGTH` becomes a misnomer, it is really just a categorisation meaning 'group of `SWEEP`s to be merged together'.

Method (1) is pretty familiar if you've ever used xia2.
But you may not have been aware that the `.xinfo` file allowed for method (2).
In fact, if you knew about it at all, you might have thought the `.xinfo` instruction list was simply a legacy feature and was just part of the plumbing of method (1).
That's understandable.
Beyond the description here, method (2) is not well documented nor sufficiently well tested.  In fact, at the time of writing, it is actually a bit broken for the standard all-DIALS pipeline, see xia2/xia2#560.

## Surely that's quite complicated enough?

As an aside, just to confuse the terminology further, [CCP4](http://www.ccp4.ac.uk) uses a logical model of a diffraction experiment with [a slightly simpler hierarchy](http://legacy.ccp4.ac.uk/html/mtzformat.html#datamodel), `PROJECT`▸`CRYSTAL`▸`DATASET`, where `DATASET` combines the concepts of `WAVELENGTH` and `SWEEP`.
In order to support [the CCP4 scaling and merging tools](https://www.mrc-lmb.cam.ac.uk/harry/pre/aimless.html) in xia2, this model creeps in from time to time, such as here:
https://github.com/xia2/xia2/blob/c8e4b8fc6fd6f70b881f3ac55c85d71d0994b334/src/xia2/Schema/Interfaces/Integrater.py#L357-L364
