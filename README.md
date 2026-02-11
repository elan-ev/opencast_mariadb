# Ansible Role to Install MariaDB for Opencast

![lint](https://github.com/elan-ev/opencast_mariadb/actions/workflows/lint.yml/badge.svg)
![molecule](https://github.com/elan-ev/opencast_mariadb/actions/workflows/molecule.yml/badge.svg)

Install MariaDB for [Opencast](https://opencast.org/) with [ansible](https://docs.ansible.com/).

## Dependencies

This role requires the MySQL community collection:

```
ansible-galaxy collection install community.mysql
```

## Role Variables

You have to specify passwords for the opencast user that accesses the database (`database_password`)
and the database root user (`database_root_password`).
Note that once you set the password for the root user, you can _not_ change it again with this role.

Further config options are optional.
Have a look at the [defaults](defaults/main.yml) to see all variables.

## Example Playbook

Just add the role to your playbook:

```yaml
- hosts: all
  become: true
  roles:
    - role: elan.opencast_mariadb
      database_password: '1234'
      database_root_password: '4567'
```

## Development

For development and testing you can use [molecule](https://molecule.readthedocs.io/en/latest/).
With podman as driver you can install it like this – preferably in a virtual environment (if you use docker, substitute `podman` with `docker`):

```bash
pip install -r .dev_requirements.txt
```

Then you can *create* the test instances, apply the ansible config (*converge*) and *destroy* the test instances with these commands:

```bash
molecule create
molecule converge
molecule destroy
```

If you want to inspect a running test instance use `molecule login --host <instance_name>`, where you replace `<instance_name>` with the desired value.

To test the role run `molecule test`.

## Database Backups (optional)

This role can (optionally) install a small backup setup using:
- a backup script placed in `database_backup_output_path`
- a `database-backup.service` + `database-backup.timer` (systemd)

When enabled, backups are written as compressed dumps:
`db-backup-<db>-<timestamp>.sql.gz`

### Backup variables

- `database_backup_enabled` (default: `false`) - Enable/disable the backup feature.
- `database_backup_output_path` (default: empty) - **Required when** `database_backup_enabled: true`.Directory where the scripts and backup files are stored (e.g. `/var/backups/mariadb`).
- `database_backup_schedule` (default: `*-*-* 05:00:00`) - systemd `OnCalendar` schedule for the timer.
- `database_backup_keep` (default: `7`)
  - Number of newest backups to keep **per database**. Older dumps are removed.
- `database_backup_dbs`- List of database names to back up (e.g. `["opencast"]`).
- `database_backup_owner` / `database_backup_group`- OS user/group that owns `database_backup_output_path` and the scripts.
- `database_backup_user` / `database_backup_user_password`- MariaDB user/password used by the backup script.

### Restore script

A restore script is installed into the same output directory (next to the backup script).
It is meant to be used manually when you really want to restore a dump.

Usage:
```bash
<database_backup_output_path>/database-restore.sh <database_name> <backup_file>
```
## License

[BSD-3-Clause](LICENSE)

## Author Information

[ELAN e.V](https://elan-ev.de/)
