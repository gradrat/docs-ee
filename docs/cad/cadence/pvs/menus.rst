.. _cds_pvs_menus:

**********************************
Setting up PVS Menus (LVS/DRC)
**********************************

It's often desirable to have the options needed to run an LVS, DRC, or extraction (QRC) run to be automatically populated based on the project or technology. This helps to speed up DRC/LVS/Extraction checks as well as avoid problems where designers/users either can't find the appropriate rules or are using the incorrect set of rules.

Getting the PVS menu to appear in a Layout Menu
===============================================

Manually
--------

The PVS menu is not available by default in Cadence. To make it available, you should:

1. Open the layout view of a cell
2. Go to Launch->Plugins->PVS in the cell view's menu
3. Now a PVS menu item should be present in the layout cell view

   
Automatically
-------------

To get the PVS menu to appear by default when you open a layout view, you should add the following lines (or similar) to your .cdsinit file.

::

    printf( strcat("-- Registering PVS menu triggers... \n"))
    deRegUserTriggers("maskLayout" nil '_pvsMaskLayoutMenuTrigger)
    deRegUserTriggers("maskLayoutXL" nil '_pvsMaskLayoutMenuTrigger)


The first line, is just informational for users. The next two lines launch PVS whenever Layout, or LayoutXL tools are used.


Using DRC/LVS Preset Files
===========================

You can either manually fill out the LVS/DRC forms, or you can load the with a set of pre-defined values using preset files.

Creating a new preset file
--------------------------

To create/populate a DRC/LVS preset file:

1. Launch the DRC or LVS menu from the PVS menu PVS->Run DRC.. (or PVS->Run LVS..)
2. Fill out the Run directory, inputs, rule files, etc...
3. Save the settings for the run by choosing the File->Save Presets option from the DRC/LVS run menu

Manually Loading a preset file
-------------------------------

Once you have a presets file, you can simply:

1. Open the DRC or LVS run menu
2. Go to File->Load Presets

The tool is somewhat smart about replacing some of cell specific preset options with the current cell (e.g. input cell, output netlist/files, etc...).

Automatically loading a preset file
------------------------------------

To have a specific preset file loaded by default (e.g. if several users all want to use the same preset file), simply define the following environment variables:

::
    
    setenv PVSUI_LVS_PRESETS_FILE <path to lvs preset file>
    setenv PVSUI_DRC_PRESETS_FILE <path to drc preset file>


Customizing DRC/LVS Preset File Inputs
=======================================

The 'smart' part of a preset file allows a single preset file to be generically used for most cells.  However, there are times that you may want to customize the inputs/outputs based on the input cells (e.g. the run directory) or the type of check you are running.

The hierarchy of choosing how the LVS or DRC runs is populated is:

1. Presets file defined in a techRuleSet (highest priority)
2. PVSPreFormTrigger skill code (if a PVSPreFormTrigger procedure is defined)
3. Auto (or manually) loaded Preset file (lowest priority)
   
Using the PVSPreFormTrigger
---------------------------

We can define a PVSPreFormTrigger to customize certain components of the LVS/DRC menu.  An example of this procedure is provided below.

::

    procedure( PVSPreFormTrigger(runType presetFile winId)
        let(
            (projDir user
            pvsTechLibPath
            runDir runDirExt
            cv cellName libName
            tmpFile f
            )

            cv = winId~>cellView
            user = getShellEnvVar("USER")
            projDir = getShellEnvVar("PROJECT")

            pvsTechLibPath = strcat( projDir "/cad/cds.lib")

            runDirExt = case(runType
                    ("LVS" "lvs")
                    ("DRC" "drc")
                    ("FASTXOR" "fastxor")
                    ("XOR" "xor")
                    (t 'default)
                ); case

            cellName = cv~>cellName
            libName = cv~>libName
            runDir = strcat("/tmp_ssd/" user "/pvs/" libName "/" cellName "/" runDirExt)

            ; Write out the temporary presets file
            tmpFile = makeTempFileName( strcat(getTempDir() "/pvs"))
            f = outfile(tmpFile)
            fprintf(f "[ pvsgui.Run%s ]\n" runType)
            fprintf(f "RunDir \"%s\" \n" runDir )
            close(f)

            ; Return the name of the presets file
            tmpFile

        ); let
    ); procedure


This procedure re-defines the run directory based on the cell's name and library. If a preset file is loaded in conjunction with this procedure, the "smart" part of the tool automatically changes several other form inputs based on the run directory while keeping things like the rule file and tool options the same.

QRC Menu Setup
================

Setting up a QRC Techlib
--------------------------

The output of the :ref:`cds_qrc_techgen` process is a technology directory(ies) that corresponds to a particular process corner/option.  This technology directory should appear as rule or corner when running parasitic extraction (PVS->Quantus QRC). To do this, you need to provide a path to that directory by defining a corner.defs file in the tech library path which might look like:

::

    DEFINE TYP <PATH TO typical corner QRC run directory>
    DEFINE SLOW <PATH TO slow corner QRC run directory>
    DEFINE FAST <PATH TO fast corner QRC run directory>


.. note:: This corner.defs file must be placed inside of a library that is defined in Cadence (via cds.lib and/or pvtech.lib) by whatever file you specify as the Quantus QRC tech lib in the run menu.

Pre-loading the PVS->QRC menu
------------------------------

When we want to run extraction based on a PVS-LVS run, we can use the PVS->QRC flow by launching the tool in the layout tool menu: QRC->Run PVS-Quantus QRC...

This menu can be automatically populated by (re)defining the vuiUserDefinedRCXFormSetupCB skill procedure. An example of that procedure is shown below:


::

    procedure( vuiUserDefinedRCXFormSetupCB(form)
        let(
            (projDir user
            pvsTechLibPath pvsTechLibName
            lvsRunDir svdbQRCDir
            cv cellName libName
            )

            cv = geGetWindowRep()
            user = getShellEnvVar("USER")
            projDir = getShellEnvVar("PROJECT")

            pvsTechLibPath = ddGetUpdatedLib()
            pvsTechLibName = "QRC_LIB"

            cellName = cv~>cellName
            libName = cv~>libName
            lvsRunDir = strcat("/tmp_ssd/" user "/pvs/" libName "/" cellName "/lvs")
            svdbQRCDir = strcat( lvsRunDir "/svdb" )

            ; Setting of the preQRCform
             when( form->hiFormSym == 'vuipreRcxForm
                 form->runName->value = cellName
                 form->runDir->value = svdbQRCDir
                 form->assuraTech->value = pvsTechLibPath
                 form->tech->value = pvsTechLibName
            ); end of when preQRC

            ; need to return a true for the QRC run to continue
            t

        ); let
    ); procedure


In this example, the cds.lib used to define the libraries is provided as the lib path definition for the tool. It also defines the run directory in the same way as the DRC/LVS example above.

.. note:: By populating/defining the assuraTech value, we no longer need a pvtech.lib file in the Cadence search path (usually needs to be where you launched Cadence).

