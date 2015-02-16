debops.sshd
###########



This role configures OpenSSH server for public key access, disables
password authentication and creates a specific configuration options for
``sftponly`` accounts.

.. contents:: Table of Contents
   :local:
   :depth: 2
   :backlinks: top

Installation
~~~~~~~~~~~~

This role requires at least Ansible ``v1.7.0``. To install it, run::

    ansible-galaxy install debops.sshd


Role dependencies
~~~~~~~~~~~~~~~~~

- ``debops.ferm``
- ``debops.sshkeys``
- ``debops.auth``
- ``debops.tcpwrappers``


Usage tips
~~~~~~~~~~

By default, sshd creates a firewall rule to limit access by ssh.
If you believe you should be able to access a server and you can ping it but not ssh, connect to the machine by other means and check if you are blocked. you can do this with the command ``cat /proc/net/xt_recent/badguys``.
You can get there if you try too many connection attempts at a short time. You can also check the ``dmesg`` log file. Unsuccessful logging attempts are logged there.



Role variables
~~~~~~~~~~~~~~

List of default variables available in the inventory::

    ---
    
    # List of IP addresses or CIDR networks to allow access to SSH without
    # restrictions.
    # They will be configured in iptables (via ferm) and /etc/hosts.allow (via
    # tcpwrappers).
    sshd_allow: []
    sshd_group_allow: []
    sshd_host_allow: []
    
    # By default SSH access from unknown IP addresses is limited and filtered by
    # iptables, you can disable this by changing variable below to 'true'. You will
    # have to enable access in iptables and tcpwrappers from any IP address
    # separately.
    sshd_unlimited_access: 'false'
    
    sshd_Port: 22
    sshd_PermitRootLogin: 'without-password'
    sshd_PasswordAuthentication: 'no'
    sshd_X11Forwarding: 'no'
    sshd_AllowGroups: 'root admins sshusers sftponly'
    
    sshd_authorized_keys: '/etc/ssh/authorized_keys'
    sshd_authorized_keys_monkeysphere: '/var/lib/monkeysphere/authorized_keys/%u'
    sshd_authorized_keys_global: '{{ sshd_authorized_keys }}/%u'
    sshd_authorized_keys_user: '%h/.ssh/authorized_keys %h/.ssh/authorized_keys2'
    
    sshd_known_hosts: []




Authors and license
~~~~~~~~~~~~~~~~~~~

``debops.sshd`` role was written by:

- Maciej Delmanowski | `e-mail <mailto:drybjed@gmail.com>`__ | `Twitter <https://twitter.com/drybjed>`__ | `GitHub <https://github.com/drybjed>`__

License: `GPLv3 <https://tldrlegal.com/license/gnu-general-public-license-v3-%28gpl-3%29>`_

