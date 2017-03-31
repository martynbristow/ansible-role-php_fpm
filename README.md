# ansible-role-php_fpm

Install and configure PHP-FPM

Don't forget to install nginx before run this role use `nginx` as default owner and group.

## Example

```yaml
php_fpm_install:
  - php5-cli
  - php5-xdebug

php_fpm_configure:
  - file: {{ php_fpm_path }}/cli/php.ini
    values:
      - { section: date, key: date.timezone, value: '{{ php_timezone }}' }
  - file: {{ php_fpm_path }}/mods-available/xdebug.ini
    values:
      - { section: date, key: date.timezone, value: '{{ php_timezone }}' }
      - { section: xdebug, key: xdebug.idekey, value: my-idekey }

app_name: myapp
php_fpm_pools:
  myapp:
    src: "templates/pool-template.conf.j2"
    dest: "{{ app_name }}.conf"
```

Add your pool:

```
# templates/pool-template.conf.j2
[{{ app_name }}]

listen = /var/run/php-fpm/{{ app_name }}.sock
listen.allowed_clients = 127.0.0.1
listen.owner = nginx
listen.group = nginx
listen.mode = 0660

user = nginx
group = nginx

pm = static
pm.max_children = 20
pm.max_requests = 5000
pm.process_idle_timeout = 20s
```

```
# playbook.yml
roles:
  - role: igor_mukhin.php_fpm
    tags: php
```

## Migrating to php7

```
# Use this repository if you want to install php7.0 on Ubuntu 14
# You don't need it on Ubuntu 16
php_fpm_repository: "ppa:ondrej/php"
php_fpm_install:
  - php7.0-fpm
php_fpm_path: "/etc/php/7.0"
php_fpm_service: "php7.0-fpm"
```

## Authors

* [kosssi](https://github.com/kosssi)
* igormukhingmailcom

## License

MIT
