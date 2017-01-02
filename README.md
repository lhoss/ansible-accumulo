# ansible-accumulo
An Ansible role for installing and configuring Apache Accumulo.


## Role Variables

Variables requiring customization:

- `accumulo_secret` - Accumulo password (default: `secret`)
- `accumulo_instance_name` - Accumulo instance name (default: `my-accumulo-cluster`)
- `accumulo_leaders` - list variable for the accumulo masters (default: the `inventory_host`)
- `accumulo_slaves` -  list variable for the accumulo slaves  (default: the `inventory_host`)
- `accumulo_tracers` - list variable for the accumulo tracers (default: the `inventory_host`)

Variables to be set only for initial setup:
- `accumulo_initialize` - if enabled (default: `false`) accumulo is initialized with initial HDFS dir (set in `accumulo_instance_initial_dir`) but only if the dir does not yes exist

Variables whose defaults require either customization or existing inventory groups `accumulo_master`,`accumulo_slaves`:

- `accumulo_leader` - if `true` the deployment ensures all master services are setup and started.
                      (By default the variable is set `true` for nodes in `accumulo_leaders`)
- `accumulo_slave` -  if `true` the deployment ensures the 'tablet server' is setup and started.
                      (By default the variable is set `true` for nodes in `accumulo_slaves`)

Variables with sane defaults: 

- `accumulo_version` - the version (default: `1.7.2`). Tested with v1.7.1, v1.7.2, v1.8.0
- `accumulo_conf_dir` - the conf dir (symlink)
- `accumulo_log_dir` - the log dir
- `accumulo_user_dir` - the accumulo user 'home' dir
- `accumulo_home` - the main install dir (equivalent to `ACCUMULO_HOME` ENV var)
- `accumulo_parent_dir` - the parent dir of `accumulo_home`

- `accumulo_service_handlers_enabled ` - if enabled (default: `true`) accumulo services are restarted on any config change; if disabled services aren't (re)started
- `accumulo_instance_initial_dir` - the initial hdfs dir  (default: `hdfs:///accumulo`)
- `accumulo_instance_volumes` - the `instance.volumes` (default: `hdfs:///accumulo`)
- `accumulo_trace_user` - the `trace.user` (default: `root`)
- `accumulo_trace_password` - the `trace.token.property.password` (default: same as `accumulo_secret`)
- `accumulo_user` - the accumulo user (default: `accumulo` )
- `accumulo_group` - the accumulo group (default: `accumulo` ). Note: if `accumulo_group != accumulo_user`, you must ensure that group exists.
- `accumulo_user_shell` - the user's shell (default (`/bin/false` )


Settings depending on external dependencies (Zookeeper, HDFS, Java):

- `accumulo_zookeeper_servers` - A list of comma-separated Zookeeper hosts (default: `localhost:2181`)
- `hdfs_root_user` - (default: `hdfs`)

- `accumulo_zookeeper_home` - default setting for ZOOKEEPER_HOME (iff no existing ENV var set)
- `accumulo_hadoop_home` - default setting for HADOOP_HOME (iff no existing ENV var set)
- `accumulo_java_home` - default setting for JAVA_HOME (iff no existing ENV var set)


Variables to set the JVM Memory options of the accumulo services:

- `accumulo_opts_tserver` - "-Xmx1g -Xms1g -XX:NewSize=500m -XX:MaxNewSize=500m"
- `accumulo_opts_master`  - "-Xmx1g -Xms1g"
- `accumulo_opts_monitor` - "-Xmx1g -Xms256m"
- `accumulo_opts_gc:`     - "-Xmx256m -Xms256m"
- `accumulo_opts_general` - "-XX:+UseConcMarkSweepGC -XX:CMSInitiatingOccupancyFraction=75 -Djava.net.preferIPv4Stack=true"
- `accumulo_opts_other`   -  "-Xmx1g -Xms256m"

Variables to customize the accumulo log4j based logging:

- `accumulo_debug_log_enabled` - if enabled (default: `true`) write separate log files containing all logs down to DEBUG log level.
- `accumulo_log_minLevel` - the minimum log level for the normal log files (default ´INFO´). Note: there is an extra set of logs configured that include `DEBUG` logs.
- `accumulo_log_pattern` - the log4j patternLayout


## Dependencies

Required role:

- JasonGiedymin.java - role used (in Vagrant example) to setup JDK-8. Note: JDK-7 is good enough for accumulo.
  I decided to not 'hard'depend (in meta/main.yml) on any Java role, so the user can choose how to setup the JDK.
   

## Example Playbook
Assuming inventory groups named `accumulo_master`, `accumulo_slaves` and `accumulo_tracers`, and following 3 variables:

    accumulo_leaders: "{{groups.accumulo_master}}"
    accumulo_slaves: "{{groups.accumulo_slaves}}"
    accumulo_tracers: "{{groups.accumulo_tracers}}"
 
a reasonable playbook is:
    
    - hosts: accumulo_master:accumulo_slaves
      sudo: true
      roles:
         - { role: lhoss.accumulo }


## Known Issues
* The role can hang in the main 'initialize' task, because 'accumulo init' may prompt for user input in some cases (for ex. when zookeeper contains accumulo meta-data, but the hdfs volumes weren't initially yet). Obviously this will not happen on a clean install.

## License
Apache2

## Attribution
Inspired from 2 open source roles:

* [stumptownlabs.accumulo](https://github.com/stumptownlabs/ansible-accumulo)
* [azavea.accumulo](https://github.com/azavea/ansible-accumulo)
