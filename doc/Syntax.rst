Syntax
======

The syntax described in this section describes how a Matlab script has to be structured for it to translate into an ABAQUS input file. This only ever refers to the latest version of GATORcell.

Generally, there is no prescribed order of how the syntax is structured in the Matlab file unless specified explicitly in this documentation.

The syntax described in this set of documenattion always referrs to the latest version of the files described on this page. **Add reference to page**

.. -------------------------------------------------------------------------------------------------------------------------------
Parts
-----

There can be 2D and 3D parts defined within GATORcell. They refer to the individual parts generated in GMSH, which is done using PartID's. The general form is that the *Core* has the PartID ``Core``, *Skin_1* = ``Skin0`` and the *Skin_2* = ``Skin1``.  GMSH numbers from the bottom-up, which means that ``Skin0`` is the one closer to the datum plane and ``Skin1`` furthest away from the datum plane. The total number of parts must be defined within the struct with the following syntax. The number must be an integer equal to the number of parts, which may be greater than just the core and the two skins.

.. code-block:: matlab
	
	Input.NumberOfParts = [NumberOfParts];

Global syntax
+++++++++++++

The global settings apply for both the **3D** and **2D** options. 

**Name:** This is the name that will be shown in the ABAQUS .inp file

.. code-block:: matlab

	Input.Part_[n].Name = "[Name]";

**PartID:** The part ID is the identification name assigned by GMSH. For the Core it simply is ``Core``. The Part ID can be found in the struct under ``Input.Mesh``. In all listings, there are part IDs available. This must be in a string format.

.. code-block:: matlab

	Input.Part_[n].PartID = "[PartID]";
	
**Material:** This associates the material type to the part. Refer to the material definition section for how to define the material [Link to the Materials section](##Materials). The input format is a :math:`1 \times 1`` numeric and must refer to an existing material number.

.. code-block:: matlab

	Input.Part_[n].Material = [MaterialNumber];

Optional global Syntax
++++++++++++++++++++++

There are some optional commands that can be used globally.

**NewElementType:** This commad does nothing else than replace the element type. It has to be noted that in order for it to work it has to be a compatible element but with a different name. They have to be the same amount of nodes with the same numbering convention.

.. code-block:: matlab

	Input.Part_[n].NewElementType = "[NewElementType]";
	
3D Parts
++++++++

**ElementType:** The element type should match the main 3D element type listed in the partID. There can be multiple element types within the same partID. GATORcell has only ever been tested with ``C3D10`` elements. This does not mean that it would not work with any other elements. 

.. code-block:: matlab

	Input.Part_[n].ElementType = "C3D10";

.. code-block:: matlab
	:caption: Example of a 3D part definition

	% Part name
	Input.Part_1.Name = "UnitCell";
	% Part ID
	Input.Part_1.PartID = "Core";
	% Element Type
	Input.Part_1.ElementType = "C3D10";
	% Material
	Input.Part_1.Material = 1;

2D Parts
++++++++
	
**ElementType:** The element type should match the main 2D element type listed in the partID. There can be multiple element types within the same partID. GATORcell has only ever been tested with ``CPS6`` elements and is likely not working with any other elements, unless the ``InputConditionerX.mlx`` is reprogrammed to handle the desired element type.

.. code-block:: matlab

	Input.Part_[n].ElementType = "CPS6";

**LowerOrderElement:** The lower element order simply prints the element type into the ``.inp`` file. This means that the translated element must be compatible with the **ElementType** specified above. This statement initiates the actual conversion from the **ElementType** in the partID to the lower order element. The element tried as part of this work is ``S3``.

.. code-block:: matlab
	
	Input.Part_[n].LowerOrderElement = "[LowerOrderElement]";

**Thickness:** This defines the skin thickness. This must be a numeric value of dimension :math:`1 \times 1`.

.. code-block:: matlab
	
	Input.Part_[n].Thickness = [Thickness];

**IntegrationPoints:** This defines the ABAQUS number of integration points `ABAQUS Documentation Integration <https://classes.engineering.wustl.edu/2009/spring/mase5513/abaqus/docs/v6.6/books/gsa/default.htm?startat=ch04s01.html>`_. This must be a numeric value of dimension :math:`1 \times 1`.

.. code-block:: matlab

	Input.Part_[n].IntegrationPoints = [IntegrationPoints];

.. code-block:: matlab
	:caption: Example of a 2D part definition

	% Part name
	Input.Part_2.Name = "Skin-1";
	% Part ID
	Input.Part_2.PartID = "Skin0";
	% Element Type
	Input.Part_2.ElementType = "CPS6";
	% Lower order Element
	Input.Part_2.LowerOrderElement = "S3";
	% Material
	Input.Part_2.Material = 2;
	% Membrane Thickness
	Input.Part_2.Thickness = 0.2;
	% Thickness Integration Points
	Input.Part_2.IntegrationPoints = 5;
.. -------------------------------------------------------------------------------------------------------------------------------
Boundary Conditions
-------------------

.. -------------------------------------------------------------------------------------------------------------------------------
Surfaces
--------

This section describes howsurfaces cane be defined. They can be defined both in **2D** and **3D** models but the syntax in GATORcell differs. Note Surfaces by defaut are alyays 2D, which initiatest an issue for Shells and Membranes. However, a 3D surfce is more complicated to define as the orientation of the 3D element matters. This is why they are sepperated here.

Global Syntax
+++++++++++++

The global settings apply for both the **3D** and **2D** parts. 

**Name:** This is the name that will be shown in the ABAQUS .inp file.

.. code-block:: matlab

	Input.Surface_[n].Name = "[Name]";

**Type:** This field instructs the ``InputCondtioner`` where to get the data from. This is structured as follows:

- SurfaceDefinition → ``ELEMENT``
  
  - Dimension → ``3D``
  - Dimension → ``2D``

The term ``ELEMENT`` stands for that the surface is defined using Elements and the term ``2D`` and ``3D`` specify whether the source part is in either 2D or 3D. The sytax is as follows

.. code-block:: matlab

  Input.Surface_[n].Type = ["[SurfaceDefinition]" "[Dimension]"];

**Part:** Part number to which the surface is referring to. This has to be a numeric value and must be an integer.

.. code-block:: matlab

  Input.Surface_[n].Part = [PartNumber];

**Elements:** This defines where to take the elements from. The surface handler in the ``InputConditioner`` function takes the mesh data directly from the mesh. The **Elements** statement referrs to which reference set to choose from within the mesh struct (``Input.Mesh``). The shape file returns the core/skin interaces automatically and stores them in the mesh file. The core interface are named ``CoreInterface0`` and ``CoreInterface1``, where the ``0`` is referring to the skin closest to ``z0`` which is the datum in the :math:`z`-direction and ``1`` furthest away. This does however not mean that this list is exclusive. If there are any other parts defined then they can be named using this as long as there is a reference in the mesh struct.

.. code-block:: matlab

  Input.Surface_[n].Elements = "[InterfaceDefinition]";


3D Surfaces
+++++++++++

**ElementType:** Since it the surface handler takes the data form the mesh surce directly the element type must be specified.

.. code-block:: matlab

  Input.Surface_[n].ElementType = "[ElementType]";


.. code-block:: matlab
  :caption: Example of a 3D surface definition

  % Name
  Input.Surface_1.Name = 'Core-Surface-1';
  % Type of element
  Input.Surface_1.Type = ["ELEMENT" "3D"];
  % Interface Elements
  Input.Surface_1.Elements = "CoreInterface0";
  % Part Number
  Input.Surface_1.Part = 1;
  % Element Type in CoreInterface
  Input.Surface_1.ElementType = "CPS6";


2D Surfaces
+++++++++++

**(ElementType):** This is an optional statement in 2D surfaces. If the **ElementType** is omitted then *ALL* elements are added to the struct. If this term is specifed then the above applies.

**Orientation:** The orientation defines which surface points in which direction. There are two choices: 

- Positive = ``SPOS``
-  Negative = ``SNEG``

GMSH assignes the mesh such that ``SPOS`` always faces away from the :math:`z`-axis and ``SNEG`` always points towards :math:`z`. 

.. code-block:: matlab

  Input.Surface_[n].Orientation = '[Orientation]';


.. code-block:: matlab
  :caption: Example of a 2D surface definition

  % Name
  Input.Surface_2.Name = 'Membrane-Surface-2';
  % Type
  Input.Surface_2.Type = ["ELEMENT" "2D"];
  % Interface Elements
  Input.Surface_2.Elements = "CoreInterface0";
  % Orientation (SPOS = Positive/SNEG = Negative)
  Input.Surface_2.Orientation = 'SNEG';
  % Part Number
  Input.Surface_2.Part = 2;


.. -------------------------------------------------------------------------------------------------------------------------------
Materials
---------

This section describes how materials are defined. ABAQUS allows various material models to be included. A number of them are programmed into GATORcell. It can handle the following material models.

- Linear Elastic → ``Elastic``
- Hyperelastic → ``Hyperelastic``

The total number of materials must be defined within the struct with the following syntax. The number must be an integer equal to the number of materials.

.. code-block:: matlab

  Input.NumberOfParts = [NumberOfParts];

Global syntax
+++++++++++++


The global settings apply to all materials. 

**Name:** This is the unique name that will be shown in the ABAQUS .inp file

.. code-block:: matlab

	Input.Material_[n].Name = "[Name]";

**Behaviour:** This sets the material model, which is either ``Elastic`` or ``Hyperelastic``. It is important to note that the first letter is capitalised.

.. code-block:: matlab

	Input.Material_[n].Behaviour = '[MaterialModel]';


Linear elastic Materials
++++++++++++++++++++++++

**E:** This setting defines the Young's modulus. It must be a numeric value of dimension :math:`1 \times 1`.

.. code-block:: matlab
	
	Input.Material_[n].E = [E];

**nu:** This defines the Poisson's ratio :math:`\nu` of the material. It must be a numeric value of dimension :math:`1 \times 1`.

.. code-block:: matlab
	
	Input.Material_[n].nu = [nu];

.. code-block:: matlab
  :caption: Example for a Linear Elastic Material

  % Material Name
  Input.Material_2.Name = 'Ninjaflex';
  % Material behaviour
  Input.Material_2.Behaviour = 'Elastic';
  % Youngs Modulus
  Input.Material_2.E = 22.9;
  % Poisson's Ratio
  Input.Material_2.nu = 0.43;


Hyperelastic Materials
++++++++++++++++++++++

This function can handle three different material models, ``mooney-rivlin``,  ``polynomial`` which in fact is a higher-order Mooney-Rivlin model and the ``ogden`` model. Since they are defined all somewhat differently they shall be described individually here. 

**Model:** The model can be defined with the following command using the appropriate model description.

- ``mooney-rivlin``
- ``polynomial``
- ``ogden``

.. code-block:: matlab

	Input.Material_[n].Model = '[Model]';

Mooney-Rivlin
*************

This is the other parameter that is required if the ``mooney-rivlin`` model was chosen

**Constants:** This defines the elastic Mooney-Rivlin constants in the format :math:`C10, C01, D1`. Important is that the sequence is exactly how it is shown here. It has to be in a numeric format of dimension :math:`3 \times 1`.

.. code-block:: matlab
  :caption: Example for a Hyperelastic Mooney-Rivlin model

  % Material Name
  Input.Material_2.Name = 'Ninjaflex';
  % Material behaviour
  Input.Material_2.Behaviour = 'Hyperelastic';
  % Material Model
  Input.Material_2.Model = 'mooney-rivlin';
  % Constants [C10, C01, D1]
  Input.Material_2.Constants = [0.5034 3.1410 0];

Polynomial
**********

These are the other parameters that are required if the ``polynomial`` model was chosen

**Order:** This setting defines the order. Important to note here is that it can be in any order, however, the **Constants** which is defined below must be in the same order as the ABAQUS definition. For further information please consult the ABAQUS documentation `ABAQUS Hyperelastic material guide <https://classes.engineering.wustl.edu/2009/spring/mase5513/abaqus/docs/v6.6/books/usb/default.htm?startat=pt05ch17s05abm07.html#usb-mat-chyperelastic>`_. The input must be an integer.

.. code-block:: matlab

	Input.Material_[n].Order = [Order];

**Constants:** The constants are numeric values that define the material's behavior. It must be in the correct format match in the **Order** defined above, which is for a `2`:superscript:`nd` order polynomial the following :math:`C10, C01, C20, C11, C02, D1, D2`. The input format is numeric and of the size :math:`x \times 1` where :math:`x` is the number of constants.


.. code-block:: matlab
  :caption: Example for a Hyperelastic Polynomial model

  % Material Name
  Input.Material_2.Name = 'Ninjaflex';
  % Material behaviour
  Input.Material_2.Behaviour = 'Hyperelastic';
  % Material Model
  Input.Material_2.Model = 'polynomial';
  % Ogden model order
  Input.Material_2.Order = 2;
  % Constants [C10 C01 C20 C11 C02 D1 D2]
  Input.Material_2.Constants = [-4.50465 9.002102 -0.2079786 0.9386004 0.1804305 0 0];

Ogden
*****

These are the other parameters that are required if the ``ogden`` model was chosen

**Order:** This setting defines the order. Important to note here is that it can be in any order, however, the **Constants** which is defined below must be in the same order as the ABAQUS definition. For further information please consult the ABAQUS documentation `ABAQUS Hyperelastic material guide <https://classes.engineering.wustl.edu/2009/spring/mase5513/abaqus/docs/v6.6/books/usb/default.htm?startat=pt05ch17s05abm07.html#usb-mat-chyperelastic>`_. The input must be an integer.

.. code-block:: matlab

	Input.Material_[n].Order = [Order];

**Constants:** The constants are numeric values that define the material's behavior. It must be in the correct format match in the **Order** defined above, which is for a `3`:superscript:`rd` order polynomial the following :math:`\mu1, \alpha1, \mu2, \alpha2, \mu3, \alpha3, D1, D2, D3`. The input format is numeric and of the size :math:`x \times 1` where :math:`x` is the number of constants.

.. code-block:: matlab
  :caption: Example for a Hyperelastic Ogden model

  % Material Name
  Input.Material_2.Name = 'Ninjaflex';
  % Material behaviour
  Input.Material_2.Behaviour = 'Hyperelastic';
  % Material Model
  Input.Material_2.Model = 'ogden';
  % Ogden model order
  Input.Material_2.Order = 3;
  % Constants [mu1 alpha1 mu2 alpha2 mu3 alpha3 D1 D2 D3]
  Input.Material_2.Constants = [-142.70 0.60 54.15 1.18 96.39 -7.46E-02 1.03E-02 0 0];
 

.. -------------------------------------------------------------------------------------------------------------------------------
Sets
----

.. -------------------------------------------------------------------------------------------------------------------------------
Loads
-----

- Pressure
The list above signifies all the loads which are programmed into GATORcell. They have to be defined as follows in Matlab

Pressure
+++++++++

**Name:** This is the name that will be shown in the ABAQUS .inp file

.. code-block:: matlab

	Input.Load_[n].Name = "[Name]";


**Type:** This defines the type of load. In this case, it is Pressure which is defined in the first statement. The second statement defines which kind of pressure load.
The following subset loads types are preprogrammed with the syntax:

- Surface Load  →  ``SurfaceLoad``

.. code-block:: matlab

	Input.Load_[n].Type = ["[Pressure]" "[SubsetLoad]"];


**Vector:** The Load vector statement defined the load vector. Pressure loads can only be normal to the surface, and thus this vector must be of a size :math:`1 \times 1`. 

.. code-block:: matlab

	Input.Load_[n].Vector = [LoadVector];


**Surface:** This defines to which surface this load or in this case pressure is associated to. The value is to be numeric in the form of :math:`1 \times 1`.

.. code-block:: matlab

	Input.Load_[n].Surface = [SurfaceNumber];

.. code-block:: matlab
  :caption: Example for a Pressure Load

  % Name
  Input.Load_1.Name = 'Pressure-Load';
  % Type
  Input.Load_1.Type = ["Pressure" "SurfaceLoad"];
  % Load Vector
  Input.Load_1.Vector = -0.5;
  % Surface
  Input.Load_1.Surface = 5;

.. -------------------------------------------------------------------------------------------------------------------------------
Initial
-------

**Initial:**
The initial Step signifies the initial condition or the initial step. So far only the :ref:`Boundary Conditions` have been programmed. There is the possibility to program some other condition if needed. 

**BC:** Defined the associated Boundary Condition. It has to be specified as a positive initeger and in the format :math:`1 \times n`.

.. code-block:: matlab

  Input.Initial.BC = [BoundaryCondition];


.. note::
  This definition is only needed once and will filther though all steps as it would when defining the initial conditions in ABAQUS. See ABAQUS manual for futrther information.
.. -------------------------------------------------------------------------------------------------------------------------------
Steps
-----

- Static

So far only the above mentioned Steps have been programmed. 

Global Syntax
+++++++++++++

**Name:** This is the name that will be shown in the ABAQUS .inp file

.. code-block:: matlab

	Input.Step_[n].Name = "[Name]";


**Type:** This denotes the step type. The abaliable Types are listed below.

- Static

.. code-block:: matlab

  Input.Step_[n].Type = "[Type]";


**Period:** This defines the step time as outlined here. As a standard it is set to :math:`1` but it can be set to anything else as long as it complies with the ABAQUS syntax. This prints the time directly form the struct to the ABAQUS input file. for more information see `ABAQUS General analysis procedure <https://classes.engineering.wustl.edu/2009/spring/mase5513/abaqus/docs/v6.6/books/gsa/default.htm?startat=ch11s01.html>`_.

.. code-block:: matlab

  Input.Step_[n].Period = 1;


Optional Syntax
+++++++++++++++

**Load:** This donates the Loads associated with this step. The values need to be positive integers and in the following format :math:`1 \times n`.

.. code-block:: matlab

  Input.Step_[n].Load = [LoadNo];




.. code-block:: matlab
  :caption: Example Step
  
  % Name
  Input.Step_1.Name = 'Pressure';
  % Type
  Input.Step_1.Type = 'Static';
  % Load
  Input.Step_1.Load = 1;
  % Time Period
  Input.Step_1.Period = 1;
  % Increment Size [Initial,Miniumum,Maximum]
  Input.Step_1.Increment = [1e-1 1e-12 1];
  % NLGEOM
  Input.Step_1.NLGEOM = 'YES';
  % Associated Boundry Condition
  Input.Step_1.BC = [2 3 4 5 6 7 8];
  % Prompts history output (number denotes the number of histories)
  Input.Step_1.H = 1;
  % History Name
  Input.Step_1.History_1.Node.Name = "RF";
  % Output History. Define the number
  Input.Step_1.History_1.Node.Set = set_no;
  % Node output values
  Input.Step_1.History_1.Node.Output = "U3";
  % Values to be printed
  Input.Step_1.History_1.Node.Print.Output = Input.Step_1.History_1.Node.Output;
  % Print Set
  Input.Step_1.History_1.Node.Print.Set = Input.Step_1.History_1.Node.Set;
  % Print frequency
  Input.Step_2.History_1.Node.Print.frequency = 1;


.. -------------------------------------------------------------------------------------------------------------------------------