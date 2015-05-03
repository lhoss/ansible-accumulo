# ansible-accumulo
An Ansible role for configuring Apache Accumulo.

## Role Variables

- `accumulo_secret` - Accumulo password (default: `secret`)
- `accumulo_leader_host` - Accumulo hostname (default: `localhost`)
- `accumulo_instance_name` - Accumulo instance name (default: `geowave-accumulo-cluster`)

## Dependencies

Requires role azavea.accumulo

## Example Playbook

    - hosts: servers
      roles:
         - { role: stumptownlabs.accumulo }

## License

Apache2

## Attribution

Adapted from [geotrellis/vagrant.geotrellis](https://github.com/geotrellis/vagrant.geotrellis/tree/master/ansible/roles/geotrellis.accumulo)