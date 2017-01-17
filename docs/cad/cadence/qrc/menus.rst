.. _cds_qrc_menus:

QRC Menu Setup
================

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

