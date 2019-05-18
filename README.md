Ansible Role: tamay.php
=========

This role installs PHP from [Remi Repo](https://rpms.remirepo.net/)

Requirements
------------

None.

Role Variables
--------------

List of all variables, including default values.

    # php version to install
    php_version: php71

PHP version that should be installed. Possible values are for example *php56*, *php71*, *php72* and *php73*.
    
    # List of additional php modules
    php_modules: []
    # Example
    #php_modules:
    #  - bcmath
    #  - intl
    #  - pdo

List of php modules that should be installed. Use values from ```yum search {{ php_version }}-php-``` without *{{ php_version }}-php-*.

    # Creates symlink
    php_symlink: yes
    php_symlink_path: /usr/bin/php
    
Creates a symlink to the installed php binary, so that php can be executed with ```php``` instead of ```php71```.
    
    # Additional php.ini configuration
    php_ini_config: {}
    # Example:
    #php_ini_config:
    #  expose_php: off
    #  date.timezone: Europe/Berlin

This variable writes *php.ini* configuration to ```/etc/opt/remi/{{ php_version }}/php.d/50-custom.ini``` which overwrites values from the default *php.ini*. 
    
    # Configuration for Opcache extension
    php_opcache_config:
      enable: 1
      enable_cli: 1
      memory_consumption: 128
      interned_strings_buffer: 8
      max_accelerated_files: 10000
      save_comments: 1
      revalidate_freq: 1
      fast_shutdown: 1

Configuration for the zend opcache extension in ```/etc/opt/remi/{{ php_version }}/php.d/10-opcache.ini```.
    
    # Install and configure php-fpm
    php_fpm: yes

Defaults to yes. Installs PHP-FPM, and sets the default pool to use UNIX instead of TCP socket. The path for unix sockets is ```/run/php-fpm```. 
    
    # php-fpm pools to create
    php_fpm_pools: []
    # Example
    #php_fpm_pools:
    #  - name: test
    #    user: $pool
    #    group: $pool
    #    listen: /run/php-fpm/$pool-{{ php_version }}.sock
    #    listen_owner: $pool
    #    listen_group: apache
    #    pm: dynamic
    #    pm_max_children: 50
    #    pm_start_servers: 5
    #    pm_min_spare_servers: 5
    #    pm_max_spare_servers: 35
    #    config:
    #      php_admin_value[memory_limit]: 64M
    #      env[HOSTNAME]: $HOSTNAME
    #      env[PATH]: /usr/local/bin:/usr/bin:/bin
    #      env[TMP]: /tmp
    #      env[TMPDIR]: /tmp
    #      env[TEMP]: /tmp

Creates multiple pools. A user with the name of the pool will also be created. *Config* contains a dictionary of key-value pairs to further configure the pool. The only mandatory option is ```name```. Everything else will be filled with default values as seen above (except *config*).



Dependencies
------------

None.

Example Playbook
----------------

    ---
    - hosts: all
    
      vars:
        httpd_modules: []
        httpd_disable_welcome: no
        httpd_mpm_module: 'prefork'
    
        php_version: php71
        php_modules:
          - bcmath
          - intl
          - pdo
    
        php_ini_config:
          expose_php: off
          date.timezone: Europe/Berlin
    
        php_opcache_config:
          enable: 1
          enable_cli: 1
          memory_consumption: 128
          interned_strings_buffer: 8
          max_accelerated_files: 10000
          save_comments: 1
          revalidate_freq: 1
          fast_shutdown: 1
    
        php_fpm: yes
        php_fpm_pools:
          - name: test
            config:
              pm: dynamic
              pm.max_children: 50
              pm.start_servers: 5
              pm.min_spare_servers: 5
              pm.max_spare_servers: 35
              php_admin_value[memory_limit]: 64M
    
      roles:
        - tamay.httpd
        - tamay.php
    
      tasks:
        - name: "Create info.php"
          copy:
            dest: /var/www/html/info.php
            content: "<?php phpinfo() ?>"
            owner: apache
            group: apache
            mode: 0644

License
-------

MIT

Author Information
------------------

tamay.mueller@gmail.com
