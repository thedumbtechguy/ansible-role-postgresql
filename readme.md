# Ansible Role: PostgreSQL

An Ansible role for installing and managing PostgreSQL.

## Requirements

This role has been tested on `Ubuntu 16` only.

## Variables

### General

- `postgresql_version` - PostgreSQL version
  - Default: `9.6`
- `postgresql_listen_addresses` - address for PostgreSQL to bind to
  - Default: `localhost`
- `postgresql_port` - port for PostgreSQL to bind to
  - Default: `5432`
- `postgresql_data_directory` - postgreSQL data directory
  - Default: `/var/lib/postgresql/{{ postgresql_version }}/main`
- `postgresql_max_connections` - maximum number of connections
  - Default: `100`
- `postgresql_shared_buffers` - memory for shared buffers. tuning overrides this value.
  - Default: `128MB`
- `postgresql_work_mem` - memory for worker processes. tuning overrides this value.
  - Default: `4MB`
- `postgresql_log_autovacuum_min_duration` - minimum duration for logging long automatic vacuuming
  - Default: `-1`
- `postgresql_log_min_duration_statement` - minimum duration for logging long queries
  - Default: `-1`
- `postgresql_hba_mapping` - a mapping of PostgreSQL host-based authentication rules
  - Default: `[]`

- `postgresql_locale` - default locale to use
  - Default: `en_GB.UTF-8`
- `postgresql_additional_locales` - additional locales to install. Useful for adding separate databases with different locales.
  - Default: `['en_US.UTF-8']`


### Tuning

- `postgresql_tune_db` - should we run pgtune
  - Default: `yes`
- `postgresql_tune_db_type` - the workload type of the database
  - Default: `mixed`
  - Options:
    - `web`: web applications
    - `oltp`: online transactions processing systems
    - `dw`: data warehouses
    - `desktop`: desktop applications
    - `mixed`: mixed type of applications
- `postgresql_tune_total_memory` - total memory usable for PostgreSQL on server
    - Default: `{{ ansible_memtotal_mb }}MB`
- `postgresql_tune_total_memory_percentage` - percentage of total memory to allocate to PostgreSQL
    - Default: `100`


### Replication

- `postgresql_wal_level` - wal level. if PgLogical is enabled, `logical` is used.
  - Default: `minimal`
- `postgresql_max_worker_processes` - provider node requires one per database being replicated. Subscriber node requires one per provider node.
  - Default: `8`
- `postgresql_max_replication_slots` - provider node requires one per subscriber node.
  - Default: `0`
- `postgresql_max_wal_senders` - provider node requires one per subscriber node.
  - Default: `0`

#### PgLogical

- `postgresql_pglogical` - install and configure pglogical.
  - Default: `no`

### Databases

- `postgresql_databases` - list of databases to manage
  - Default: `[]`
  - Example
    ```yaml
    postgresql_databases:
      - name: exampledb # required; the rest are optional
        lc_collate: # defaults to '{{ postgresql_locale }}'
        lc_ctype: # defaults to '{{ postgresql_locale }}'
        encoding: # defaults to 'UTF-8'
        template: # defaults to 'template0'
        login_host: # defaults to 'localhost'
        login_password: # defaults to not set
        login_user: # defaults to 'postgres'
        port: # defaults to not set
        state: # defaults to 'present'. can be 'absent'
    ```

### Users

- `postgresql_users` - list of databases to manage
  - Default: `[]`
  - Example
    ```yaml
    postgresql_users:
      - name: jdoe #required; the rest are optional
        password: # defaults to not set
        priv: # defaults to not set
        role_attr_flags: # defaults to not set
        db: # defaults to not set
        login_host: # defaults to 'localhost'
        login_password: # defaults to not set
        login_user: # defaults to 'postgres'
        port: # defaults to not set
        state: # defaults to 'present'. can be 'absent'
    ```

## Usage Example

```yaml
- hosts: all
  vars:
    postgresql_listen_addresses: "*"
    postgresql_hba_mapping:
      - { type: "host", database: "all", user: "all", address: "0.0.0.0/0", method: "md5" }
      - { type: "host", database: "all", user: "all", address: "10.0.2.0/24", method: "md5" }
    postgresql_users:
      - name: jdoe
        state: present
    postgresql_databases:
      - name: test
        state: present
    postgresql_tune_total_memory_percentage: 50
  roles:
    - setup_postgresql
```



## License

MIT / BSD

## Author Information

This role was created by [Stefan Froelich](https://thedumbtechguy.blogspot.com/).

## Credits

This role was built upon the original work of:

- [azavea/ansible-postgresql](https://github.com/azavea/ansible-postgresql)
- [geerlingguy/ansible-role-postgresql](https://github.com/geerlingguy/ansible-role-postgresql)
- [imincik/ansible-postgresql_tune](https://github.com/imincik/ansible-postgresql_tune)
- [gilesw/ansible-postgresql_tune](https://github.com/gilesw/ansible-postgresql_tune/network)
