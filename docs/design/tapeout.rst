**************************
Chip Tapeout Design Flow
**************************

The contents here describe recommended procedures/requirements/guidelines that should be followed for taping out a chip.

Major Milestones
================

The major milestones for each project may differ slightly based on the goals of the tapeout, but the following list should be a basis to start from.

Pass Architecture Design Review
--------------------------------

* Choose architecture to address design needs/goals
* Provide high-level system simulation results to indicate goals are achievable
* Generate system and block-level specifications
* Document architecture, assumptions, and requirements
  
Pass Schematic/RTL Design Review
---------------------------------

This should happen for each block in the system. This milestone will not have been reached until every block (or an encapsulating block) in the chip passes a design review.

* Generate schematics or RTL to implement block-level specifications
* Simulation results show required performance or functionality is being met
* Document design including decisions, assumptions and key results

Pass Post Layout Design Review
------------------------------

All requirements for the the Schematic/RTL review should be repeated for the layout design.  Additionally, the following requirements should be met.

* Generate LVS/DRC clean layout
* Review of any special layout requirements (metalization, matching, power, signal access)
* Confirmation that the design meets timing and edge rate requirements

Pass Top Level Verification
----------------------------

Once the entire chip design has been constructed (even if only as models), a suite of regression tests should begin being developed. These tests should continually be revised/improved and run as the design progresses. 

Design Reviews
==============

The guideline for things to review for each block can be found in the digital (:ref:`Digital_Design_Review`) and analog (:ref:`Analog_Design_Review`) workflows.


Final Tapeout Checklist
========================

Clocks
------

* Manual/visual inspection of clock networks on synthesized clocks
* Are clocks shielded from/against other sensitive lines?
* Check to make sure that all supplies for any clock buffers are present when you need a clock --> simulate power up and check that clocks are present.
* Check fan-out of any custom clock networks

Resets
------

* Check loading/timing/dependencies of all asynchronous resets

Interfaces
----------

* Have all interfaces between blocks & outside of the chip been simulated?
* At the chip level, there should at least be behavioral level simulations of all interfaces
* Check timing between custom and synthesized blocks
* Check the supply voltages at each interface. If they have different supply voltages, make sure there are appropriate level shifting circuits.

Power Supplies
--------------

* Simulate ramping of power supplies

    - Check reset timings
    - Check for leakage currents (floating inputs)
    - Check for clocks
    - Check for latch-up

* Run DC simulation of entire chip

    - Check bias points, leakage currents
      
* Check the amount and location of decap on each supply rail

Layout
------

* Check DRC

    - All required DRC rules should pass
    - Any waived DRC rules should be marked in a DRC waiver list and noted as to why the DRC rule is waived (`example <https://docs.google.com/spreadsheets/d/14WR3C4LZAJy3kOAlpGJr50J7Sr2TJML3L8LNtrF5k0o/edit#gid=0>`_)

* Check LVS

    - Pin labels at the top should be on pads/bumps
    - LVS should pass with NO virtual connections
    - All components that you care about should be LVS'd (e.g. if you have an inductor, or some other custom device, you should LVS it!)
    - Look for any floating guard rings (sealring is okay)

* Check Metal Integrity

    - Check that any high current nets (power, ground, high power inputs/outputs) have maximally connected metal.  You can use the :ref:`Via_Finder` and :ref:`Via_Insertion` tool for this purpose.
    - Check the resistivity of any critical nets that must carry high current using the `resistance visualization tool <http://docs.lionsemi.local/docs/parasitic-r-visualization/en/latest/parasitic_R/usage.html>`_.
      
I/O and ESD
-----------

* Are all outputs sized to drive the anticipated board/output load?
* Are all inputs designed to handle the expected voltage ranges?
* Do all inputs have some kind of weak pull-up/down to insure their state?
* Is there an ESD path for all I/Os?  (See :ref:`ESD_Guidelines`)


Final Tapeout Procedure
========================

After checking all of the pre-tapeout checklist items we are ready to send the final GDS to the foundry.

1. Stream out the layout design to GDS.  If there are additional non-silicon layers (e.g. RDL), make sure to alter the layermap file to remove these layers (since the gds layers that they map to may collide with internal foundry layers).
2. Stream in the output GDS into a new library and DO NOT include any reference libraries during the stream in.
3. Run LVS using the streamed-in library layout and the schematic we had for the top level chip.
4. Run DRC on the streamed-in library layout. The errors should match, or be similar (depending on the type of errors).
5. Tab the revision control database 
6. Freeze/disable access to the revision control database
7. Freeze/check-in the versions of any dependencies (cad/ versions, tools)
8. Tar and zip the gds database
9. Run a checksum on the \*.tar.gz file
10. Send the database to the foundry
11. Add Info to project module about tool versions used during tapeout
    
   


      
