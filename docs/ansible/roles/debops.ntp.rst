debops.ntp
##########



``debops.ntp`` role can be used to install `OpenNTPD`_ time server which
will be used both to synchronize time on a host and serve accurate time
information for other hosts (if enabled). This role can also manage
timezone settings using ``tzdata`` Debian package.

Time server will not be installed in an LXC or OpenVZ container, because
time synchronization in this case is provided by the host operating system.

.. _OpenNTPD: http://www.openntpd.org/

.. contents:: Table of Contents
   :local:
   :depth: 2
   :backlinks: top

Installation
~~~~~~~~~~~~

This role requires at least Ansible ``v1.7.0``. To install it, run::

    ansible-galaxy install debops.ntp


Role dependencies
~~~~~~~~~~~~~~~~~

- ``debops.ferm``


Role variables
~~~~~~~~~~~~~~

List of default variables available in the inventory::

    ---
    
    # Which clock management daemon should be installed? Currently only 'openntpd'
    # is supported. Set to False to disable clock management
    ntp: 'openntpd'
    
    # Timezone configuration
    # This is a two element list:
    #   - first element is the "Area" (Europe, Asia, America, Etc, ...)
    #   - second element is the "Zone" (Warsaw, Tokyo, Chicago, UTC, ...)
    # Run 'dpkg-reconfigure tzdata' to see list of possible values
    # If this variable is empty or False, timezone won't be changed
    ntp_timezone: []
    
    # List of interfaces ntpd should listen on. Specify '*' to listen on all
    # interfaces. If this list is not empty, ntp port will be opened in firewall
    ntp_listen: []
    
    # List of hosts/networks in CIDR format to allow access to ntp port in
    # firewall. If this list is empty, access will be allowed from anywhere. You
    # should probably define a list of networks allowed access to mitigate NTP
    # amplification attacks
    ntp_allow: []
    
    # List of NTP servers to synchronize with
    ntp_servers: [ '0.debian.pool.ntp.org',
                   '1.debian.pool.ntp.org',
                   '2.debian.pool.ntp.org',
                   '3.debian.pool.ntp.org' ]




Authors and license
~~~~~~~~~~~~~~~~~~~

``debops.ntp`` role was written by:

- Maciej Delmanowski | `e-mail <mailto:drybjed@gmail.com>`__ | `Twitter <https://twitter.com/drybjed>`__ | `GitHub <https://github.com/drybjed>`__

License: `GPLv3 <https://tldrlegal.com/license/gnu-general-public-license-v3-%28gpl-3%29>`_

