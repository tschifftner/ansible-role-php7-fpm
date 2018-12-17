# Ansible Role: php7-fpm

[![Build Status](https://travis-ci.org/tschifftner/ansible-role-php7-fpm.svg?branch=master)](https://travis-ci.org/tschifftner/ansible-role-php7-fpm)

Installs php7-fpm on Debian/Ubuntu linux servers.

## Requirements

None.

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

### Create vhosts

```
php7_fpm_pools:
  - name: 'live'
    user: 'live'
    prefix: '/var/www/live/current'
    tmp_path: '/var/www/live/tmp'
    sessions_path: '/var/www/live/sessions'
    releases_path: '/var/www/live/releases'
    shared_path: '/var/www/live/shared'
    logs_path: '/var/www/live/logs'
    project_paths:
      - '/var/www/live/shared/media'
      - '/var/www/live/shared/var'
    php_admin_value:
      error_log: 'logs/php_errors.log'
      max_input_vars: 5000
      max_execution_time: 300
      memory_limit: '256M'

  - name: 'staging'
    user: 'staging'
    pm: ondemand
    pm_max_children: 2
    prefix: '/var/www/staging/current'
    tmp_path: '/var/www/staging/tmp'
    sessions_path: '/var/www/staging/sessions'
    releases_path: '/var/www/staging/releases'
    shared_path: '/var/www/staging/shared'
    logs_path: '/var/www/staging/logs'
    project_paths:
      - '/var/www/staging/shared/media'
      - '/var/www/staging/shared/var'
    php_admin_value:
      error_log: 'logs/php_errors.log'
      max_input_vars: 5000
      max_execution_time: 300
      memory_limit: '256M'
```

### Remove vhosts

To remove vhosts _remove them entirely_ or set ```state: absent``` 
```
php7_fpm_pools:
  - name: 'live'
    ...
    state: absent
```
     
### Custom folders

Custom folders are created when defined in ```project_paths``` 
```
php7_fpm_pools:
  - name: 'live'
    ...
    project_paths:
      - '/var/www/live/shared/media'
      - '/var/www/live/shared/var'
```
      
### php.ini variables
```
# php.ini (cli) settings
php7_cli_ini_expose_php: On
php7_cli_ini_memory_limit: 64M
php7_cli_ini_max_execution_time: 30
php7_cli_ini_max_input_time: 60
php7_cli_ini_max_input_vars: 100
php7_cli_ini_realpath_cache_size: 32K
php7_cli_ini_realpath_cache_ttl: 120
php7_cli_ini_upload_max_filesize: 64M
php7_cli_ini_post_max_size: 32M
php7_cli_ini_date_timezone: "Europe/Berlin"
php7_cli_ini_sendmail_path: "/usr/sbin/sendmail -t -i"
php7_cli_ini_error_reporting: "E_ALL & ~E_DEPRECATED & ~E_STRICT"
php7_cli_ini_display_errors: "Off"
php7_cli_ini_display_startup_errors: "Off"
php7_cli_ini_disable_functions: 'pcntl_alarm,pcntl_fork,pcntl_waitpid,pcntl_wait,pcntl_wifexited,pcntl_wifstopped,pcntl_wifsignaled,pcntl_wexitstatus,pcntl_wtermsig,pcntl_wstopsig,pcntl_signal,pcntl_signal_dispatch,pcntl_get_last_error,pcntl_strerror,pcntl_sigprocmask,pcntl_sigwaitinfo,pcntl_sigtimedwait,pcntl_exec,pcntl_getpriority,pcntl_setpriority,'
php7_cli_ini_openssl: ''

# php.ini (fpm) settings
php7_fpm_ini_expose_php: On
php7_fpm_ini_memory_limit: 64M
php7_fpm_ini_max_execution_time: 30
php7_fpm_ini_max_input_time: 60
php7_fpm_ini_max_input_vars: 100
php7_fpm_ini_realpath_cache_size: 32K
php7_fpm_ini_realpath_cache_ttl: 120
php7_fpm_ini_upload_max_filesize: 64M
php7_fpm_ini_post_max_size: 32M
php7_fpm_ini_date_timezone: "Europe/Berlin"
php7_fpm_ini_sendmail_path: "/usr/sbin/sendmail -t -i"
php7_fpm_ini_error_reporting: "E_ALL & ~E_DEPRECATED & ~E_STRICT"
php7_fpm_ini_display_errors: "Off"
php7_fpm_ini_display_startup_errors: "Off"
php7_fpm_ini_openssl: ''
```

### APCu variables
```
# APCu
php7_apcu_ini:
  enabled: 1
  shm_segments: 1
  shm_size: 256M
  num_files_hint: 10000
  user_entries_hint: 10000
  max_file_size: 1M
  optimization: 0
  ttl: 0
  user_ttl: 0
  gc_ttl: 600
  cache_by_default: 1
  filters: ""
  slam_defense: 0
  use_request_time: 1
  mmap_file_mask: /tmp/apc-dummy.XXXXXX
  file_update_protection: 2
  enable_cli: 0
  stat: 1
  write_lock: 1
  report_autofilter: 0
  include_once_override: 0
  rfc1867: 1
  rfc1867_prefix: "upload_"
  rfc1867_name: "APC_UPLOAD_PROGRESS"
  rfc1867_freq: 0
  localcache: 1
  localcache.size: 512
  coredump_unmap: 0
  stat_ctime: 0
```

### Opcache variables
```
# OpCache
php7_opcache_ini:
  enable: "1"
  enable_cli: "0"
  memory_consumption: "96"
  interned_strings_buffer: "16"
  max_accelerated_files: "4096"
  max_wasted_percentage: "5"
  validate_timestamps: "1"
  revalidate_freq: "2"
  max_file_size: "0"
  save_comments: "1"

```

## Multiple php versions

```
- hosts: projects-php7
  become: true

  roles:
    - { role: tschifftner.sury, tags: ['sury'] }
    - { role: tschifftner.php7-fpm, tags: ['php7-fpm', 'php7.0-fpm'], php7_fpm_version: '7.0' }
    - { role: tschifftner.php7-fpm, tags: ['php7-fpm', 'php7.1-fpm'], php7_fpm_version: '7.1' }
```

## Dependencies

 - [tschifftner.sury](https://github.com/tschifftner/ansible-role-sury)

## Example Playbook

    - hosts: server
      roles:
        - { role: tschifftner.php7-fpm }

## Supported OS

 - Debian 9 (Stretch)
 - Debian 8 (Jessie)
 - Ubuntu 18.04 (Bionic Beaver)
 
## Required ansible version

Ansible 2.5+

## License

[MIT License](http://choosealicense.com/licenses/mit/)


## Author Information

 - [Tobias Schifftner](https://twitter.com/tschifftner), [ambimax® GmbH](https://www.ambimax.de)