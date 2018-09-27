# Ansible Role: Apache
Installs and configures Apache.

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
apache_error_log_level: error
apache_access_log_format: combined
```
The error and access log settings.

```yaml
apache_vhost_options: "-Indexes +FollowSymLinks +MultiViews"
apache_vhost_allow_override: "All"
apache_vhost_require: "all granted"
```
The virtual host directory settings. 

```yaml
apache_ssl_cipher_suite: 'EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH'
apache_ssl_protocol: 'All -SSLv2 -SSLv3 -TLSv1 -TLSv1.1'
apache_ssl_stapling_cache: 'shmcb:logs/stapling-cache(150000)'
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
apache_disable_ssl_compression: false # Requires Apache >= 2.4.3
apache_disable_ssl_session_tickets: false # Requires Apache >= 2.4.11
```
Configure the Apache virtual host file by enabling/disabling these settings. The Apache `SSLCompression` directive requires version 2.4.3 and the directive `SSLSessionTickets` requires version 2.4.11.

```yaml
apache_modules:
- name: rewrite
- name: ldap
  state: absent
```
A list of Apache modules to enable/disable. The default state is `present`, use `absent` to disable a module.

```yaml
apache_headers:
- 'X-Frame-Options DENY'
- 'X-Content-Type-Options nosniff'
```
A list of Apache headers to include for virtual hosts.

```yaml
apache_ssl_headers:
- 'Strict-Transport-Security "max-age=63072000; includeSubDomains; preload"'
```
A list of Apache headers to include in the HTTPS virtual host section.

```yaml
apache_vhosts:
- hostname: example.test
  alias: example
  root: /var/www/html
- hostname: example-ssl.test
  root: /var/www/html
  ssl_certificate: /etc/ssl/example-ssl.test/certificate.crt
  ssl_certificate_key: /etc/ssl/example-ssl.test/privatekey.pem
  ssl_certificate_chain: /etc/ssl/example-ssl.test/fullchain.pem
```
A list of virtual hosts to create. `hostname` and `root` are required. HTTPS sites also require `ssl_certificate` and `ssl_certificate_key`. Optional properties are `alias` and `ssl_certificate_chain`.

## Dependencies
None.

## Example Playbook
```yaml
- hosts: app
  become: yes

  - import_role:
      name: damianlewis.apache
```
