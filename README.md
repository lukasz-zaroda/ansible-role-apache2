Ansible role: Apache 2 Fast CGI
===================

Apache 2 installation, configuration, and easy virtual hosts management made to work with
[PHP-FPM ansible role](https://github.com/lukasz-zaroda/ansible-role-php-fpm).

NOTE: It doesn't configure PHP-FPM by itself. For this you should use the mentioned role.

Requirements
------------

Ansible >= 2.2

Role Variables
--------------

**apache2_vhosts_defaults**

This is a dictionary of settings, which will be added to all virtual hosts. For example:

```yaml
apache2_vhosts_defaults:
  serveradmin: admin@example.com
```

The above example will set `admin@example.com` as a `ServerAdmin` for all virtual hosts.

**apache2_vhosts**

This is a dictionary of virtual hosts with their settings. For example:

```yaml
apache2_vhosts:
  example1.com:
    ssl:
      enabled: true
      cert: /etc/ssl/local/luken-example1.com/cert.pem
      key: /etc/ssl/private/luken-example1.com.key
      chain: /etc/ssl/local/luken-example1.com/ca-chain.crt
    root_owner: example1
    root_group: example1
    documentroot: { path: /var/www/html/example1.com/public_html }
  example2.com:
    root_owner: example2
    root_group: example2
    documentroot: { path: /var/www/html/example2.com/public_html }
```

The above example configuration will create two virtual hosts. For each virtual host a root
directory will be also created, and proper permissions to it will be set. First virtual host
will have the SSL configured, by using specified certificate paths. Note that `documentroot`
setting is a dictionary. This is because you can add custom directives to this directory, by
adding a dictionary with them under the key `directives`, for example:

```yaml
example2.com:
  root_owner: example2
  root_group: example2
  documentroot:
    path: /var/www/html/example2.com/public_html
    directives:
    - "Options Indexes FollowSymLinks MultiViews"
    - "AllowOverride None"
    - "Require all granted"
```

The above example overrides the default directives for all virtual hosts which come from the
dictionary: `apache2_documentroot_directives_default` - this dictionary have the following default
values:

```yaml
apache2_documentroot_directives_default:
- "Options Indexes FollowSymLinks MultiViews"
- "AllowOverride All"
- "Require all granted"
```

All available settings for a virtual host are:

  - ssl:
    - enabled (true/false)
    - cert (path on server)
    - key (path on server)
    - chain (path on server) (optional)
  - root_mode (permission mode for the root directory, for example: "0775")
  - root_owner (owner of the root directory)
  - root_group (group of the root directory)
  - document_root
    - path
    - directives (optional)
  - enabled (true/false)
  - ip
  - serveradmin
  - fpm_pattern (used for identifying PHP files for php-fpm)

You can look at default values for the aboce in defaults/main.yml

Dependencies
------------

This role is made to be used alongside with [PHP-FPM role](https://github.com/lukasz-zaroda/ansible-role-php-fpm) .
It configures Apache to use PHP-FPM sockets created by the mentioned role.

If you don't want to configure Apache2 to work with PHP-FPM then you can set
`apache2_php` variable to false, to install vanilla Apache.

Example Playbook
----------------

```yaml

---
- hosts: servers
  become: true
  become_method: sudo
  roles:
  - role: apache2-fcgi
  
    apache2_vhosts_defaults:
      serveradmin: admin@example.com
    apache2_vhosts:
      example1.com:
        ssl:
          enabled: true
          cert: /etc/ssl/local/example1.com/cert.pem
          chain: /etc/ssl/local/example1.com/ca-chain.crt
          key: /etc/ssl/private/example1.com.key
        root_owner: example1
        root_group: example1
        documentroot: { path: /var/www/html/example1.com/public_html }
      example2.com:
        root_owner: example2
        root_group: example2
        documentroot: { path: /var/www/html/example2.com }

```

License
-------

MIT

Author Information
------------------

Łukasz Zaroda - https://luken-tech.pl