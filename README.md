# Ansible Role to Install MariaDB for Opencast

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

## License

[BSD-3-Clause](LICENSE)

## Author Information

[ELAN e.V](https://elan-ev.de/)
