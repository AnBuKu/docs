debops.etherpad
###############



This role installs and configures `Etherpad`_, an on-line multiuser
collabolative text editor. It will be installed behind ``nginx`` server
with MySQL as a database backend.

.. _Etherpad: http://etherpad.org/

.. contents:: Table of Contents
   :local:
   :depth: 2
   :backlinks: top

Installation
~~~~~~~~~~~~

This role requires at least Ansible ``v1.7.0``. To install it, run::

    ansible-galaxy install debops.etherpad


Role dependencies
~~~~~~~~~~~~~~~~~

- ``debops.etc_services``
- ``debops.secret``
- ``debops.mysql``
- ``debops.nginx``
- ``debops.nodejs``


Role variables
~~~~~~~~~~~~~~

List of default variables available in the inventory::

    ---
    
    # ---- Basic configuration ----
    
    # Etherpad git version to install
    etherpad_version: 'develop'
    
    # Should etherpad role manage it's own dependencies?
    etherpad_dependencies: True
    
    # What domain will be configured for Etherpad
    etherpad_domain: [ 'pad.{{ ansible_domain }}' ]
    
    # Title of Etherpad instance
    etherpad_title: 'Etherpad'
    
    # E-mail address of the instance administrator, will be shown on each new pad
    # (see 'etherpad_welcome_text' below)
    etherpad_mail_admin: 'root@{{ ansible_domain }}'
    
    # Text displayed on all new pads by default
    etherpad_welcome_text: 'Welcome to {{ etherpad_title }}!\n\nThis pad is synchronized as you type, so that everyone viewing this page sees the same text. This allows you to collaborate seamlessly on documents.\n\nContact with administrator: mailto:{{ etherpad_mail_admin }}\n\n'
    
    
    # ---- Database and network ----
    
    # Allow access only from selected IP addresses/CIDR networks. If empty, allow
    # access from everywhere
    etherpad_allow: []
    
    # Database to use for data storage (choices: mysql, dirty)
    # More databases will be available in the future
    etherpad_database: 'mysql'
    
    # Connection type used for the database
    etherpad_database_connection: 'socket'
    
    # IP address and port where etherpad-lite daemon will listen for connections
    etherpad_bind: '127.0.0.1'
    etherpad_port: '9001'
    
    
    # ---- Authentication ----
    
    # List of Etherpad administrative and user accounts, only works with 'secret'
    # variable defined (see 'secret' role). Passwords are generated automatically and
    # saved in secret/ directory
    etherpad_admins: [ 'admin' ]
    etherpad_users: []
    
    # Require authentication from all users
    etherpad_require_authentication: 'false'
    
    # Require authorization by a module or user with is_admin = True
    etherpad_require_authorization: 'false'
    
    # Require session to access pads
    etherpad_require_session: 'false'
    
    # Block creation of new pads by unauthorized users?
    etherpad_edit_only: 'false'
    
    # Trust the reverse proxy (nginx)?
    etherpad_trust_proxy: 'false'
    
    
    # ---- Etherpad customization ----
    
    # Enable Abiword support (for document import)?
    etherpad_abiword: True
    
    # List of Etherpad plugins to enable
    etherpad_plugins:
        - 'adminpads'
        - 'align'
        - 'font_color'
        - 'font_family'
        - 'font_size'
        - 'headings'
        - 'hide_referrer'
        - 'line_height'
        - 'linkify'
        - 'message_all'
        - 'padlist'
        - 'page_view'
        - 'print'
        - 'rss'
        - 'scrollto'
        - 'superscript'
        - 'subscript'
    
    
    # ---- Other options ----
    
    # Minify CSS and JS assets?
    etherpad_minify: 'true'
    
    # Maximum age of cached assets (6 hours by default)
    etherpad_max_age: '{{ (60 * 60 * 6) }}'
    
    # Disable IP addresses in logs?
    etherpad_disable_ip_logging: 'false'
    
    # Etherpad log level (choices: DEBUG, INFO, WARN, ERROR)
    etherpad_loglevel: 'INFO'
    
    # Here you can define custom settings.json entries in YAML format, which will
    # be converted to JSON and put at the end of the configuration file
    etherpad_custom_json: False

List of internal variables used by the role::

    etherpad_mysql_database_password
    etherpad_session_key


Authors and license
~~~~~~~~~~~~~~~~~~~

``debops.etherpad`` role was written by:

- Maciej Delmanowski | `e-mail <mailto:drybjed@gmail.com>`__ | `Twitter <https://twitter.com/drybjed>`__ | `GitHub <https://github.com/drybjed>`__

License: `GPLv3 <https://tldrlegal.com/license/gnu-general-public-license-v3-%28gpl-3%29>`_

