debops.owncloud
###############



This role installs `ownCloud`_ instance on a specified host, with either
MySQL or PostgreSQL database as a backend and an nginx webserver as
a frontend.

At the moment role doesn't work correctly due to the changes in ownCloud
repository structure.

.. _ownCloud: http://owncloud.org/

.. contents:: Table of Contents
   :local:
   :depth: 2
   :backlinks: top

Installation
~~~~~~~~~~~~

This role requires at least Ansible ``v1.7.0``. To install it, run::

    ansible-galaxy install debops.owncloud


Role dependencies
~~~~~~~~~~~~~~~~~

- ``debops.secret``
- ``debops.nginx``
- ``debops.postgresql``
- ``debops.php5``
- ``debops.mysql``


Role variables
~~~~~~~~~~~~~~

List of default variables available in the inventory::

    ---
    
    # --- Basic options ---
    
    # Should ownCloud role manage it's own dependencies (nginx, php5, postgresql, mysql)?
    # If you want to setup them differently, you should change this to False
    owncloud_dependencies: True
    
    # Domain that will be configured for ownCloud instance
    owncloud_domain: [ 'owncloud.{{ ansible_domain }}' ]
    
    
    # --- ownCloud source and deployment ---
    
    # User and group that will be used for ownCloud instance
    owncloud_user: 'owncloud'
    owncloud_group: 'owncloud'
    owncloud_home: '/srv/users/{{ owncloud_user }}'
    
    # Path where ownCloud data/ directory and files are stored
    owncloud_data_path: '/srv/lib/{{ owncloud_user }}/{{ owncloud_domain[0] }}/data'
    
    # Where ownCloud instance will be deployed (web root)
    owncloud_deploy_path: '/srv/www/{{ owncloud_user }}/sites/{{ owncloud_domain[0] }}/public'
    
    # What source should be used to get ownCloud files (upstream, githost, github, gitlab)
    owncloud_source: 'upstream'
    
    # Default URL and git branch for ownCloud upstream repository
    owncloud_upstream_url: 'https://github.com/owncloud/core.git'
    owncloud_upstream_branch: 'stable6'
    
    # Default settings for ownCloud sources other than "upstream"
    # If you want to use that with github, you can create your ownCloud repository
    # as '<your username>/owncloud' with branch 'master'
    owncloud_deploy_user: 'git'
    owncloud_deploy_server: 'github.com'
    owncloud_deploy_repo: '{{ lookup("env","USER") }}/owncloud.git'
    owncloud_deploy_branch: 'master'
    
    # OAuth token for GitHub / GitLab access, used to setup SSH deploy key
    owncloud_deploy_token: False
    
    # Hash of different ownCloud sources
    owncloud_source_map:
    
      # Official ownCloud upstream repository on GitHub (basic installation)
      upstream:
        url:    '{{ owncloud_upstream_url }}'
        branch: '{{ owncloud_upstream_branch }}'
    
      # A git repository configured on a host in the Ansible cluster, for example
      # with 'githost' role
      # Ansible will try and setup ssh public key of the 'owncloud' user as deploy
      # key on the server with git repository, using authorized_key module
      githost:
        url:    '{{ owncloud_deploy_user }}@{{ owncloud_deploy_server }}:{{ owncloud_deploy_repo }}'
        branch: '{{ owncloud_deploy_branch }}'
    
      # A git repository set up on GitHub, with deploy key configured through API
      # using OAuth token
      github:
        url:    'git@github.com:{{ owncloud_deploy_repo }}'
        branch: '{{ owncloud_deploy_branch }}'
    
      # A git repository set up on a GitLab instance, with deploy key configured
      # through API using OAuth token
      gitlab:
        url:    '{{ owncloud_deploy_user }}@{{ owncloud_deploy_server }}:{{ owncloud_deploy_repo }}'
        branch: '{{ owncloud_deploy_branch }}'
    
    
    # --- ownCloud database ---
    
    # ownCloud recommends MySQL database as the default. Set to False to use SQLite
    owncloud_database: 'mysql'
    
    owncloud_database_map:
    
      # MySQL database on localhost (random password will be generated when using 'secret' role)
      mysql:
        dbtype: 'mysql'
        dbname: '{{ owncloud_user }}'
        dbuser: '{{ owncloud_user }}'
        dbpass: '{{ owncloud_dbpass | default("password") }}'
        dbhost: 'localhost'
        dbtableprefix: ''
    
      # PostgreSQL database on localhost, connection through Unix socket, no default password
      postgresql:
        dbtype: 'pgsql'
        dbname: '{{ owncloud_user }}'
        dbuser: '{{ owncloud_user }}'
        dbpass: ''
        dbhost: '/var/run/postgresql'
        dbtableprefix: ''
    
    
    # --- ownCloud admin login / password ---
    
    # Default admin username, in the form 'admin-$USER'
    # Set to False to disable automatic username and password
    owncloud_admin_username: 'admin-{{ lookup("env","USER") }}'
    
    # Default admin password, will be randomly generated if 'secret' role is enabled
    owncloud_admin_password: 'password'
    
    # Length of randomly generated admin password
    owncloud_password_length: '20'
    
    # Should Ansible automatically open ownCloud page to finish setup on it's own?
    # Disabled if admin username is set to False
    owncloud_autosetup: True
    
    
    # --- ownCloud configuration ---
    
    # Max upload size set in nginx and php5, with amount as M or G
    owncloud_upload_size: '128M'
    
    # Output buffering set in php5, with amount set in megabytes
    owncloud_php5_output_buffering: '128'
    
    # Max children processes to run in php5-fpm
    owncloud_php5_max_children: '50'
    
    # At what time cron should execute background jobs
    owncloud_cron_minute: '*/15'

List of internal variables used by the role::

    owncloud_database_password
    owncloud_admin_password
    owncloud_deploy_data


Authors and license
~~~~~~~~~~~~~~~~~~~

``debops.owncloud`` role was written by:

- Maciej Delmanowski | `e-mail <mailto:drybjed@gmail.com>`__ | `Twitter <https://twitter.com/drybjed>`__ | `GitHub <https://github.com/drybjed>`__

License: `GPLv3 <https://tldrlegal.com/license/gnu-general-public-license-v3-%28gpl-3%29>`_

