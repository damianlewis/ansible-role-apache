# Ansible Role: Apache
Installs and configures Apache sites.

## Requirements
None.

## Role Variables
Available variables are listed below, along with their default values (see `defaults/main.yml`).

```yaml
apache_sites_available_path: /etc/apache2/sites-available
apache_sites_enabled_path: /etc/apache2/sites-enabled
apache_default_site_path: /etc/apache2/sites-enabled/000-default.conf
```
The default paths and site used by Apache.

```yaml
apache_http_port: 80
apache_https_port: 443
```
The ports that Apache uses.

```yaml
apache_ip_address: '*'
```
The IP address that the virtual host is bound to.

```yaml
apache_error_log_level: error
apache_access_log_format: combined
```
The error and access log settings.

```yaml
apache_ssl_cipher_suite: 'EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH'
apache_ssl_protocol: 'All -SSLv2 -SSLv3 -TLSv1 -TLSv1.1'
apache_ssl_stapling_cache: 'shmcb:logs/stapling-cache(150000)'
apache_ssl_hsts: '"max-age=63072000; includeSubDomains; preload"'
```
The SSL cipher, protocol and stapling cache settings.

```yaml
apache_enable_error_log: false
apache_enable_access_log: false
apache_disable_default_site: true
apache_enable_http_to_https_redirect: false
apache_enable_ssl_cipher_suite: false
apache_enable_ssl_protocol: false
apache_enable_ssl_cipher_order: false
apache_enable_ssl_stapling: false
apache_enable_ssl_hsts: false

# Requires Apache >= 2.4.3
apache_disable_ssl_compression: false

# Requires Apache >= 2.4.11
apache_disable_ssl_session_tickets: false
```
Configure the Apache virtual host file by enabling/disabling these settings.

```yaml
apache_modules: []
```
A list of Apache modules to enable/disable. The default state is `present`, use `absent` to disable a module.

```yaml
apache_modules: []
- name: ssl
- name: rewrite
  state: absent
```
An example of how to enable/disable Apache modules.

```yaml
apache_headers: []
```
A list of Apache headers to include for virtual hosts.

```yaml
apache_headers:
- X-Frame-Options DENY
- X-Content-Type-Options nosniff
```
An example of included Apache headers.

```yaml
apache_sites: []
```
A list of sites to create. `hostname` and `root` properties are required, `alias` is optional. HTTPS sites also require the `ssl_certificate` and `ssl_certificate_key` properties, `ssl_certificate_chain` is optional.

```yaml
apache_sites:
- hostname: ''
  root: ''
  state: present
```
Default values used for site configurations. The default state of a site is `present`, use `absent` to disable/remove a site.

```yaml
apache_sites:
- hostname: example1.test
  alias: example1
  root: /var/www/html/example1
  directory:
  - rule: '"/var/www/html/example1"'
    block:
    - 'Options -Indexes +FollowSymLinks +MultiViews'
    - 'AllowOverride All'
    - 'Require all granted'
  filesmatch:
  - rule: '".+\.ph(p[3457]?|t|tml)$"'
    block:
    - 'SetHandler proxy:unix:/run/php/php5.6-fpm.sock|fcgi://localhost'
- hostname: example2.test
  alias: example2
  root: /var/www/html/example2
  ssl_certificate: /etc/ssl/example2.test/certificate.crt
  ssl_certificate_key: /etc/ssl/private/example2.test.key
  ssl_certificate_chain: /etc/ssl/example2.test/fullchain.pem
  directory:
  - rule: '"/var/www/html/example2"'
    block:
    - 'Options -Indexes +FollowSymLinks +MultiViews'
    - 'AllowOverride All'
    - 'Require all granted'
  filesmatch:
  - rule: '".+\.ph(ar|p|tml)$"'
    block:
    - 'SetHandler proxy:unix:/run/php/php7.2-fpm.sock|fcgi://localhost'
```
An example configuration for both a HTTP and HTTPS site. Both these examples show how to use different versions of PHP and make use of unix socket connections for PHP-FPM.

Note: These examples assume you have PHP-FPM installed and Apache configured to use the proxy and proxy_fcgi modules. Consider using the [damianlewis.apache-php-fpm](https://github.com/damianlewis/ansible-role-apache-php-fpm) ansible role.

## Dependencies
None.

## Example Playbook
```yaml
- hosts: server
  become: yes

  tasks:
  - import_role:
      name: damianlewis.apache
```
