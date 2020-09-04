# Geometry cleanup

Take a decent data set and deliberately screw up the imported version - change the beam centre, change the rotation axis by 90 degrees. Take a data set which is narrow (15 degrees) at 18 keV so we only use the middle of the detector to reproduce the working environment.
Look at the reciprocal lattice viewer - you see a lattice but something may not be right...
Try indexing, only get 12% of reflections so clearly something wrong
```
Refined crystal models:
model 1 (948 reflections):
Crystal:
    Unit cell: 44.2(4), 85.8(4), 86.8(5), 109.79(7), 89.9(3), 89.1(3)
    Space group: P 1
    U matrix:  {{-0.1250, -0.5716,  0.8110},
                { 0.1119, -0.8203, -0.5609},
                { 0.9858,  0.0206,  0.1664}}
    B matrix:  {{ 0.0226,  0.0000,  0.0000},
                {-0.0003,  0.0117,  0.0000},
                {-0.0002,  0.0042,  0.0122}}
    A = UB:    {{-0.0028, -0.0033,  0.0099},
                { 0.0029, -0.0119, -0.0069},
                { 0.0223,  0.0009,  0.0020}}
+------------+-------------+---------------+-------------+
|   Imageset |   # indexed |   # unindexed | % indexed   |
|------------+-------------+---------------+-------------|
|          0 |         948 |          6846 | 12.2%       |
+------------+-------------+---------------+-------------+
Saving refined experiments to indexed.expt
Saving refined reflections to indexed.refl

```
Now to figure what
With sensible paramers looking at
RMSDs by experiment:
```
+-------+--------+----------+----------+------------+
|   Exp |   Nref |   RMSD_X |   RMSD_Y |     RMSD_Z |
|    id |        |     (px) |     (px) |   (images) |
|-------+--------+----------+----------+------------|
|     0 |   1500 |  0.15639 | 0.095739 |    0.12425 |
+-------+--------+----------+----------+------------+
Refined crystal models:
model 1 (7585 reflections):
Crystal:
    Unit cell: 68.244(9), 68.320(5), 68.268(5), 109.4591(9), 109.456(4), 109.438(4)
    Space group: P 1
    U matrix:  {{ 0.2654,  0.6789, -0.6846},
                {-0.3832,  0.7258,  0.5712},
                { 0.8847,  0.1107,  0.4528}}
    B matrix:  {{ 0.0147,  0.0000,  0.0000},
                { 0.0052,  0.0155,  0.0000},
                { 0.0090,  0.0089,  0.0179}}
    A = UB:    {{ 0.0013,  0.0044, -0.0123},
                { 0.0033,  0.0164,  0.0102},
                { 0.0176,  0.0058,  0.0081}}
+------------+-------------+---------------+-------------+
|   Imageset |   # indexed |   # unindexed | % indexed   |
|------------+-------------+---------------+-------------|
|          0 |        7585 |           208 | 97.3%       |
+------------+-------------+---------------+-------------+
Saving refined experiments to indexed.expt
Saving refined reflections to indexed.refl
```
Anyhoo...
(i) which axis is the rotation axis?
Reflections perpendicular to the rotation axis go through Ewald sphere faster than those close to the axis - so spots above and below the axis should have flatter shoeboxes compared with those along the axis.
```python
from dials.array_family import flex
data = flex.reflection_table.from_file("strong.refl")
bb = data["bbox"].parts()
flex.min(bb[4]), flex.max(bb[5])
bb[4] are the starts of bbox, [5] end - so width is difference:
width = bb[5] - bb[4]
flex.max(width)
```
=> 31
Look at a histogram -
```python
widths = flex.histogram(width.as_double(), data_min=0, data_max=40, n_slots=40)
for c, n in zip(widths.slot_centers(), widths.slots()):
    print(f"{c} {n}")
```
```
0.5 0
1.5 2217
2.5 2022
3.5 1500
4.5 1099
5.5 604
6.5 204
7.5 57
8.5 24
9.5 21
10.5 6
11.5 12
12.5 5
13.5 4
14.5 7
... etc ...

```
OK, so we care about the x, y locations as well:
```python
x, y = data["xyzobs.px.value"].parts()[:2]
```
Let's see where the widest points are?
```python
from matplotlib import pyplot
fig = pyplot.figure()
axis = fig.add_subplot(111, projection="3d")
axis.scatter(x.as_numpy_array(), y.as_numpy_array(), width.as_numpy_array(), marker=".")
pyplot.show()
```
Rotate to find which way the axis points... 
But we're only really interested in the widest reflections so... with recourse to the histogram above:
```python
sel = width > 5
width = width.select(sel)
x = x.select(sel)
y = y.select(sel)
```
=>
![](https://raw.githubusercontent.com/dials/kb/master/collections/_howtos/geometry_wideboxen.png)

We can see that the rotation axis is roughly aligned with the X axis on the detector, so it's +/- (1, 0, 0)
=> edit imported.expt to set this to (1, 0, 0) say (which happens to be right here but that is not relevent)
Load spots and imported into reciprocal lattice viewer again - mash "Invert rotation axis" to figure out which way looks right... remember the beam centre is off too so it won't look super right. The correct way should look regular grid like, the inverse kind of squiggly.
Have a go at indexing: this may be not quite right because of the incorrect beam centre but it should do _something_ now.
Next: dials.check_indexing_symmetry - pass in indexed.{expt,refl} - and grid=2 (say) - this will look at changing the offsets for assigning Miller indices and compute CC on the spot intensity against it's Miller-inverted opposite - if you have it indexed right the 0 0 0 offset should be best - is not the case here:
```
 0 -1 -1    210 0.003(5)
 0 -1  0    108 0.016(9)
 0  0 -2    922 0.0650(11)
 0  0 -1    756 0.0988(13)
 0  0  0    572 0.0116(18)
 0  0  1    382 0.020(3)
 0  0  2    218 0.289(4)
 0  1 -2   1462 0.199
 0  1 -1   1338 0.105
 0  1  0   1156 0.004
 0  1  1    952 0.0097(11)
```
Could be that 0, 0, 2 is the right hkl offset to use here?
OK, reminder of the indexing results above
RMSDs by experiment:
```
+-------+--------+----------+----------+------------+
|   Exp |   Nref |   RMSD_X |   RMSD_Y |     RMSD_Z |
|    id |        |     (px) |     (px) |   (images) |
|-------+--------+----------+----------+------------|
|     0 |   1500 |  0.19874 |   0.3272 |    0.74897 |
+-------+--------+----------+----------+------------+
```
dials.reindex hkl_offset=0,0,2 indexed.refl
=> reindexed.refl
Then refine to see if this has helped:
dials.refine scan_varying=false indexed.expt reindexed.refl  
Nope, way worse:
```
+-------+--------+----------+----------+------------+
|   Exp |   Nref |   RMSD_X |   RMSD_Y |     RMSD_Z |
|    id |        |     (px) |     (px) |   (images) |
|-------+--------+----------+----------+------------|
|     0 |   5652 |   1.2234 |  0.91705 |     8.2908 |
+-------+--------+----------+----------+------------+
```
Pop some eyeballs on the dials image viewer with the reflections overlayed and it is clear something is wrong (dials.image_viewer indexed.{refl,expt})
Looks like need a way bigger grid... maybe off by 4 points? Clear some clutter by using
dials.check_indexing_symmetry grid=4 indexed.* symop_threshold=0.2
and we see:
```
-2  0 -2   2282 0.237
-2  1 -2   2456 0.205
-1 -2 -4    686 0.3807(13)
-1 -2 -3    590 0.993
-1 -2 -2    440 0.4423(18)
 0 -2 -3     36 0.34(3)
 0  0  0    572 0.0116(18)
 0  0  2    218 0.289(4)
 1 -1 -4     42 0.24(2)
```
OK, now we're on the right lines, let's look close at -1,-2,-3 with reindex / dials.refine - ohai solution:
```
+-------+--------+----------+----------+------------+
|   Exp |   Nref |   RMSD_X |   RMSD_Y |     RMSD_Z |
|    id |        |     (px) |     (px) |   (images) |
|-------+--------+----------+----------+------------|
|     0 |   5277 |  0.21776 |  0.16574 |    0.22525 |
+-------+--------+----------+----------+------------+
```
Boom! Headshot. OK, so _maybe_ the geometry is not perfect now but it is a bucket closer to where it should be. Certainly we got ourselves a right beam centre now.
Now for the proverbial "clever bit" - we goto start:
dials.index refined.expt strong.refl
This takes the original strong reflections and assigns indices to them, then refines from there. We're now even closer to the truth
```
+-------+--------+----------+----------+------------+
|   Exp |   Nref |   RMSD_X |   RMSD_Y |     RMSD_Z |
|    id |        |     (px) |     (px) |   (images) |
|-------+--------+----------+----------+------------|
|     0 |   1500 |  0.15875 | 0.097702 |    0.12028 |
+-------+--------+----------+----------+------------+
```
In my hands the detector is a bit wonky now, but that we can handle by resetting them to the canonical values in the indexed reflection file and re-refining:
```
fast_axis => 1, 0, 0
slow_axis => 0, -1, 0
```
Though when I do this the detector still seems slightly tilted, so maybe that is the truth? Turns out it probably is :-) 
Key points here:
 - the spots, once found, stay found - indices change but the x,y,z
   positions in strong.refl stay good
 - the geoetry is entirely encoded in the .expt files which are _text_
   so you can fix them
 - the worst that can happen is you have to go back to start, so play


16:26
Helpful Python script:
```bash
$ cat explore.py 
```
```python
from dials.array_family import flex
data = flex.reflection_table.from_file("strong.refl")
bb = data["bbox"].parts()
flex.min(bb[4]), flex.max(bb[5])
width = bb[5] - bb[4]
x, y = data["xyzobs.px.value"].parts()[:2]
sel = width > 5
width = width.select(sel)
x = x.select(sel)
y = y.select(sel)
from matplotlib import pyplot
fig = pyplot.figure()
axis = fig.add_subplot(111, projection="3d")
axis.scatter(x.as_numpy_array(), y.as_numpy_array(), width.as_numpy_array(), marker=".")
axis.set_xlabel("X")
axis.set_ylabel("Y")
axis.set_zlabel("Width")
pyplot.show()
```

16:26
Beware, drink at least a coffee before embarking on this quest

16:27
Yeh I I should turn this all into a proper magic program for figuring things out

