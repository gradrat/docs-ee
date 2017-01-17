.. _cds_qrc_usage:

QRC Usage
==========

Techlib Setup
-------------

The output of the :ref:`cds_qrc_techgen` process is a technology directory(ies) that corresponds to a particular process corner/option.  In order to use the extraction results, this technology directory should appear as rule or corner when running parasitic extraction (PVS->Quantus QRC). To do this, you need to provide a path to that directory by defining a corner.defs file in the tech library path which might look like:

::

    DEFINE TYP <PATH TO typical corner QRC run directory>
    DEFINE SLOW <PATH TO slow corner QRC run directory>
    DEFINE FAST <PATH TO fast corner QRC run directory>


.. note:: This corner.defs file must be placed inside of a library that is defined in Cadence (via cds.lib and/or pvtech.lib) by whatever file you specify as the Quantus QRC tech lib in the run menu.


