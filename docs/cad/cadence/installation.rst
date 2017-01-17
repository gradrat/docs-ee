Installation
=============

Getting source files
--------------------

* Download the \*.sdp download configuration files and the Installscape tool which downloads the actual source files from Cadence
* Unpack the Installscape tool along with all of the \*.sdp files in one directory
* Before using Installscape, you need to install the appropriate library support (it is/was a 32-bit tool):

::

    >> yum install glibc.i686 libXext.i686 libXtst.i686

*  glibc.i686 - ld-linux.so.2
*  libXext.i686 - libXext.so.6
*  libXtst.i686 - libXtst.so.6

License Installation
--------------------

* Install the cadence license tools (LCU or under the tools/bin directory of most packages)
* Install the necessary libraries to run the licensing tools:

::

    >> yum install libgcc.i686 redhat-lsb-core.i686 

* If you want, use the "config\_lic" tool under the /share/license directory or else just edit the license file so that it has a line like so:

::

    SERVER <servername> <Ethernet MAC Address> <port>

where, it's MAC address and port to access the licenses at are filled in appropriately.

* Start the license server:

::

    >> lmgrd -c <license file>

* Set either CDS\_LIC\_FILE or LM\_LICENSE\_FILE environment variables to point to @ to gain access to the tools.

Automatically starting the license server on boot
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can also have the license server run automatically by editing the rc.lic (might be rclic.sample) file and having it executed by the /etc/rc.d/rc.local script.

So for example, the rc.lic file would look like (this can be anywhere you choose):

::

    #!bash

    #!/bin/sh
    # Start Cadence license daemons and specify the debug log file
    #
    # If editing this file manually, be sure to set values for INSTALL_DIR,
    # LICENSE_FILE, and optionally for LOG_FILE and LMGRD_OPTS
    #
    INSTALL_DIR='<Cadence tool root directory where you have INSTALL_DIR/tools/bin/lmgrd>'
    LICENSE_FILE='<path to license file>'
    LOG_FILE='<path to log file>'
    LMGRD_OPTS='<license manager options>'
    LOG_DIR='<path to log file>'

    echo
    echo "Starting Cadence license daemons"
    echo

    if [ -r ${INSTALL_DIR}/tools/bin/lmgrd ]; then
      rm -f /usr/tmp/lockcdslmd

      if [ -r ${LOG_FILE} ]; then
        time_stamp=`ls -ol ${LOG_FILE} | awk '{printf "%s.%d.%s\n",$5,$6,$7}'`
        mv ${LOG_FILE} ${LOG_FILE}.$time_stamp
        (echo "     Old debug log files in ${LOG_DIR}:")
        ( ls -l ${LOG_FILE}.* )
      fi

      # so boot can be run in the background and log file can be moved
      # while daemons are running

      ( ${INSTALL_DIR}/tools/bin/lmgrd ${LMGRD_OPTS} -c ${LICENSE_FILE} \
        2>&1 ) | ksh -c "while read line; do echo \"\$line\" >> ${LOG_FILE}; done" &
      sleep 2
    else
      echo ""
      echo "Cannot locate the license manager daemon (lmgrd)."
      echo "Please verify that the necessary symbolic link exists before proceeding."
      echo "For more information about licensing utilities, see the 'Cadence"
      echo "License Manager' in CDSDoc."
      echo ""
    fi
    exit 0

Then the contents of your /etc/rc.d/rc.local might look like:

::

    #!bash

    #!/bin/sh
    # This script will be executed *after* all the other init scripts

    touch /var/lock/subsys/local
    sh ./<path to rc.lic file>


Specific Tool Dependencies
---------------------------

For each of the following tools, you'll first need to install the following packages:

Virtuoso (IC)
~~~~~~~~~~~~~

::

    >> yum install libXp xorg-x11-fonts*dpi.noarch


ADE
~~~

::

    >> yum install compat-readline5 libgcc.i686

Encounter Digital Implementation (EDI)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

    >> yum install ksh

PVS / QRC Extraction
~~~~~~~~~~~~~~~~~~~~

::

    >> yum install compat-libtermcap 

For QRC tech generation:

::

    >> yum install libXft.i686 libXmu.i686 

Other Cadence tools relying on 32-bit packages
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You may have to install these packages too depending on your tool

::

    >> yum install elfutils elfutils-libelf libXp
    >> yum install compat-libstdc++-296
    >> yum install libXext.so.6
    >> yum install libXt.i686 libXt.x86_64
    >> yum install gcc-c++
    >> yum install libXp.so.6
    >> yum install libXtst.so.6
    >> yum install glibc-devel.i686
    >> yum install libXcursor.so.1
    >> yum install openmotif
    >> yum install libelf.so.1
    >> yum install libXss.so.1
    >> yum install libXft.so.2
    >> yum install libGL.so.1
    >> yum install libGLU.so.1
    >> yum install libXrandr.so.2
    >> yum install libXi.i686 ,libSM.i686, elfutils.libefl.i686, libXrender.i686, zlib.i686

Troubleshooting
---------------

ADE-XL hangs or won't start
~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you see that ADE-XL doesn't run (but ADE-L does), you might encounter
the (ADE-XL Message 1921) error. If so, it's likely that you didn't
install all of the required packages for your OS. To see what you are
missing,

1 Go to your run directory, which should look something like:
/.tmp\_/.cmddir0/

2 Look at the **runICRP** file while the simulation is trying to run.
There should be a shell command in that file to the tune of 'virtuoso '.
Copy or record that command somewhere.

3 When the simulator finally stops, that file might be gone, but run the
recorded command in your shell. This should produce some error log to
the screen. Follow the path of the error message (maybe something to do
with VNC) to see what libraries are missing

References
----------

-  http://alexcooper.co.uk/blog/2013/05/installing-cadence-tools-2012-2013/
