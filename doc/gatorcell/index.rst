GATORcell
========

GATORcell Matlab wrapper to quickly and efficiently analyse a GATOR sandwich panels using an ABAQUS FEA. This essentially consists of a series of scripts designed to create ABAQUS input files for GATOR panels, using GMSH as a 3D CAD design and meshing algorithm.

The complete GATORcell code can be found here `GATORcell GitHub page <https://github.com/aq18507/GATORcell>`_ and its accompaning documentation here `GATORcell Documentation page <https://gatorcell.rmhaerospace.com>`_. This code relies on three software packages to run. `ABAQUS/Standard <https://www.3ds.com/products-services/simulia/products/abaqus/abaqusstandard/>`_ as an FEA solver ideally with the capability of having access to ABAQUS/CAE for debugging. `GMSH <https://gmsh.info/>`_ as a meshing and command line CAD tool and finally `Matlab <https://uk.mathworks.com/>`_ to run this GATORcell scripts in. Refer to the :ref:`Compatibility` page for the tested software versions.