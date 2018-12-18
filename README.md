# Ansible Role: Apache
Installs and configures Apache.

## Requirements
None.

## Role Variables
Available variables are listed below, see `defaults/main.yml` for the default values.

```yaml
apache_use_ppa: false
```
To use the PPA repository to install Apache, set the `apache_use_ppa` variable to `true`.

```yaml
apache_user: www
apache_group: www
```
You can change the user and group that Apache will run under. By default, Apache will use `www-data` for both user and group.

```yaml
apache_timeout: '100'
apache_enable_keepalive: false
apache_max_keepalive_request: '200'
apache_keepalive_timeout: '300'
apache_enable_hostname_lookups: true
```
Some of the main configuration directives used by Apache can be overridden by setting any of the above variables.

```yaml
apache_log_path: /path/to/logs
apache_error_log_level: warn
```
You can configure the error log using the `apache_error_log_level` variable. The error log level can be one of the follow: `trace8, ..., trace1, debug, info, notice, warn, error, crit, alert,` or `emerg`. By default, the error log level is `warn`.

You an override the default location where log files are stored by setting the `apache_log_path` variable. The default location is `/var/log/apache2`.

```yaml
apache_disable_default_site: true
```
Disables the default site installed with Apache.

```yaml
apache_pid_file: logs/apache.pid
```
The default `pid` directive setting is `/var/run/apache2/apache2.pid`. Use the `apache_pid_file` variable to override this.

```yaml
apache_run_path: /var/run/apache2
apache_lock_path: /var/lock/apache2
```
Use the `apache_run_path` and `apache_lock_path` variables to override the default values use for the temporary state file locations. The default for `apache_run_path` is `/var/run/apache2` and the default for `apache_lock_path` is `/var/lock/apache2`.

```yaml
apache_http_port: '8080'
apache_https_port: '44300'
```
To override the default ports used for HTTP and HTTPS, use the `apache_http_port` and `apache_https_port` variables.

```yaml
apache_allow_additional_confs: false
```
By default, Apache will include any configuration files found in either the `conf.d` or `conf-enabled` folder. To exclude these additional configurations set the `apache_allow_additional_confs` variable to `false`.

```yaml
apache_modules:
- name: headers
- name: rewrite
- name: ssl
  state: absent
```
Use the `apache_modules` variable to enable/disable Apache modules. The `name` attribute is required and should be the name of the module to enable/disable. By default, any listed module will be enabled. To disable a module add the `state` attribute with a value of `absent`.

```yaml
apache_sites:
- hostname: www.example.com
  root: /var/www/html
  alias: *.example.com
```
Use the `apache_sites` variable to add and configure sites. The only attributes required for a site are the `hostname` and the path to the `root` folder. An optional server `alias` can be added.

```yaml
apache_sites:
- hostname: example.com
  root: /var/www/html
  state: absent
```
By default a site is created when added to the `apache_sites` variable. Use the `state` attribute with a value of `absent` to disable/remove a site.

```yaml
apache_sites:
- hostname: example.com
  root: /var/www/html
  enable_http_to_https_redirect: true
```
Set the `enable_http_to_https_redirect` variable to redirect HTTP requests to the HTTPS protocol. Make sure that an SSL certificate has been set up and that Apache is configured to use SSL, as detailed below.

```yaml
apache_sites:
- hostname: www.sub.example.com
  root: /var/www/subdomain/sub
  virtualhost_directives: |
    ServerPath "/sub/"
    RewriteEngine On
    RewriteRule "^(/sub/.*)" "/var/www/subdomain$1"
```
Additional virtual host directives can be added using the `virtualhost_directives` attribute.

```yaml
apache_sites:
- hostname: example.com
  root: /var/www/html
  headers:
  - 'X-Frame-Options "DENY"'
  - 'X-Content-Type-Options "nosniff"'
  - 'X-XSS-Protection "1; mode=block"'
```
Use the `headers` attribute to add server response headers.

```yaml
apache_sites:
- hostname: example.com
  root: /var/www/html
  directories:
  - rule: '/var/www/html'
    block: |
      Options -Indexes +FollowSymLinks +MultiViews
      AllowOverride All
      Require all granted
```
Directory directives can be created by adding them to the `directories` attribute. Specify a `rule` for the directory and the directives to be included in the `block`,

```yaml
apache_sites:
- hostname: example.com
  root: /var/www/html
  filesmatches:
  - rule: '.+\.ph(p[3457]?|t|tml)$'
    block: |
      SetHandler proxy:unix:/run/php/php7.2-fpm.sock|fcgi://localhost
```
FilesMatch directives can be created by adding them to the `filesmatches` attribute. Specify a `rule` for the regex and the directives to be included in the `block`.

```yaml
apache_sites:
- hostname: example.com
  root: /var/www/html
  enable_error_log: true
  error_log_level: warn
  enable_access_log: true
  access_log_format: main
```
To create separate log files for individual sites set the `enable_error_log` and `enable_access_log` attributes to `true`. Use the `error_log_level` and `access_log_format` attributes to configure the logs. The error log level can be one of the follow: `trace8, ..., trace1, debug, info, notice, warn, error, crit, alert,` or `emerg`. By default, the error log level is `warn`. To use a custom access log format make sure the custom format exists. By default the access log format is `combined`.

```yaml
apache_sites:
- hostname: example.com
  root: /var/www/html
  ssl_certificate: /etc/ssl/example.com/certificate.crt
  ssl_certificate_key: /etc/ssl/private/example.com.key
  ssl_certificate_chain: /etc/ssl/example.com/fullchain.pem
```
To use HTTPS make sure an SSL certificate has been installed on the server. Specify the location of certificate using the `ssl_certificate` attribute and the location of the private key using the `ssl_certificate_key` attribute. An optional full chain certificate can be used by setting the `ssl_certificate_chain` variable.

```yaml
apache_sites:
- hostname: example.com
  root: /var/www/html
  https_virtualhost_directives: |
    SSLProtocol All -SSLv2 -SSLv3 -TLSv1 -TLSv1.1
    SSLCipherSuite EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH
    SSLHonorCipherOrder on
    SSLUseStapling on
    SSLCompression off
    SSLSessionTickets off
  https_headers:
  - 'Strict-Transport-Security "max-age=63072000; includeSubDomains; preload"'
```
Additional SSL directives and headers can be added using the `https_server_directives` and `https_headers` attribute.

```yaml
apache_sites:
- hostname: example.com
  root: /var/www/html
  server_directives: |
    SSLStaplingCache shmcb:logs/stapling-cache(150000)
```
Additional server directives can be added to the virtual host file using the `server_directives` attribute.

```yaml
apache_sites:
- hostname: example.com
  root: /var/www/html
  ip_address: '*'
  http_port: '80'
  https_port: '443'
```
The default values used to configure a site are shown above. Add any of these attributes to the site to override their value.

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
