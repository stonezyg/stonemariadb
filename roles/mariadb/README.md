# Ansible Role: MariaDB

An Ansible Role that installs MariaDB on RHEL/CentOS.

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):


    mariadb_user_home: /root

The home directory inside which Python MySQL settings will be stored, which Ansible will use when connecting to MariaDB. This should be the home directory of the user which runs this Ansible role.

    mariadb_root_password: root

The MariaDB root user account password.

    mariadb_root_password_update: no

Whether to force update the MariaDB root user's password. By default, this role will only change the root user's password when MariaDB is first configured. You can force an update by setting this to `yes`.

> Note: If you get an error like `ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)` after a failed or interrupted playbook run, this usually means the root password wasn't originally updated to begin with. Try either removing  the `.my.cnf` file inside the configured `mariadb_user_home` or updating it and setting `password=''` (the insecure default password). Run the playbook again, with `mariadb_root_password_update` set to `yes`, and the setup should complete.

    mariadb_enabled_on_startup: yes

Whether MariaDB should be enabled on startup.

    overwrite_global_mycnf: yes

Whether the global my.cnf should be overwritten each time this role is run. Setting this to `no` tells Ansible to only create the `my.cnf` file if it doesn't exist. This should be left at its default value (`yes`) if you'd like to use this role's variables to configure MariaDB.

    mariadb_config_include_files: []

A list of files that should override the default global my.cnf. Each item in the array requires a "src" parameter which is a path to a file. An optional "force" parameter can force the file to be updated each time ansible runs.

    mariadb_databases: []

The MariaDB databases to create. A database has the values `name`, `encoding` (defaults to `utf8`) and `collation` (defaults to `utf8_general_ci`). The formats of these are the same as in the `mariadb_db` module.

    mariadb_users: []

The MariaDB users and their privileges. A user has the values `name`, `host` (defaults to `localhost`), `password`, `priv` (defaults to `*.*:USAGE`), `append_privs` (defaults to `no`),  `state`  (defaults to `present`). The formats of these are the same as in the `mariadb_user` module.

    mariadb_port: "3306"
    mariadb_bind_address: '0.0.0.0'
    mariadb_datadir: /var/lib/mysql
    mariadb_socket: *default value depends on OS*
    mariadb_pid_file: *default value depends on OS*

Default MariaDB connection configuration.

    mariadb_log: ""
    mariadb_log_error: *default value depends on OS*
    mariadb_syslog_tag: *default value depends on OS*

MariaDB logging configuration. Setting `mariadb_log` (the general query log) or `mariadb_log_error` to `syslog` will make MariaDB log to syslog using the `mariadb_syslog_tag`.

    mariadb_slow_query_log_enabled: no
    mariadb_slow_query_log_file: *default value depends on OS*
    mariadb_slow_query_time: 2

Slow query log settings. Note that the log file will be created by this role, but if you're running on a server with SELinux or AppArmor, you may need to add this path to the allowed paths for MariaDB, or disable the mysql profile. For example, on Debian/Ubuntu, you can run `sudo ln -s /etc/apparmor.d/usr.sbin.mysqld /etc/apparmor.d/disable/usr.sbin.mysqld && sudo service apparmor restart`.

    mariadb_key_buffer_size: "256M"
    mariadb_max_allowed_packet: "64M"
    mariadb_table_open_cache: "256"
    [...]

The rest of the settings in `defaults/main.yml` control MariaDB's memory usage and some other common settings. The default values are tuned for a server where MariaDB can consume ~512 MB RAM, so you should consider adjusting them to suit your particular server better.

## Dependencies

- Role: `damienlagae.base`

## Example Playbook

    - hosts: database
      become: yes
      vars_files:
        - vars/main.yml
      roles:
        - damienlagae.mariadb

*Inside `vars/main.yml`*:

    mariadb_root_password: super-secure-password
    mariadb_databases:
      - name: example_db
        encoding: latin1
        collation: latin1_general_ci
    mariadb_users:
      - name: example_user
        host: "%"
        password: similarly-secure-password
        priv: "example_db.*:ALL"

## License

MIT / BSD

## Author Information

This role was created in 2016 by [Damien Lagae](mailto:damienlagae@gmail.com).