# Ansible Role: Apache
Installs and configures Apache sites.

## Requirements
None.

## Role Variables
Available variables are listed below, along with their default values (see `defaults/main.yml`).

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
apache_vhost_options: '-Indexes +FollowSymLinks +MultiViews'
apache_vhost_allow_override: All
apache_vhost_require: all granted
```
The virtual host directory settings.

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
- name: rewrite
- name: ssl
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
apache_vhosts: []
```
A list of virtual hosts to create. `hostname` and `root` properties are required, `alias` is optional. HTTPS sites also require the `ssl_certificate` and `ssl_certificate_key` properties, `ssl_certificate_chain` is optional.

```yaml
apache_vhosts:
- hostname: example.test
  alias: example
  root: /var/www/html
- hostname: example-ssl.test
  root: /var/www/html
  ssl_certificate: /etc/ssl/example-ssl.test/certificate.crt
  ssl_certificate_key: /etc/ssl/private/example-ssl.test.key
  ssl_certificate_chain: /etc/ssl/example-ssl.test/fullchain.pem
```
An example virtual hosts configuration for both HTTP and HTTPS.

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
