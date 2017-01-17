.. _cds_virtuoso_layout:

*************
Layout Usage
*************

Layout XL Usage
===============

Layout Tips
===========

Marking Nets
~~~~~~~~~~~~

* To change how Cadence marks connectivity on nets, you can choose Connectivity-->Nets-->Mark and then hit the F3 button for options
* To preset certain options for yourself, you can modify your .cdsenv file. For example, to choose which layers you want excluded in the connectivity you could add:

::

    layout markNetExcludePurposes boolean t
    layout _markNetExcludePurposesName string "boundary annotate error label flight warning"

* You can also save the mark net options from the options form that appears when you hit F3. If you save this to the tech library, then those options become the default for all libraries that use that tech lib. The file should be saved with the name *markNetOptions* and placed in this path:

::

    <tech library directory>/.cadence/dfII/markNet/markNetOptions

This will be the default file if there isn't a similar path in the current directory (where virtuoso was launched) or in the user's home directory.

Creating a new multi-part path
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Multipart paths can be useful for things like creating guard rings or seal rings or any connected structure that requires relative spacings/sizings.

1. Open a layout view
2. Go to Create->Multipart Path
3. Hit the 'F3' key to bring up the Multipart Path options menu
4. Choose a template name to create or edit
5. Click the Subpart... options button to create the multipart path
   definition

Creating pins from labels
~~~~~~~~~~~~~~~~~~~~~~~~~

Depending on how you normally define pin/net connectivity, you sometimes may want to create pin database objects from text labels so that the layout can be better used in subsequent layouts in tools such as Layout-XL.

* If you are using labels (on layers M1TXT, M2TXT, etc.), go to the **Tools** menu and select **Create Pins from Labels...**.
* A window will pop up with options to put pins on locations of labels.
* The default settings should be: 
    * Objects Within: Cellview
    * Object Type: Both
    * Objects: All

* If you just have M3TXT labels, then make sure to check the **ignore** checkbox on the right of all the other ones. If you have M2TXT, then make sure to not ignore those, etc.
* The column with **Objects Layer** is where it is looking for the labels. The **Pin Layer** is where it will add a rectangular *pin*. 
* For something like M3TXT, you should change the **Pin Layer** to **M3**, etc.
* After pressing **OK**, there will be little metal rectangles at every label. You may have to go to each one to make sure they are all at the appropriate places (and not producing DRC errors).

Creating a P&R Boundary
~~~~~~~~~~~~~~~~~~~~~~~

Sometimes you will want to do this if the layout is intended to be used in an automated place & route tool or perhaps for estimating a design size in Layout-XL.

1. Go to the **Create** menu and select **P&R Objects** and then **P&R Boundary**.
2. Point to the two opposite corners of the boundary of your cell and then adjust the shape by chopping, etc, if needed.

Shortcuts
=========

Ctrl+d
    Unselect all

z
    Zoom in on area

Ctrl+z
    Zoom out

Shift+z
    Zoom in
    
m
    Move selection

c
    Copy selection

s
    Stretch selection

C
    Chop selection

p
    Create a path in the current layer

r
    Create a rectangle shape in the current layer


