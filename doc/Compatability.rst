Comapatbility
=============

Current Version
---------------

The latest GATORcell version relies on the following scripts and versions:

+---------------------------------------------------------------+
| GATORcell Version: |ProjectVersion|                           |
+-------------------+---------------+---------------------------+
| Script            | Major Version | Script Name               |
+===================+===============+===========================+
| makeModel         |   4           | ``makeModel4.mlx``        |
+-------------------+---------------+---------------------------+
| InputCondtioner   |   6           | ``InputCondtioner6.mlx``  |
+-------------------+---------------+---------------------------+
| Matlab2Abaqus     |   1           | ``Matlab2Abaqus.mlx``     |
+-------------------+---------------+---------------------------+
| runAbaqus         |   4           | ``runAbaqus4.mlx``        |
+-------------------+---------------+---------------------------+
| unitcell          |   7           | ``unitcell7.geo``         |
+-------------------+---------------+---------------------------+

GATORcell relies on a number of scripts that are compatible with each other. The version of each script is structured as ``v.[MainVersion].[Subversion]``. The compatibility only ever refers to the main version. since there might be models that are created on older versions it was decided to create new scripts/function names by specifying the version in their name with the following convention: ``[ScriptName][Version].mlx`` noting that version ``1`` is shown as ``[ScriptName].mlx``. The same applies to the GMSH shape files with the ``.geo`` suffix.

GATORcell is a wrapper program written in Matlab. While it is likely that it works on any older, as well as future versions it has to be noted that it is only tested and proven to work on the version listed below. The same applies to both GMSH and ABAQUS. This was initially written in the GMSH version ``4.9.3``. This means that it will be backwards compatible to this version. GMSH has since completely rewritten the seeding algorithm which produces a much better mesh from version ``4.10.5`` onwards with a significantly faster meshing algorithm. It is therefore advised to use the version in the table below.

+-----------------------------------+
| Software Versions                 |
+---------------------+-------------+
| Software Package    | Version     |
+=====================+=============+
| Matlab              | ``R2022a``  |
+---------------------+-------------+
| GMSH                | ``4.11.0``  |
+---------------------+-------------+
| ABAQUS              | ``R2018x``  |
+---------------------+-------------+

.. warning::
    The current versions of GATORcell have only ever been tested with the script version combination shown above. While great care was taken that they are backwards compatible it has not been tested.

Compatability Archive
---------------------

This section lists all older versions. This is for reference only.

GATORcell Version: v.6.220609
+++++++++++++++++++++++++++++

+-------------------+---------------+---------------------------+
| Script            | Major Version | Script Name               |
+===================+===============+===========================+
| makeModel         |   4           | ``makeModel4.mlx``        |
+-------------------+---------------+---------------------------+
| InputCondtioner   |   6           | ``InputCondtioner6.mlx``  |
+-------------------+---------------+---------------------------+
| Matlab2Abaqus     |   1           | ``Matlab2Abaqus.mlx``     |
+-------------------+---------------+---------------------------+
| runAbaqus         |   4           | ``runAbaqus4.mlx``        |
+-------------------+---------------+---------------------------+

GATORcell Version: v.5.220109
+++++++++++++++++++++++++++++

+-------------------+---------------+---------------------------+
| Script            | Major Version | Script Name               |
+===================+===============+===========================+
| makeModel         |   4           | ``makeModel4.mlx``        |
+-------------------+---------------+---------------------------+
| InputCondtioner   |   5           | ``InputCondtioner5.mlx``  |
+-------------------+---------------+---------------------------+
| Matlab2Abaqus     |   1           | ``Matlab2Abaqus.mlx``     |
+-------------------+---------------+---------------------------+
| runAbaqus         |   4           | ``runAbaqus4.mlx``        |
+-------------------+---------------+---------------------------+
