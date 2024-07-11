# php-fpm

This is an Ansible role which sets up php-fpm pools. It's based on https://github.com/stuvusIT/php-fpm, but diverged from it.

Compared to stuvusIT's version, the two main differences are:

1. It focusses only pool configuration, it doesn't bother installing packages or even setting default php.ini settings. The role assumes you've done that prior to configuring the pools. The role works in tandem with e.g. https://github.com/lukasic/ansible-role-multi-php

2. It supports multiple php versions. You can have one pool running PHP 8.1, two pool on 8.3 and 4 on 8.2.


## Requirements

Debian or Ubuntu


## Role Variables

| Name                                  | Required/Default         | Description                                                                                                                                               |
|:--------------------------------------|:------------------------:|:----------------------------------------------------------------------------------------------------------------------------------------------------------|
| `php_fpm_pools`                       | :heavy_check_mark:       | List of php-fpm pools to define, see [pools](#pools).                                                                                                     |

### Pools
Each pool is specified by a dict in the `php_fpm_pools` list.

| Name                        | Required/Default                                            | Description                                                                                                                                                            |
|:----------------------------|:-----------------------------------------------------------:|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                      | :heavy_check_mark:                                          | Name of the pool                                                                                                                                                       |
| `version`                   | :heavy_check_mark:                                          | PHP version this pool uses (e.g. `8.3`) |
| `listen`                    | :heavy_check_mark:                                          | The address on which to accept FastCGI requests. Valid syntaxes are: 'ip.add.re.ss:port', 'port', '/path/to/unix/socket'.                                              |
| `user`                      | :heavy_check_mark:                                          | Unix user running the FPM processes.                                                                                                                                   |
| `group`                     | :heavy_multiplication_x:                                    | Unix group running the FPM processes.                                                                                                                                  |
| `pm`                        | :heavy_check_mark:                                          | Choose how the process manager will control the number of child processes. Possible values are `static`, `ondemand`, `dynamic`.                                        |
| `pm_max_children`           | :heavy_multiplication_x: (if `pm` is `static` or `dynamic`) | The number of child processes to be created when pm is set to static and the maximum number of child processes to be created when pm is set to dynamic.                |
| `listen_backlog`            | `{{ php_fpm_listen_backlog }}` (`-1`)                       | Maximum number of requests in the backlog.                                                                                                                             |
| `allowed_clients`           | `{{ php_fpm_listen_allowed_clients }}` (`[any]`)            | List client IP addresses allowed to connect.                                                                                                                           |
| `listen_owner`              | :heavy_multiplication_x:                                    | Owner of the Unix socket, if one is used                                                                                                                               |
| `listen_group`              | :heavy_multiplication_x:                                    | Group of the Unix socket, if one is used                                                                                                                               |
| `listen_mode`               | `{{ php_fpm_listen_mode }}` (`0660`)                        | File mode of the Unix socket, if one is used                                                                                                                           |
| `listen_acl_users`          | :heavy_multiplication_x:                                    | List of ACL user names. If used, `listen_owner` and `listen_group` are ignored.                                                                                        |
| `listen_acl_groups`         | :heavy_multiplication_x:                                    | List of ACL group names. If used, `listen_owner` and `listen_group` are ignored.                                                                                       |
| `pm_start_servers`          | :heavy_multiplication_x:                                    | Number of child processes created on startup                                                                                                                           |
| `pm_min_spare_servers`      | :heavy_multiplication_x:                                    | Minimum number of idle server processes                                                                                                                                |
| `pm_max_spare_servers`      | :heavy_check_mark: (if `pm` is `dynamic`)                   | Maximum number of idle server processes                                                                                                                                |
| `process_idle_timeout`      | `{{ php_fpm_pm_process_idle_timeout }}` (`10s`)             | Number of seconds after which an idle process will be killed. Only used when `pm` is `ondemand`                                                                        |
| `pm_max_requests`           | `{{ php_fpm_pm_max_requests }}` (`0`)                       | The number of requests each child process should execute before respawning.                                                                                            |
| `pm_status_path`            | :heavy_multiplication_x:                                    | The URI to view the FPM status page.                                                                                                                                   |
| `ping_path`                 | :heavy_multiplication_x:                                    | The ping URI to call the monitoring page of FPM (must start with `/`).                                                                                                 |
| `ping_response`             | `{{ php_fpm_ping_response }}` (`pong`)                      | Set the response of a ping request. The response is formatted as text/plain with a 200 response code.                                                                  |
| `processes_priority`        | :heavy_multiplication_x:                                    | Specify the nice priority to apply to the worker process.                                                                                                              |
| `prefix`                    | :heavy_multiplication_x:                                    | Specify prefix for path evaluation.                                                                                                                                    |
| `request_terminate_timeout` | `{{ php_fpm_request_terminate_timeout }}` (`0`)             | The timeout for serving a single request after which the worker process will be killed.                                                                                |
| `request_slowlog_timeout`   | `{{ php_fpm_request_slowlog_timeout }}` (`0`)               | The timeout for serving a single request after which a PHP backtrace will be dumped to the `slowlog` file.                                                             |
| `slowlog`                   | `{{ php_fpm_slowlog }}` (`log/php-fpm.log.slow`)            | The log file for slow requests.                                                                                                                                        |
| `rlimit_files`              | :heavy_multiplication_x:                                    | Set open file descriptor rlimit for child processes in this pool.                                                                                                      |
| `rlimit_core`               | :heavy_multiplication_x:                                    | Set max core size rlimit for child processes in this pool.                                                                                                             |
| `chroot`                    | :heavy_multiplication_x:                                    | Chroot to this directory (absolute path) at the start.                                                                                                                 |
| `chdir`                     | :heavy_multiplication_x:                                    | Chdir to this directory (absolute path) at the start.                                                                                                                  |
| `catch_workers_output`      | `{{ php_fpm_catch_workers_output }}` (`False`)              | Redirect worker stdout and stderr into main error log.                                                                                                                 |
| `clear_env`                 | `{{ php_fpm_clear_env }}` (`True`)                          | Clear environment in FPM workers.                                                                                                                                      |
| `security_limit_extensions` | `php_fpm_security_limit_extensions` (`[.php, .phar]`)       | Limits the extensions of the main script FPM will allow to parse.                                                                                                      |
| `access_log`                | :heavy_multiplication_x:                                    | The access log file.                                                                                                                                                   |
| `access_format`             | `{{ php_fpm_access_format }}` (`%R - %u %t \"%m %r\" %s`)   | The access log format.                                                                                                                                                 |
| `envs`                      | :heavy_multiplication_x:                                    | Dict of environment variables to set.                                                                                                                                  |
| `php_flags`                 | :heavy_multiplication_x:                                    | Dict of `php.ini` directives to set for this pool. The value of a directive should be a boolean.                                                                       |
| `php_admin_flags`           | :heavy_multiplication_x:                                    | Dict of `php.ini` directives to set for this pool. The value of a directive should be a boolean. Unlike `php_flags`, these values cannot be changed using `ini_set()`. |
| `php_values`                | :heavy_multiplication_x:                                    | Dict of PHP values to set.                                                                                                                                             |
| `php_admin_values`          | :heavy_multiplication_x:                                    | Dict of PHP values to set. Unlike `php_values`, these values cannot be changed using `ini_set()`.                                                                      |

Only the `PHP_INI_PERDIR` and `PHP_INI_ALL` [directives](https://secure.php.net/manual/en/ini.list.php) can be set using `php_flags`, `php_admin_flags`, `php_values` or `php_admin_values`.


## Example

```yml
php_fpm_pools:
  - name: fpm-host
    version: 8.1
    listen: /path/to/unix/socket
    user: www-data
    pm: static
    pm_max_children: 20
    pm_start_servers: 20
    processes_priority: -19
    envs:
      TMP: /var/tmp
    php_admin_values:
      open_basedir: /var/www
```


## License

This work is licensed under a [Creative Commons Attribution-ShareAlike 4.0 International License](http://creativecommons.org/licenses/by-sa/4.0/).


## Author Information

 * Original author: [Fritz Otlinghaus (Scriptkiddi)](https://github.com/Scriptkiddi) _fritz.otlinghaus@stuvus.uni-stuttgart.de_
 * Modified by: [Frank Louwers](https://github.com/franklouwers) _frank@kiwazo.be_
