# waveMakerZ Tutorial

---
**Details:**
* Feature: waveMakerZ
* FeatureType: Tutorial
* Version: OFv1812
* Date: 22/02/2021
* Author: Anatoliy Khait
* Contact: haitanatoliy@gmail.com
* Status: complete

1. [Description](#description)  
1. [User Guide](#user_guide)
1. [Dependencies](#dependencies)
1. [Examples](#examples)
1. [Planned Work](#planned_work)
1. [References](#references)
1. [Comments](#comments)
---

<a name="description"/>

### Description

This tutorial shows how to use the second-order accurate wavemaker boundary condition implemented in `waveMakerZ` library. The possible motion of the generating boundary includes piston and paddle types of the wavemaker, as well as any intermediate hinged types of the wavemaker. As an input parameter, this boundary condition takes surface elevation variation in time provided at the location of the wavemaker. The surface elevation should incorporate only linear wave contributions, i.e. it should consist of free waves only. First and second-order contributions to the wavemaker motion are calculated from the provided surface elevation record involving Zakharov theory of weakly-nonlinear water waves. The accuracy of the current implementation of the wavemaker boundary condition is limited to the second order in terms of the wave steepness parameter.

More information on the theory behind `waveMakerZ` library can be found in the papers referenced below.

<a name="user_guide"/>

### User Guide

This tutorial is run using the `Allrun` script that initially cleans the directory with `Cleanall` script and copies `0.orig` to `0` directory. Then it runs sequentially `blockMesh` and `setFields` utilities, afterward standard `interFoam` solver with the linked `libwaveMakerZ.so` library. The first and second-order contributions to the wavemaker motion are calculated by the `libwaveMakerZ.so` library during execution of the `interFoam` solver and then written into the files `spectra` (spectra of the wavemaker motion) and `time_series` (time series of the wavemaker coordinates). In the end, the script `gnuplot_script.plt` is executed to produce three images: `spectrum_elevation.png`: first and second-order surface elevation at the wavemaker; `spectrum_wavemaker_displacement.png`: first and second-order spectra of the wavemaker displacements; `wavemaker_displacement.png`: time series of the first and second-order wavemaker displacements. Examples of the images are placed in the `results` directory.

The following modifications were introduced to the standard `interFoam` case in order to use `libwaveMakerZ.so` library:
 * line `libs ("libwaveMakerZ.so");` is added in the end of `system/controlDict` dictionary to link the corresponding library;
 * dictionary `constant/dynamicMeshDict` is added to enable dynamic deformation of the mesh;
 * boundary conditions `0/pointDisplacement` for the deforming mesh are added;
 * solver for `cellDisplacement` and `cellDisplacementFinal` is added to `system/fvSolution` dictionary.

Parameters of the wavemaker are set in `0/pointDisplacement` for the boundary `leftwall`. Below the description of the properties of the wavemaker boundary condition is summarized:

| Property      | Description                                       | Possible values  |
| --------------|---------------------------------------------------|------------------|
| type          | Type of the boundary condition                    | waveMakerZ       |
| motionType    | Selection of piston or hinged wavemaker           | piston / hinged  |
| secondOrder   | Enable second-order wavemaker motion              | true / false     |
| n             | Unit vector normal to the wavemaker surface       | (1 0 0)          |
| depth         | Depth of the wave tank                            | 0.75 [meters]    |
| rampTime      | Time to reach the maximum motion of the wavemaker | 3.0 [seconds]    |
| hingeLocation | Location of the hinge below the free surface      | 0.75 [meters]    |
| numHarmonics  | Number of frequency harmonics considered          | 32               |
| inputFile     | File with the linear waves surface elevation      | "signal"         |

The spectrum of the linear free waves is calculated from the time record of the surface elevation provided as the input to the boundary condition. This time record is stored in `"signal"` file. The Fast Fourier Transform from the `fftw-3.3.7` library is adopted to calculate the complex surface elevation spectrum. Depending on the content of the `"signal"` file, the resultant spectrum may incorporate thousands of harmonics leading to unnecessary computational loads. Therefore, the spectrum is truncated at a certain harmonic number that can be set with a parameter `numHarmonics` given above.

<a name="dependencies"/>

### Dependencies

| *Dependencies*        | *FeatureType*     | *Location*                 |
| -------------         |--------------     | -----                      |
| blockMesh             | utility           | local                      |
| setFields             | utility           | local                      |
| interFoam             | solver            | local                      |
| waveMakerZ            | library           | waveMakerZ_library_OFv1812 |
| libfftw3.so           | library           | local                      |

<a name="examples"/>

### Examples which use this feature

There are currently no other cases using this tutorial as a basis.

<a name="planned_work"/>

### Planned work

There is currently no planned work for this tutorial.

<a name="references"/>

### References

 * A. Khait, L. Shemer, Nonlinear wave generation by a wavemaker in deep to intermediate water depth. Ocean Engineering, 182 (2019), 222-234
 * A. Khait, Third-Order Generation of Narrow-Banded Wave Trains by a Wavemaker. Ocean Engineering, 218 (2020), 108200

----
<a name="comments"/>

### Comments

| *Date* | *Owner* | *Type* |      *Comment*                            | *Status* |
--- | --- | --- | --- | ---

----
