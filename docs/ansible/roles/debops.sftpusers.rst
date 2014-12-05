debops.sftpusers
################



``debops.sftpusers`` role can be used to create and manage user accounts
which have system access restricted to SFTP only.

.. contents:: Table of Contents
   :local:
   :depth: 2
   :backlinks: top

Installation
~~~~~~~~~~~~

This role requires at least Ansible ``v1.7.0``. To install it, run::

    ansible-galaxy install debops.sftpusers


Role dependencies
~~~~~~~~~~~~~~~~~

- ``debops.auth``


Role variables
~~~~~~~~~~~~~~

List of default variables available in the inventory::

    ---
    
    # ---- An example account entry ----
    
    # Everything except 'name', 'dirs' and 'sites' is optional
    # List of all recognized values, default value listed first
    #
    #  - name: 'username'               # mandatory, default group if not defined
    #
    #    # Required, can be empty. Create directories in user home directory
    #    dirs: [ 'files', 'dir/subdir', 'directory\ with\ spaces' ]
    #
    #    # Required, can be empty. Prepare site directories for websites and mount
    #    # them in the users' home directory in $HOME/sites/
    #    sites: [ 'example.com', 'other.example.com' ]
    #
    #    state: 'present,absent'
    #    group: 'name'                  # default group
    #    gid: ''
    #    uid: ''
    #    comment: 'GECOS entry'
    #    systemuser: False/True         # create system user
    #    systemgroup: False/True        # create system group
    #
    #    home: '/srv/sftpusers/name'
    #
    #    # Create ~/.forward file (set to False to remove ~/.forward)
    #    forward: [ 'user@domain', 'account' ]
    #
    
    
    # ---- Lists of different accounts to create/manage ----
    
    # "Global" users
    sftpusers_list: []
    
    # "Host group" users
    sftpusers_group_list: []
    
    # "Host" users
    sftpusers_host_list: []
    
    
    # ---- Global defaults ----
    
    # Add a suffix to an account name, for example: '-sftp'
    sftpusers_name_suffix: ""
    
    # System group that restricts account use to SFTPonly
    sftpusers_system_group: 'sftponly'
    
    # Shell enforced on all SFTPonly accounts
    sftpusers_default_shell: '/usr/sbin/nologin'
    
    # List of groups SFTPonly users belong in
    sftpusers_default_groups: [ '{{ sftpusers_system_group }}' ]
    
    # Path to directory where websites are stored
    sftpusers_default_www_prefix: '/srv/www'
    
    # System group which should be allowed access to website directory
    sftpusers_default_www_group: 'www-data'
    
    # Mount options used to mount website directories in user home directories
    # (sftponly users don't have access to directories outside of their home
    # directory (/srv/sftpusers/username). Because of that website directories
    # (/srv/www/username/sites/) will be bind mounted to their home directories
    # (/srv/sftpusers/username/sites/), thus allowing secure access)
    sftpusers_default_mount_options: 'bind'
    
    # Directory where SFTPonly user homes will be created
    sftpusers_default_home_prefix: '/srv/sftpusers'
    
    # UNIX permissions enforced on users home directories
    sftpusers_default_home_mode: '0750'
    
    # List of default directories created on SFTPonly accounts (users don't have
    # permission to access their home directory due to SFTPonly constraints, but they
    # can access subdirectories)
    sftpusers_default_dirs: [ 'files' ]



Detailed usage guide
~~~~~~~~~~~~~~~~~~~~

Access to SFTPonly accounts is allowed only using SSH public keys, but
users cannot manage their own keys. Instead, sshd server uses keys from
``/etc/ssh/authorized_keys/<user>`` for authorization. Use
``debops.sshkeys`` role to manage these keys separately.


Authors and license
~~~~~~~~~~~~~~~~~~~

``debops.sftpusers`` role was written by:

- Maciej Delmanowski | `e-mail <mailto:drybjed@gmail.com>`__ | `Twitter <https://twitter.com/drybjed>`__ | `GitHub <https://github.com/drybjed>`__

License: `GPLv3 <https://tldrlegal.com/license/gnu-general-public-license-v3-%28gpl-3%29>`_

