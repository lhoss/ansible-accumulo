# ansible-accumulo
An Ansible role for configuring Apache Accumulo.


## Role Variables

- `accumulo_secret` - Accumulo password (default: `secret`)
- `accumulo_leader_host` - Accumulo hostname (default: `localhost`)
- `accumulo_instance_name` - Accumulo instance name (default: `geowave-accumulo-cluster`)

## Dependencies

Requires roles
- azavea.build-essential - provides setup of a minimal build environment to build the native libs
- azavea.java - a basic role to setup JDK-7 (no support for JDK-8 though). Note: JDK-7 is minimal requirement

## Example Playbook

    - hosts: servers
      roles:
         - { role: lhoss.accumulo }

## License
Apache2

## Attribution
Adapted from [geotrellis/vagrant.geotrellis](https://github.com/geotrellis/vagrant.geotrellis/tree/master/ansible/roles/geotrellis.accumulo)


