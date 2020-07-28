---
name: Fixing Bugs
---

# Fixing Bugs

## Introduction

Find a bug during routine work - trying to integrate multi-lattice still data fails with an error:

```
Initialising
Processing reference reflections
 read 575 strong spots
 removing 89 unindexed reflections
 removing 34 reflections marked as bad for refinement
 using 452 indexed reflections
 found 123 junk reflections
dials Internal Error:
/Users/graeme/svn/cctbx/modules/dials/array_family/boost_python/flex_reflection_table.cc(508):
DIALS_ASSERT(id[i] < num_expr) failure.
```

Looking through the code to dials.index this appears to mean that there are reflections in there which have an experiment id which does not correspond to an experiment. Reviewing dials.index.log; see

```
Refinement failed:
Too few reflections to parameterise Crystal8Phi1, Crystal8Phi2, Crystal8Phi3, Crystal8g_param_0, Crystal8g_param_1.
Try modifying refinement.parameterisation.auto_reduction options
```

Which suggests that indexing may have taken place, but not refinement. Quick jiffy script confirms that something is wrong:

```
from dials.array_family import flex
from dxtbx.model.experiment_list import ExperimentList

refl = flex.reflection_table.from_file("indexed.refl")
expt = ExperimentList.from_file("indexed.expt")

ids = set(refl["id"]) - set([-1])

for i in ids:
    e = expt[i]
```

=> create an issue:

https://github.com/dials/dials/issues/1349

This includes the input data, a script to reproduce the issue and this
script to verify the problem.

## The Fix
Make some changes to fix the issue, identifying another potential issue along the way:

https://github.com/dials/dials/issues/1351

and 

https://github.com/dials/dials/pull/1350

## The Test Data

Now this pull request may fix the issue but it would be good to ensure that it does not come back - so the data included in the example are uploaded to the dials-data-files repo:

https://github.com/dials/data-files/commit/2e890287a925b69a3b5a55284166f71c4d173978

Since they are really small this is fine and efficient, then added to the dials-data repo:

https://github.com/dials/data/pull/210

This includes a bunch of information about the data so people can see what it’s about and where it came from - this data was my data so was at liberty to make it public. This is the included information, in a yaml file:

```
name: Multiple lattices for indexing stills, from VMXi thaumatin grid scan
author: Graeme Winter (2019)
license: CC-BY 4.0
url: https://ispyb.diamond.ac.uk/dc/visit/nt24686-7/id/3906663
description: >
  Experiment and reflection file data corresponding to image 07603 (i.e.
  split 07602) from a position in a thaumatin grid scan where multiple
  crystal lattices overlap. 

data:
 - url: https://github.com/dials/data-files/raw/2e890287a925b69a3b5a55284166f71c4d173978/vmxi_thaumatin_grid_index/split_07602.expt
 - url: https://github.com/dials/data-files/raw/2e890287a925b69a3b5a55284166f71c4d173978/vmxi_thaumatin_grid_index/split_07602.refl
```

There was much to-and-fro with this as you make a PR, then merge it, then automated systems take over to annotate it and publish an update to dials-data. So after that, 

pip install -U dials_data

gives you access to the new files.

## The Test

These data can then be used to add a regression test to the PR, so not only has a bug been found and fixed, but it prevents it from coming back - this is essentially codifying the information in the original issue:

```
from dxtbx.model.experiment_list import ExperimentList
from dials.array_family import flex
import procrunner


def test_all_expt_ids_have_expts(dials_data, tmpdir):
    result = procrunner.run(
        [
            "dials.index",
            dials_data("vmxi_thaumatin_grid_index").join("split_07602.expt"),
            dials_data("vmxi_thaumatin_grid_index").join("split_07602.refl"),
            "stills.indexer=sequences",
            "indexing.method=real_space_grid_search",
            "space_group=P4",
            "unit_cell=58,58,150,90,90,90",
            "max_lattices=8",
            "beam.fix=all",
            "detector.fix=all",
        ],
        working_directory=tmpdir,
    )
    assert not result.returncode and not result.stderr
    assert tmpdir.join("indexed.expt").check(file=1)
    assert tmpdir.join("indexed.refl").check(file=1)

    refl = flex.reflection_table.from_file(tmpdir / "indexed.refl")
    expt = ExperimentList.from_file(tmpdir / "indexed.expt")

    ids = set(refl["id"]) - set([-1])

    assert max(ids) + 1 == len(expt)
```

In future the


    tmpdir

fixture will be replaced with

    tmp_path

so it's worth checking recently added tests for the best syntax to
use.

## Finishing Up

Last but not least a news fragment was added so when the change set is merged we have a record of what changed for the release notes - this is necessarily brief, but may be useful for an end-user to see that a bug they reported has been fixed:

```
dials.index: In multi-lattice indexing, ensure that reflections corresponding to lattices where refinement failed are flagged as unindexed.
```

When the PR is squash-merged the tests will be in the repository and will be copied to the release branch if the fix is cherry-picked, along with the news. As the issues were mentioned in the commit messages they will also be automatically closed, and anyone looking in the future can get more detail on what the change set was about by following those links. 

The PR can then also be discussed to achieve consensus on how the changes are implemented, which on balance usually improves the overall sanity of the change set. 

Overall the time spent documenting the issue and adding tests was much greater than the time spent fixing the actual issue, but this investment means that it does not come back and the provenance for the change set is clear. 
