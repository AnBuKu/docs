debops.gitusers
###############


``debops.gitusers`` role can be used to create and maintain user accounts
accessible over SSH with limited functionality provided by ``git-shell``
and a set of shell scripts. These accounts can provide users with limited
shell functionality and ability to create git repositories on the server,
which then can be used to "deploy" web content (HTML, CSS, PHP5 web pages,
support for other environments might be included in the future).

You can think of this role as a simple `Heroku`_ or `PagodaBox`_ clone.

.. _Heroku: https://www.heroku.com/
.. _PagodaBox: https://pagodabox.com/

.. contents:: Table of Contents
   :local:
   :depth: 2
   :backlinks: top

Installation
~~~~~~~~~~~~

This role requires at least Ansible ``v1.7.0``. To install it, run::

    ansible-galaxy install debops.gitusers


Role dependencies
~~~~~~~~~~~~~~~~~

- ``debops.auth``


Role variables
~~~~~~~~~~~~~~

List of default variables available in the inventory::

    ---
    
    # --- An example account entry, everything except 'name' is optional
    # List of all recognized values, default value listed first
    #
    #  - name: 'username'               # mandatory, default group if not defined
    #    state: 'present,absent'
    #    group: 'name'                  # default group
    #    groups: []                     # list of groups to set
    #    append: yes/no                 # add to, or set groups
    #    gid: ''
    #    uid: ''
    #    comment: 'GECOS entry'
    #    systemuser: False/True         # create system user
    #    systemgroup: False/True        # create system group
    #
    #    domain: '{{ ansible_domain }}' # for git users
    #
    #    # Create ~/.forward file (set to False to remove ~/.forward)
    #    forward: [ 'user@domain', 'account' ]
    #
    #    # Add or disable ssh authorized keys (set to False to remove ~/.ssh/authorized_keys
    #    sshkeys: [ 'list', 'of', 'keys' ]
    
    
    # --- Lists of different accounts to create/manage ---
    
    # "Global" users
    gitusers_list: []
    
    # "Host group" users
    gitusers_group_list: []
    
    # "Host" users
    gitusers_host_list: []
    
    
    # --- Global defaults ---
    
    # Add a suffix to an account name, for example '-git'
    gitusers_name_suffix: ""
    
    # Shell enforced on all git-shell accounts
    gitusers_default_shell: '/usr/bin/git-shell'
    
    # List of groups git-shell users belong to (git-shell requires SSH access)
    gitusers_default_groups_list: [ 'sshusers' ]
    
    # Should default groups be added to, or replace current user groups? Set to
    # 'no' to enforce your preferred list of groups
    gitusers_default_groups_append: 'yes'
    
    # Directory where git-shell user home will be created
    gitusers_default_home_prefix: '/srv/gitusers'
    
    # Unix permissions enforced on users home directories
    gitusers_default_home_mode: '0750'
    
    # Path to directory where websites are stored
    gitusers_default_www_prefix: '/srv/www'
    
    # System group which should be allowed access to website directory
    gitusers_default_www_group: 'www-data'
    
    # What domain should git users use for publishing websites
    gitusers_default_domain: '{{ ansible_domain }}'
    
    # List of scripts which are copied to user directories into
    # ~/git-shell-commands/ directory
    gitusers_default_commands: [ 'help', 'list', 'init', 'publish', 'userdir',
                                 'deploy', 'snapshot', 'clean', 'rename', 'drop' ]




Authors and license
~~~~~~~~~~~~~~~~~~~

``debops.gitusers`` role was written by:

- Maciej Delmanowski | `e-mail <mailto:drybjed@gmail.com>`__ | `Twitter <https://twitter.com/drybjed>`__ | `GitHub <https://github.com/drybjed>`__

License: `GPLv3 <https://tldrlegal.com/license/gnu-general-public-license-v3-%28gpl-3%29>`_

