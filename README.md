# Abaqus UEL Elasticity
 
This repository contains user element subroutines (UEL) for Abaqus/ Standard written in Fortran for small strain isotropic linear elasticity. Standard displacement-based element formulation has been used. More details of the subroutine file are given in the next section. The purpose of the project is to make users familiar with developing user element subroutines (UEL) in Abaqus using a simple standard case. This source code contains necessary subroutines related to element operations and matrix algebra. Once comfortable, users can easily repurpose this source code as a template for their element.




## Obtaining the file

If you have `git` installed, you can clone the repository to your local machine using
```bash
git clone https://github.com/bibekananda-datta/Abaqus-UEL-Elasticity.git
```
Alternatively, you can download the files in a `zip` folder in this repository using the `code` drop-down menu on the top right corner

To receive updates in this repository, you can also `fork` the repository and sync as updates are deployed, develop your code by creating a separate branch, and propose updates using the `pull` and `merge` features of GitHub.



## Description of the repository

### `uel_mech.for` subroutine

As a default, Abaqus prefers the user subroutines to be written in Fortran fixed form (F77 standard) while common features of modern Fortran can be included if the compiler allows. The element formulation is standard and can be found in any standard finite element textbook. The user element includes 4 types of 2D continuum solid elements (Tri3, Tri6, Quad4, and Quad8) in plane-strain and 4 types of 3D continuum solid elements (Tet4, Tet10, Hex8, Hex10). These elements can be used in both reduced and full integration schemes. It is up to the user to specify the correct number of integration points for each element. Body force and traction boundary conditions have not been implemented in this user subroutine, however, these can be applied by overlaying standard Abaqus elements on the user elements (to be discussed in the **Visualization** section).

The isotropic Hooke's law describes the constitutive law for the material. Since Abaqus/ Viewer does not provide native support for visualizing user elements, an additional layer of elements with the same element connectivity has been created and results at the integration points of the elements are stored using the `UVARM` subroutine.




> [!NOTE]
> As of now, plane stress and axisymmetric elements are not available, but users can easily extend the subroutine to include these capabilities.
> The constitutive behavior of the material can be extended to include viscoelasticity, plasticity, and uncoupled thermoelasticity.

> [!WARNING]
> If the elements are used for materials near-incompressibility limit or under bending case scenarios, the user may want to opt for higher-order elements. Please remember that these are standard displacement-based element formulations, and no special treatment for shear locking, hourglass modes, or volumetric locking is available.

> [!TIP]
> This file contains subroutines to obtain the Gauss quadrature information, interpolation functions for 2D and 3D Lagrangian elements and matrix operations. Users are highly recommended to go through these subroutines and can repurpose them to write their user element (UEL) code.



### Example input files

#### Properties

A few sample input files are provided for different element types to demonstrate the usage of the user element subroutine capabilities in Abaqus. Users can use Abaqus/ CAE to create a standard Abaqus model and export `.inp` files. The name of the provided files indicates the type of elements being used and its equivalent counterpart in Abaqus which is used for visualization. Users are highly recommended to go through the input file to understand different element types and element and material properties being used. 

It is recommended to use the option **Do not use parts and assemblies in input files** from the **model attributes** drop-down menu before exporting the input file. This option will generate a cleaner input file which will be easy to modify. Once the standard input file is exported from Abaqus, the user will need to modify it to use it with the UEL. For isotropic linear elastic elements, users need to specify Young's modulus and Poisson ratio as the real properties. Additional integer properties to be specified are the number of integration points, `nInt`, and the number of variables to be post-processed at the integration points `nPostVars`.


#### Visualization

An additional set of elements with the same element connectivity as the user element has been created in the input file to visualize the results. This technique was used since the programmed elements have the same interpolation functions and integration points as built-in continuum elements in Abaqus. These additional elements (so-called dummy elements) have negligible elastic properties and thus will not affect the results. If you are using a reduced integration element from the user subroutine, then use the same type of element from Abaqus as dummy elements.


> [!NOTE]
> If the user elements have different nodal connectivity and integration points than the standard Abaqus elements, then this technique can not be used. In that case, the user will need to store the variables at the integration point using `SVARS` and post-process using a separate Python script (too complicated unless you need to!).

> [!TIP]
> User can specify body force and traction/ pressure type boundary conditions on the dummy elements since they share the same element connectivity, it will be included in the calculation.

> [!TIP]
> For larger models, the user can write Python or MATLAB script which will parse the ABAQUS-generated input file and add additional dummy elements. This will ease the pre-processing procedure. For example, check `addElemMech.py` which can parse a simple input file to generate the overlaying dummy elements on the user elements.




## Configuring Abaqus and executing the subroutines

To run user subroutines in Abaqus, you will need to install Intel Visual Studio and Intel oneAPI package and link them with Abaqus. Follow [thus tutorial](https://www.bibekanandadatta.com/blog/2021/link-intel-and-vs-abaqus-2020/) if you have not done it yet. There might be other similar resources available to help you with configuring Abaqus for subroutine compilation and execution.


Make sure that the user subroutine and input file are in the same directory. Using the `Abaqus command line terminal` or `cmd` or `PowerShell`, you can execute the following command from the directory to execute the subroutine.

```bash
abaqus interactive double analysis ask_delete=off job=your_job_name input=input_file_name.inp user=uel_mech.for cpus=no_of_processor
```
Specify the variable names in the above command as needed.

If you use the PowerShell-based terminal, you can also execute the subroutine by running the `runAbq.ps1` file. Make sure to check the input file name in the file.
```bash
./runAbq
```
For additional information on executing user subroutines, check the Abaqus/ Standard user manual.


## Documentation

Documentation for this code will be made available upon request.


## Citation

In case you use this subroutine for educational or research purposes, please cite this source.
