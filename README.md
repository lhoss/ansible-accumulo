# ansible-accumulo
An Ansible role for installing and configuring Apache Accumulo.


## Role Variables

Variables requiring customization:

- `accumulo_secret` - Accumulo password (default: `secret`)
- `accumulo_instance_name` - Accumulo instance name (default: `geowave-accumulo-cluster`)

Variables whose defaults require either cusomization or existing inventory groups `accumulo_master`,`accumulo_slaves`:

- `accumulo_leaders` - list variable for the accumulo masters (default: inventory group `accumulo_master`)
- `accumulo_slaves` - list variable for the accumulo slaves (default: inventory group `accumulo_slaves`)
- `accumulo_leader` - if `true` the deployment ensures all master services are setup and started.
                      (By default the variable is set `true` for nodes in `accumulo_leaders`)
- `accumulo_slave` -  if `true` the deployment ensures the 'tablet server' is setup and started.
                      (By default the variable is set `true` for nodes in `accumulo_slaves`)

Variables with sane defaults: 

- `accumulo_version` - the version (default: `1.7.1`). If >v1.6.3 accumulo is installed from the official bin.tar.gz (from local `./files` dir).
- `accumulo_conf_dir` - the conf dir (symlink)
- `accumulo_log_dir` - the log dir
- `accumulo_user_dir` - the accumulo user 'home' dir
- `accumulo_home` - the main install dir (equivalent to `ACCUMULO_HOME` ENV var)
- `accumulo_parent_dir` - the parent dir of `accumulo_home`

- `accumulo_initialize` - If enabled (default: `true`), accumulo HDFS is initialized using the `hdfs:///accumulo/` dir (but only iff the dir does not yes exist)
- `accumulo_instance_volumes` - the `instance.volumes` (default: `hdfs:///accumulo`)
- `accumulo_trace_user` - the `trace.user` (default: `root`)
- `accumulo_trace_password` - the `trace.token.property.password` (default: same as `accumulo_secret`)
- `accumulo_user` - the accumulo user (default: `accumulo` )
- `accumulo_group` - the accumulo group (default: `accumulo` ). Note: if `accumulo_group != accumulo_user`, you must ensure that group exists.
- `accumulo_user_shell` - the user's shell (default (`/bin/false` )


Settings depending on external dependencies (Zookeeper, HDFS, Java):

- `accumulo_zookeeper_servers` - A list of comma-separated Zookeeper hosts (default: `localhost:2181`)
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

Variables to customize the accumulo log4j based logging:

- `accumulo_log_minLevel` - the minimum log level for the normal log files (default ´INFO´). Note: there is an extra set of logs configured that include `DEBUG` logs.
- `accumulo_log_pattern` - the log4j patternLayout


## Dependencies

Requires roles:

- azavea.build-essential - provides setup of a minimal build environment to build the native libs
- azavea.java - (optional role) to setup JDK-7 (no support for JDK-8 though). Note: JDK-7 is minimal requirement

Requires the accumulo binaries archive (except for < v1.6.2) present in the local ./files dir. Example for v1.7.1: `files/accumulo-1.7.1-bin.tar.gz`

## Example Playbook
The role defaults sets variables `accumulo_leaders`, `accumulo_slaves` based on 2 inventory groups.
Assuming inventory groups named `accumulo_master` and `accumulo_slaves`, a reasonable playbook is:
    
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
