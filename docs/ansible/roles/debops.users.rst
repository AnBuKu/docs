debops.users
############



This role can be used to manage user accounts (and user groups). You can
manage almost all aspects of the users' account, like UID/GID, home
directory, shell, etc. Accounts are configured using lists in Ansible
inventory, with separate lists for:

* admin accounts;
* global list of users created on each host in a cluster;
* list of users created on a group of hosts;
* list of users created on a specific host;

``debops.users`` is meant as a simple way to create a few dozen accounts max,
for larger number of accounts its preferred to use a dedicated solution,
like an LDAP directory.

.. contents:: Table of Contents
   :local:
   :depth: 2
   :backlinks: top

Installation
~~~~~~~~~~~~

This role requires at least Ansible ``v1.7.0``. To install it, run::

    ansible-galaxy install debops.users




Role variables
~~~~~~~~~~~~~~

List of default variables available in the inventory::

    ---
    
    # Should Ansible manage user accounts? Set to False to disable
    users: True
    
    
    # --- Lists of different accounts to create/manage ---
    
    # root user, if you want to change something on the root account
    users_root:
      - name: 'root'
    
    # Default user created by Ansible
    users_default:
      - name: '{{ ansible_ssh_user }}'
    
    # Administrators
    users_admins: []
    
    # Groups (normal or system)
    users_groups: []
    
    # "Global" users
    users_list: []
    
    # "Host group" users
    users_group_list: []
    
    # "Host" users
    users_host_list: []
    
    
    # --- An example account entry, everything except 'name' is optional
    # List of all recognized values, default value listed first
    # (users_list/users_admins/users_default/users_group_list/users_host_list/users_groups):
    #   name: 'username'               # mandatory, default group if not defined
    #   state: 'present,absent'
    #   group: 'name'                  # default group
    #   groups: []                     # list of groups to set
    #   append: yes/no                 # add to, or set groups
    #   gid: 1000
    #   uid: 1000
    #   shell: '/bin/sh'
    #   comment: 'GECOS entry'
    #   systemuser: False/True         # create system user
    #   systemgroup: False/True        # create system group
    #
    #    dotfiles: False/True           # download and configure dotfiles?
    #    dotfiles_repo: 'repository'
    #    dotfiles_command: 'make all'
    #    dotfiles_creates '~/.zshrc'
    #
    #    # Create ~/.forward file (set to False to remove ~/.forward)
    #    forward: [ 'user@domain', 'account' ]
    #
    #    # Add or disable ssh authorized keys (set to False to remove ~/.ssh/authorized_keys
    #    sshkeys: [ 'list', 'of', 'keys' ]
    
    
    # --- Global defaults ---
    
    # Default shell used for new accounts
    users_default_shell: '/bin/bash'
    
    # List of default groups added to new accounts
    users_default_groups_list: []
    
    # Should default groups be added to existing groups, or replace existing
    # groups?
    users_default_groups_append: 'yes'
    
    # Path to directory where home directories for new users are created
    users_default_home_prefix: '/home'
    
    # Default state of dotfiles on all accounts managed by Ansible
    # False - dotfiles are not configured by default
    # True - dotfiles will be configured by default
    users_default_dotfiles: False
    
    # Default dotfile hash to use
    users_default_dotfiles_key: 'drybjed'
    
    # List of dotfile hashes
    users_dotfiles:
      drybjed:
        repo: 'https://github.com/drybjed/dotfiles.git'
        command: 'make install'
        creates: '~/.zshrc'




Authors and license
~~~~~~~~~~~~~~~~~~~

``debops.users`` role was written by:

- Maciej Delmanowski | `e-mail <mailto:drybjed@gmail.com>`__ | `Twitter <https://twitter.com/drybjed>`__ | `GitHub <https://github.com/drybjed>`__

License: `GPLv3 <https://tldrlegal.com/license/gnu-general-public-license-v3-%28gpl-3%29>`_

