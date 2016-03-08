# ansible-accumulo
An Ansible role for installing and configuring Apache Accumulo.


## Role Variables

Variables requiring customization:

- `accumulo_secret` - Accumulo password (default: `secret`)
- `accumulo_instance_name` - Accumulo instance name (default: `geowave-accumulo-cluster`)

Variables whose defaults require the 'inventory group' `accumulo_master`:

- `accumulo_leader_host` - by default set to `{{groups.accumulo_master[0]}}`

- `accumulo_leader` - if `true` the deployment ensures all required 'Leader' services are started. 
                      (By default set according to the inventory groups, see below: 
                       `{{inventory_hostname in groups.accumulo_master}}`

Variables with sane defaults: 

- `accumulo_version` - the version (default: `1.7.1`). If >v1.6.3 accumulo is installed from the official bin.tar.gz (from local `./files` dir).
- `accumulo_mirror` - mirror used for (used for <v1.6.3 'deb' pkg based install)
- `accumulo_conf_dir` - the conf dir (symlink)
- `accumulo_log_dir` - the log dir
- `accumulo_user_dir` - the accumulo user 'home' dir
- `accumulo_home` - the main install dir (equivalent to `ACCUMULO_HOME` ENV var)
- `accumulo_parent_dir` - the parent dir of `accumulo_home`

- `accumulo_initialize` - (default: `true`) If enabled, accumulo HDFS is initialized (iff  `hdfs:///accumulo/` does not exist)
- `accumulo_trace_user` - the `trace.user` (default: `root`)
- `accumulo_trace_password` - the `trace.token.property.password` (default: same as `accumulo_secret`)
- `accumulo_user` - the accumulo user (default: `accumulo` )
- `accumulo_group` - the accumulo group (default: `accumulo` ). Note: if `accumulo_group != accumulo_user`, you must ensure that group exists.
- `accumulo_user_shell` - the user's shell (default (`/bin/false` )
- `accumulo_cron_failover` - (default: `false`). If `true` an experimental cron-scripts based failover mechanism is installed (originally taken from `stumptownlabs.accumulo` role)


Settings depending on external dependencies (Zookeeper, HDFS, Java):

- `zookeeper_servers` - (default: `{ ip: "{{accumulo_leader_host}}" }´
- `hdfs_root_user` - (default: `hdfs`)

- `accumulo_zookeeper_home` - the ZOOKEEPER_HOME used if not found in the system's ENV
- `accumulo_hadoop_home` - the HADOOP_HOME used if not found in the system's ENV
- `accumulo_java_home` - the JAVA_HOME used if not found in the system's ENV


Variables to set the JVM Memory options of the accumulo services:

- `accumulo_opts_tserver` - "-Xmx1g -Xms1g -XX:NewSize=500m -XX:MaxNewSize=500m"
- `accumulo_opts_master`  - "-Xmx1g -Xms1g"
- `accumulo_opts_monitor` - "-Xmx1g -Xms256m"
- `accumulo_opts_gc:`     - "-Xmx256m -Xms256m"
- `accumulo_opts_general` - "-XX:+UseConcMarkSweepGC -XX:CMSInitiatingOccupancyFraction=75 -Djava.net.preferIPv4Stack=true"
- `accumulo_opts_other`   -  "-Xmx1g -Xms256m"



## Dependencies

Requires roles:

- azavea.build-essential - provides setup of a minimal build environment to build the native libs
- azavea.java - (optional role) to setup JDK-7 (no support for JDK-8 though). Note: JDK-7 is minimal requirement

Requires the accumulo binaries archive (except for < v1.6.2) present in the local ./files dir. Example for v1.7.1: `files/accumulo-1.7.1-bin.tar.gz`

## Example Playbook
The role defaults sets variables `accumulo_leader`, `accumulo_leader_host` 
automatically for each host, based on the inventory group `accumulo_master`.
Assuming the slave nodes are in a group `accumulo_slaves`, a reasonable playbook is:
    
    - hosts: accumulo_master:accumulo_slaves
      sudo: true
      roles:
         - { role: lhoss.accumulo }


## License
Apache2

## Attribution
Inspired from 2 open source roles:

* [stumptownlabs.accumulo](https://github.com/stumptownlabs/ansible-accumulo)
* [azavea.accumulo](https://github.com/azavea/ansible-accumulo)
