debops.lxc
##########



``debops.lxc`` role can be used to configure and manage LXC environment on
a host. Specifically, LXC 1.0 support will be installed on Ubuntu Trusty
and Debian Jessie, for Debian Wheezy, LXC 1.0 package will be backported
from Jessie using ``debops.backporter`` role and Linux kernel from
``wheezy-backports`` will be installed for required kernel features (this
will require a reboot of the host).

You can use ``debops.lxc`` role to create and manage Linux Containers with
different configurations. These containers can be used on an external
interface (DHCP server is recommended) or on an internal NAT interface
(network will be configured by a specific play in DebOps, using
``debops.dnsmasq`` and ``debops.nat`` roles).

.. contents:: Table of Contents
   :local:
   :depth: 2
   :backlinks: top

Installation
~~~~~~~~~~~~

This role requires at least Ansible ``v1.7.0``. To install it, run::

    ansible-galaxy install debops.lxc


Role dependencies
~~~~~~~~~~~~~~~~~

- ``debops.ferm``
- ``debops.secret``
- ``debops.backporter``


Role variables
~~~~~~~~~~~~~~

List of default variables available in the inventory::

    ---
    
    # ---- Install Linux kernel from Debian Backports on Wheezy ----
    
    # Name of Linux kernel package to install from Debian Backports on Debian
    # Wheezy. If you use different architecture than amd64, you will need to change
    # the package name
    lxc_kernel_package: 'linux-image-amd64'
    
    # Address of recipient of a mail message with information about updated
    # kernel and reboot requirement
    lxc_kernel_mail_to: [ 'root@{{ ansible_domain }}' ]
    
    
    # ---- Configure LXC environment ----
    
    # Path where containers and their configuration is stored
    lxc_root_path: '/var/lib/lxc'
    
    # Configuration of default network interface to use for "normal" containers
    # (in DebOps, it's configured by default in 'interfaces' role)
    # This is the external interface in two-interface setup
    lxc_interface_external: 'br0'
    
    # Configuration of default network interface to use for "normal" containers
    # (in DebOps, it's configured by default in 'interfaces' role)
    # This is the internal interface in two-interface setup
    lxc_interface_internal: 'br1'
    
    # Configuration of default network interface to use for "NATted" containers
    # (in DebOps, it's configured in LXC playbook by 'nat' role)
    lxc_interface_nat: 'br2'
    
    # Which configuration from list below will be included in
    # /etc/lxc/default.conf?
    lxc_configuration_default: 'external'
    
    # List of configuration files and their contents in /etc/lxc/ directory.
    # Files will be named in the form: /etc/lxc/<key>.conf
    # If you are modifying ths map, make sure that 'lxc' key is present!
    lxc_configuration_map:
    
      # Global LXC configuration
      lxc: |
        lxc.lxcpath = {{ lxc_root_path }}
    
      # Original contents of /etc/lxc/default.conf
      empty: |
        lxc.network.type = empty
    
      # Simple configuration for "normal" containers with one network interface.
      # Default in DebOps
      external: |
        lxc.network.type = veth
        lxc.network.link = {{ lxc_interface_external }}
        lxc.network.flags = up
        lxc.start.auto = 1
    
      # Configuration with two network interfaces, not tested at the moment
      external_internal: |
        lxc.network.type = veth
        lxc.network.link = {{ lxc_interface_external }}
        lxc.network.flags = up
        lxc.network.type = veth
        lxc.network.link = {{ lxc_interface_internal }}
        lxc.network.flags = up
        lxc.start.auto = 1
    
      # Configuration for a container behind NAT
      nat: |
        lxc.network.type = veth
        lxc.network.link = {{ lxc_interface_nat }}
        lxc.network.flags = up
        lxc.start.auto = 1
    
    
    # ---- Configure custom templates ----
    
    # Length of generated root password
    lxc_template_root_password_length: '20'
    
    # Definition of root password (by default it will be randomly generated and
    # stored in secrets)
    lxc_template_root_password: '{{ lookup("password", secret + "/credentials/" + ansible_fqdn + "/lxc/container/root/password chars=ascii,numbers,digits,hexdigits length=" + lxc_template_root_password_length) }}'
    
    # SSH public key to put in root account of new container
    lxc_template_root_authorized_key: '{{ lookup("pipe", "ssh-add -L") }}'
    
    # Name of administrator account to create (by default, your username)
    lxc_template_admin_account: '{{ lookup("env","USER") }}'
    
    # SSH public key to put in administrator account of new container
    lxc_template_admin_authorized_key: '{{ lookup("pipe", "ssh-add -L") }}'
    
    # Address of Debian mirror to use in debootstrap
    # Example usage with local apt-cacher-ng proxy: 'http://cache.{{ ansible_domain }}:3142/debian'
    lxc_template_debootstrap_mirror: 'http://cdn.debian.net/debian'
    
    # Automatically add 'security.debian.org' repository and perform 'apt-get
    # upgrade' on container creation to get latest security updates. Container
    # creation takes longer, but resulting system is more secure.
    lxc_template_security_upgrade: True
    
    # List of packages downloaded and installed by debootstrap
    lxc_template_debootstrap_packages: [ 'ifupdown', 'locales', 'libui-dialog-perl', 'dialog',
                                         'isc-dhcp-client', 'netbase', 'net-tools', 'iproute',
                                         'openssh-server', 'sudo', 'lsb-release', 'python',
                                         'python-apt', 'python-pycurl', 'make', 'git',
                                         'ncurses-term', 'iputils-ping', 'debian-archive-keyring',
                                         'apt-transport-https', 'vim-tiny', 'cron', 'curl',
                                         'openssl', 'ca-certificates' ]
    
    
    # ---- Manage LXC containers ----
    
    # Default template used by lxc-create, from /usr/share/lxc/templates/
    lxc_default_template: 'debops'
    
    # LXC containers managed by Ansible are defined in a list below. Each entry is
    # a hash with keys as container parameters. Container configuration parameters
    # are interpreted only on initial container creation and are not updated
    # automatically afterwards.
    
    # List of required parameters:
    #   - name: ''               container name, will be used as subdomain
    #                            in dnsmasq NAT configuration.
    
    # List of optional parameters:
    #   - state: ''              defines what state should that container be in on
    #                            next Ansible run. Recognized states:
    #                              - started    (container should be running)
    #                              - stopped    (container should be stopped)
    #                              - absent     (container will be destroyed)
    #                            Without this parameter container will be created,
    #                            but not started automatically.
    #   - config: True or ''     enables usage of custom configuration instead of
    #                            default from /etc/lxc/default.conf
    #                            If True, container will be created with configuration
    #                            generated by Ansible from /tmp/lxc_temp_*.conf
    #                            Otherwise specify absolute path to a configuration
    #                            file to use (for example '/etc/lxc/nat.conf').
    #   - template: ''           template from /usr/share/lxc/templates/ to use for
    #                            this container, instead of the default.
    #   - template_options: ''   string of freeform options added at the end of
    #                            lxc-create command, after "--".
    #   - storage: ''            string of freeform storage options added to lxc-create
    #                            command after -B (for example: 'lvm --fssize 10G')
    #                            See 'man lxc-create' for available options.
    #   - network: ''            if 'config' option is not set, 'network' value becomes
    #                            a "shortcut" to select specific config file from /etc/lxc/*
    #                            (for example, you can specify 'network: "nat"' and host
    #                            will be configured with config file from /etc/lxc/nat.conf).
    
    # List of parameters recognized with 'config: True' (generated configuration):
    #   - network: ''            currently you can specify 'external' or 'nat'
    #                            to connect default network interface of a container
    #                            to specified network interface of a host.
    #   - hwaddr: ''             if 'network' option is set, you can specify Ethernet
    #                            address of container network interface.
    #   - auto: True/False       by default containers are configured to start
    #                            automatically at boot; using this option you can
    #                            disable autostart of a container.
    #   - options: |             text block, will be added at the end of the configuration
    #                            file.
    
    # List of LXC containers managed by Ansible.
    lxc_containers: []
    
      # Simple container, not started by default, autostart on boot enabled
      #- name: 'example-container'
    
      # Simple container started automatically
      #- name: 'container'
      #  state: 'started'
    
      # Debian container, started automatically, network behind NAT
      #- name: 'natted-container'
      #  config: True
      #  network: 'nat'
      #  state: 'started'
      #  template: 'debian'




Authors and license
~~~~~~~~~~~~~~~~~~~

``debops.lxc`` role was written by:

- Maciej Delmanowski | `e-mail <mailto:drybjed@gmail.com>`__ | `Twitter <https://twitter.com/drybjed>`__ | `GitHub <https://github.com/drybjed>`__

License: `GPLv3 <https://tldrlegal.com/license/gnu-general-public-license-v3-%28gpl-3%29>`_

