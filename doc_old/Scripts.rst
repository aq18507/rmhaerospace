Scripts
=======

This section documents individual scripts that either is necessary or useful in running GATORcell simulations. Each script is described in its most current version which is outlined in the Compatability section in this documentation. Any depreciated scripts are documented in the **Depreciated Script** section of this documentation.

runAbaqus4
----------

Description
+++++++++++

The ``runAbaqus4`` script is designed to run abaqus from ``Matlab`` over the command line using the ``.inp`` files and corresponding ``.mat`` files stored in the same directory. To run in its basic versions it does not require an input, i.e. the function only needs to be called.

**Working Principle**

#. It scans the directory for ``.inp`` and ``.mat`` files. Once it has generated a list it will submit all files to ABAQUS.
#. There is a gatekeeper function to prevent CPU and Memory overload. The parameter ``NumberOfModels = 6`` which means that it can run 6 models in parallel. It will do this on one single core, which provides the computer with two cores overhead.
#. Once all models are solved ``runAbaqus4`` will call the ``dataSort`` function to append the reults from the ``.dat`` files into the corresponding ``.mat`` file.

.. warning::
    There are known cases where this function fails to complete, this mostly happens when the ``dataSort`` function is called. This is caused by some files the are not properly closed by Abaqus (the causes are at the time of writing unknown). If this is the case open the Windows Task Manager (``CTRL + ALT + DEL``) and look for the process/es Name **SMAStaMain**. Right-click on the process and select ``End Process``. Repeat if there is more than one of those still running. If they cannot be found then they may be hidden under the **MatlabR20xxx** tab. Once this is done run ``dataSort`` from the command line, noting that you may need to run the preamble to index the scripts directory.


Optional Parameters
+++++++++++++++++++
#. ``NumberOfModels`` which limits the number of simulations to be run in parallel. Default == :math:`6`

Syntax
++++++

.. code-block:: matlab
    
    runAbaqus4;

findRunningPorcess
------------------

Description
+++++++++++

This function determines whether Abaqus ``standard.exe`` is still running. If it is a dialogue window is called up in Matlab notifying that it is still running. Click ``OK`` to proceed. The next window will present the option of either waiting until all models are finished or killing all Abaqus processes.

Syntax
++++++

.. code-block:: matlab

    findRunningProcess;

.. warning::

    Clicking ``OK`` will terminate all running ``standard.exe`` processes. This means that the model in question may not get completed.


dataSort
--------

Description
+++++++++++

The ``dataSort`` script takes all data from an analysis and sorts it into directories. This script does not require any inputs. It indexes all ``.dat`` files. It then appends the results into the corresponding ``.mat`` files with the structured array named ``Output``. This script is stored in the ``Script`` directory, which means that if it is run for the first time it will require the working directory to be indexed. This can either be done manually or by running the ``preamble;`` command, providing the file exists in the working directory.

Syntax
++++++

.. code-block:: matlab
    
    dataSort;

.. warning::
    If there is a results directory named ``Abaqus_NaN`` then this will be overwritten! Ensure that this is appropriately renamed.


dataRead5
---------

Description
+++++++++++

This function reads data from all ``.mat`` files in a directory and sorts them in a single array. Since the outputs that are required may vary quite significantly it is written in such an array that all data can be accessed with relative ease. To make a request an input structured array must be created; let's call it ``Request`` that contains a request variable that follows the convention ``r1``, ``r2``, ``r3``, etc. Each request consists of a string array which is structured as follows

.. code-block:: matlab

    Request.r[N] = ["[Function]" "[FileIdentifier]" "[Option]" "[StructuredArrayPath]" "[RequestedVariable]"]

**Function:** The following functions are so far available:

#. ``i`` denotes a unique **Identifier**, or identifiers acting as a unique fingerprint to associate outputs from different data sets to one single array entry. Important to note here is that they must be present in all data sets. For instance for a mesh density study where the variable that is changed is defined by a ``MeshSizeMax`` then this will be the variable to track. But likewise if there are for instance :math:`6` unique variables that would idnetify a models are changed, then they need to be identified as such. The data must be in the follwing format :math:`1 \times 1`

#. ``s`` denotes a **Single** output that is not an identifer. This may be some mesh data, or model data. It has to be noted that this data set will not be conditioned and must be of following format :math:`1 \times 1`. 

#. ``a`` offers the ability to extract data from an array that in not in the format of :math:`1 \times 1`. That said the output has to be in the format :math:`1 \times 1` format. The location needs to be specified in the options file where to get the data from. If there is a :math:`4 \times 4` matrix and the data from row :math:`2` culumn :math:`3` then the following needs to be entered as an option ``"2" "3"``.

#. ``dP_max_U3`` this gets the maximum out-of-plain displacement in the :math:`z`-direction. The original data must be in the format :math:`1 \times n`.

#. ``EA_max`` computes the *EA* at the maximum extension, or at the last interwall using the reaction forces using the following equation, where the original data must be in the format :math:`1 \times n`.

.. math::

    \frac{FL}{d}

#. ``EI_max`` computes the *EA* at the maximum extension, or at the last interwall using the reaction forces using the following equation, where the original data must be in the format :math:`1 \times n`.

.. math::
     
    \frac{FL^3}{48d}

#. ``RF_max`` Computes the sum of all reaction forces at maximum extension. The original data must be in the format :math:`1 \times n`.

**FileIdentifier:** The file identifer categorises the model according to a common pattern in the file name. For instance in a file name ``EA_test_1.inp`` where ``EA_`` is the common pattern.

**RequestedVariable:** Defines the path to the data. For instance if it is stored in ``Input.CoreParameters.Mesh`` then each level needs to be specified as a string like the following ``"Input" "CoreParameters" "Mesh"``.

**RequestedVariable:** This is the variable that is requested. It must be in a string format. Note that in the output prompt it will save the variable name if the ``i``, ``s``, ``a`` or ``RF_max`` functions are used. For the ``EA_max`` and ``EI_max`` functions the actual functions are printed for clarity.

Input Examples
++++++++++++++

.. code-block:: matlab

    Request.r1  = ["i" "DP_" "Input" "CoreParameters" "t"];
    Request.r2  = ["i" "DP_" "Input" "CoreParameters" "theta"];
    Request.r3  = ["i" "DP_" "Input" "CoreParameters" "z"];
    Request.r4  = ["i" "DP_" "Input" "CoreParameters" "h"];
    Request.r5  = ["i" "DP_" "Input" "CoreParameters" "dE"];
    Request.r6  = ["i" "DP_" "Input" "CoreParameters" "ts"];
    Request.r7  = ["a" "DP_" "1" "1" "Output" "TotalCpuTime"];
    Request.r8  = ["a" "DP_" "2" "1" "Output" "TotalCpuTime"];
    Request.r9  = ["s" "EA_" "Input" "CoreParameters" "xmax"];
    Request.r10 = ["s" "EA_" "Input" "CoreParameters" "ymax"];
    Request.r11 = ["EA_max" "EA_" "Output" "Step_1" "History_1" "RF2"];
    Request.r12 = ["EI_max" "EI_" "Output" "Step_1" "History_1" "RF3"];
    Request.r13 = ["dP_max_U3" "DP_" "Output" "Step_2" "History_1" "U3"];

Syntax
++++++

.. code-block:: matlab

    [ModelData] = dataRead5(Path,Dir,Request,moveFiles)


licenseCounter
--------------

Description
+++++++++++

This function counts the number of ABAQUS licences used by the computer from which the command is executed. There are no inputs to this function, it will find the computer name and then run the ABAQUS command ``abaqus licensing dslsstat -usage`` to determine the number of licences. ABAQUS takes out 50 tokens per licence and the fair usage policy is at the time of writing 400 tokens, which would equate to 8 individual licences.

Syntax
++++++

.. code-block:: matlab

    License_num = licenseCounter


existingFiles
-------------

This function counts the number of files of a certain type present in the directory in which it is executed. It requires a string input with the desired file suffix, for example for a ``.mlx`` file the string ``mlx`` is required. Note that it will internally convert it to the following expression ``*.mlx``. The output is a cell-array containing a file name ``Files`` per cell and the number of files ``Size`` as a secondary output.

Syntax
++++++

.. code-block:: matlab

    [Files,Size] = existingFiles('FileType');


dirCombine
----------

This function combines the data of two directories and deletes the predefined directory. This function has two inputs and no output. The inputs have to be in string format. 

**dir_1** The first directory is the one that is the one the second directory is gopying all its data to.
**dir_2** The second directory is the one that is going to be deleted.

Syntax
++++++

.. code-block:: matlab

    dirCombine('dir_1','dir_2');


recreateInp
-----------

This function recreates an input file (``.inp``), providing there is a healthy corresponding ``.mat`` file present. Note that this script does not need any input. It needs to be run from the directory in which the corrupted ``.inp`` files and the corresponding ``.mat`` are located.

Syntax
++++++

.. code-block:: matlab

    recreateInp();