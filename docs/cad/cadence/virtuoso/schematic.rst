.. _cds_virtuoso_schematic:

****************
Schematic Usage
****************

Schematic Setup
===============

Customizing Sheet Info
-------------------------

When editing a schematic in Cadence you can (should) add in a built-in sheet element border to each schematic view:  

::

    Edit->Sheet Size...  

If you want to customize what is displayed, you can change the contents by editing (as the CAD/root user) the **Asize/Bsize** symbol views in the US_8ths library AND the **Title** symbol view.

Some possible labels to add include:

ilInst~>cellView~>cellName
    This provides the name of the cell that the sheet is instantiated in.

SosGetCurrentVersion(ilInst~>cellView)
    If you are using Cliosoft, this provides the revision/managed state of the cellview.

ilInst~>cellView~>fileTimeStamp
    This provides the last date/time that the schematic was edited & saved.

ilInst~>cellView~>createTime
    This provides the creation date for the schematic.

if(ilInst~>cellView~>modifiedButNotSaved then "True" else "False")
    Indicates whether the schematic has been modified but not saved.

dbGetPropByName(ilInst~>cellView "lastSchematicExtraction")~>value
    This returns the date that the schematic was last "checked".


Default Shortcuts
==================

m
    Move selection

c
    Copy selection

r
    Rotate selection

i
    Create an instance

p
    Create a pin

e
    Descend into a selected instance

Ctrl+e
    Return up one level of hierarchy

9
    Highlights a net


