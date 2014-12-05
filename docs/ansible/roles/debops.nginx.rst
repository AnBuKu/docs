debops.nginx
############


`nginx`_ is a fast and light webserver with extensible configuration.

``debops.nginx`` role can be used to install and manage ``nginx``
configuration for multiple websites at the same time. Server is configured
using inventory variables, role can also be used as a dependency of another
role to configure a webserver for that role using dependency variables.

.. _nginx: http://nginx.org/

.. contents:: Table of Contents
   :local:
   :depth: 2
   :backlinks: top

Installation
~~~~~~~~~~~~

This role requires at least Ansible ``v1.7.0``. To install it, run::

    ansible-galaxy install debops.nginx


Role dependencies
~~~~~~~~~~~~~~~~~

- ``debops.ferm``
- ``debops.apt_preferences``


Role variables
~~~~~~~~~~~~~~

List of default variables available in the inventory::

    ---
    
    # List of IP addresses or CIDR networks allowed to connect to HTTP or HTTPS
    # service. It will be configured in iptables firewall via 'ferm' role. If there
    # are no entries, nginx will accept connections from any IP address or network.
    # If you have multiple web services on a host, you might want to control access
    # using 'item.location_allow' option instead.
    nginx_allow: []
    nginx_group_allow: []
    nginx_host_allow: []
    
    nginx_base_packages: [ 'nginx-full' ]
    nginx_user: 'www-data'
    
    # Nicenness, from 20 (nice) to -20 (not nice)
    nginx_worker_priority: '0'
    
    nginx_worker_processes: '{{ ansible_processor_cores }}'
    nginx_worker_connections: 1024
    
    # Maximum number of opened files per process, must be higher than worker_connections
    nginx_worker_rlimit_nofile: 4096
    
    nginx_server_tokens: 'off'
    
    nginx_server_names_hash_bucket_size: 64
    nginx_server_names_hash_max_size: 512
    
    nginx_default_keepalive_timeout: 60
    
    # Path to PKI infrastructure, set to False to disable nginx SSL support
    nginx_pki: '/srv/pki'
    
    # SSL key hostname to look for to check if SSL should be enabled
    nginx_pki_check_key: '{{ ansible_fqdn }}'
    
    # Where to look for certificate by default, choices: 'selfsigned', 'signed', 'wildcard'
    # Server-wide.
    nginx_default_ssl_type: 'selfsigned'
    
    # Default hostname used to select SSL certificate if none is defined
    nginx_default_ssl_cert: '{{ ansible_fqdn }}'
    
    # Default SSL cipher list. More information in vars/main.yml
    nginx_default_ssl_ciphers: 'pfs'
    
    # Default SSL ECDH curve used on servers, to see a list of supported curves, run:
    #     openssl ecparam -list_curves
    # See also: https://security.stackexchange.com/questions/31772/
    nginx_default_ssl_curve: 'secp384r1'
    
    # If wildcard SSL certificate is used, which one should be used by default?
    nginx_default_ssl_wildcard: '{{ ansible_domain }}'
    
    # List od DNS servers used to resolve OCSP stapling. If it's empty, nginx role
    # will try to use nameservers from /etc/resolv.conf
    # Currently only the first nameserver is used
    nginx_ocsp_resolvers: []
    
    # At what hour run DH params regeneration script?
    nginx_cron_dhparams_hour: '1'
    
    # HTTP Strict-Transport-Security
    # https://en.wikipedia.org/wiki/HTTP_Strict_Transport_Security
    nginx_hsts_age: '15768000'
    nginx_hsts_subdomains: True
    
    # server_name which will be marked as default
    nginx_default_name: '{{ ansible_fqdn }}'
    
    # Default server template used if no type is selected
    nginx_default_type: 'default'
    
    # Default server root
    nginx_default_root: '/srv/www/sites/default/public'
    
    # Create global webroot directories?
    # Path: /srv/www/sites/*/public
    nginx_webroot_create: True
    nginx_webroot_owner: 'root'
    nginx_webroot_group: 'root'
    nginx_webroot_mode: '0755'
    
    # Should nginx servers have status pages enabled by default
    # If yes, provide a list of allowed networks/hosts
    #nginx_default_status:
    #  - '127.0.0.0/8'
    
    # Hash of symlinks to local server definitions stored in /etc/nginx/sites-local/
    # Entries with empty values or False will be removed
    # Symlinks will be created in /etc/nginx/sites-enabled/
    nginx_local_servers: {}
      #'symlink': 'file'
      #'other-symlink.conf': 'sub/directory/file.conf'
      #'removed-file': False
      #'also-removed':
      #'symlink\ with\ spaces.conf': 'other-file.conf'
    
    # List of nginx map definitions
    # Each map should be defined in it's own hash variable, similar to upstreams
    # and servers
    # http://nginx.org/en/docs/http/ngx_http_map_module.html
    nginx_maps: []
    
    # List of nginx upstream definitions
    nginx_upstreams: [ '{{ nginx_upstream_php5 }}' ]
    
    # Upstream for default php5-fpm configuration
    nginx_upstream_php5:
      enabled: True
      name: 'php5_www-data'
      type: 'php5'
      php5: 'www-data'
    
    # List of nginx server definitions
    nginx_servers: [ '{{ nginx_server_default }}' ]
    
    # Default nginx site
    # List and description of available parameters can be found in nginx server
    # templates: templates/etc/nginx/sites-available/*.conf.j2
    nginx_server_default:
      enabled: True
      name: []
      default: True

List of internal variables used by the role::

    nginx_register_default_server_specified
    nginx_register_default_server_name
    nginx_ssl
    nginx_ocsp_resolvers
    nginx_register_default_server_first


Authors and license
~~~~~~~~~~~~~~~~~~~

``debops.nginx`` role was written by:

- Maciej Delmanowski | `e-mail <mailto:drybjed@gmail.com>`__ | `Twitter <https://twitter.com/drybjed>`__ | `GitHub <https://github.com/drybjed>`__

License: `GPLv3 <https://tldrlegal.com/license/gnu-general-public-license-v3-%28gpl-3%29>`_

