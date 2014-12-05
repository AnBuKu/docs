debops.console
##############



This role manages console-related things, like enabling serial console,
setting up ``/etc/motd`` and ``/etc/issue`` files, configuring system-wide
locale settings. You can also provide a list of packages to install.

.. contents:: Table of Contents
   :local:
   :depth: 2
   :backlinks: top

Installation
~~~~~~~~~~~~

This role requires at least Ansible ``v1.7.0``. To install it, run::

    ansible-galaxy install debops.console




Role variables
~~~~~~~~~~~~~~

List of default variables available in the inventory::

    ---
    
    # Custom string added to /etc/issue
    console_issue: 'Organization Name'
    
    # Enable or disable serial console (allows you to use 'lxc-console',
    # 'virsh console' and other similar commands)
    console_serial: False
    
    # Serial console port
    console_serial_port: 'ttyS0'
    
    # Serial console baud rate
    console_serial_baud: '115200'
    
    # Serial console TERM string used to define terminal capabilities
    console_serial_term: 'xterm'
    
    # String used to enable serial console in sysvinit /etc/inittab
    console_serial_inittab: 'S0:2345:respawn:/sbin/getty -L {{ console_serial_port }} {{ console_serial_baud }} {{ console_serial_term }}'
    
    # Contents of /etc/motd
    console_motd: |
        -------------------------------------------------
                This system is managed by Ansible
        -------------------------------------------------
    
    # List of required console packages
    console_base_packages: [ 'locales' ]
    
    # List of additional packages to install
    console_packages: [ 'ncurses-term', 'vim', 'mc', 'ranger', 'tmux', 'zsh', 'htop',
                        'less', 'file', 'psmisc', 'iftop', 'nload', 'lsof' ]
    
    # Enable or disable FSCKFIX option in /etc/default/rcS
    # This option controls the behaviour of fsck during boot time, if it's enabled,
    # fsck will automatically repair filesystems without stopping the boot
    # process.
    # Choices: yes, no. Set to False to disable this feature.
    console_fsckfix: 'yes'
    
    # List of locales to enable on the host
    console_locales: [ 'en_US.UTF-8' ]




Authors and license
~~~~~~~~~~~~~~~~~~~

``debops.console`` role was written by:

- Maciej Delmanowski | `e-mail <mailto:drybjed@gmail.com>`__ | `Twitter <https://twitter.com/drybjed>`__ | `GitHub <https://github.com/drybjed>`__

License: `GPLv3 <https://tldrlegal.com/license/gnu-general-public-license-v3-%28gpl-3%29>`_

