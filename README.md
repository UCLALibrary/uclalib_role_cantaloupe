uclalib_role_cantaloupe &nbsp;[![Build Status](https://travis-ci.org/UCLALibrary/uclalib_role_cantaloupe.svg?branch=master)](https://travis-ci.org/UCLALibrary/uclalib_role_cantaloupe)
=========

Ansible role to provision and configure a Cantaloupe IIIF image server

The role supports Cantaloupe 4 and higher

Requirements
------------

This installation of Cantaloupe requires Apache Tomcat installed using the `uclalib_role_tomcat` role.

Role Variables
--------------
Required Settings:
* `cantaloupe_version` - defines the version of Cantaloupe to install on the server (e.g. `4.0.2`)
    * no default set
* `cantaloupe_url` - defines the URL path where Cantaloupe is downloaded
    * default is `https://github.com/medusa-project/cantaloupe/releases/download/v{{ cantaloupe_version }}/cantaloupe-{{ cantaloupe_version }}.zip`
* `cantaloupe_install_path` - defines the filesystem path where the Cantaloupe package is unarchived
    * default is `/opt`
* `cantaloupe_symlink_path` - defines the filesystem symbolic link path for Cantaloupe
    * default is `{{ cantaloupe_install_path }}/cantaloupe`
* `cantaloupe_properties_path` - defines the filesystem path to the `cantaloupe.properties` configuration file
    * default is `{{ cantaloupe_symlink_path }}/cantaloupe.properties`
* `cantaloupe_log_dir` - defines the filesystem path to the Cantaloupe log directory
    * default is `/var/log/cantaloupe`
* `cantaloupe_base_dir` - defines the filesystem path to the Cantaloupe tomcat directory
    * default is `/usr/local/cantaloupe`
* `cantaloupe_tomcat_user` - defines the username the Cantaloupe tomcat process will run as
    * default is `cantaloupe`
* `cantaloupe_min_mem` - defines the minimum heap memory space for the Cantaloupe tomcat process
    * default is `2048m`
* `cantaloupe_max_mem` - defines the maximum heap memory space for the Cantaloupe tomcat process
    * default is `2048m`

Optional Settings:
* `cantaloupe_delegate_script_url` - defines the URL path to obtain a Cantaloupe delegate script
    * no default set
* `cantaloupe_fedora_url` - defines the URL path to a Fedora repository server
    * no default set
* `cantaloupe_fedora_base_path` - defines the base path that identifies a Fedora repository (e.g. `/prod`)
    * no default set

In addition to the above role variables, every configuration parameter in the `cantaloupe.properties` file is a variable.

Reference the following:

* `main.yml` file in the `defaults` directory
* [Cantaloupe User Manual](https://medusa-project.github.io/cantaloupe/manual/4.0/getting-started.html)

to determine what options are available to customize.

Cantaloupe Download URL Note
----------------------------

The default value for the `cantaloupe_url` variable is:

`https://github.com/medusa-project/cantaloupe/releases/download/v{{ cantaloupe_version }}/cantaloupe-{{ cantaloupe_version }}.zip`

If you are affiliated with UCLA, you have the option of overriding this default url value with:

`http://pkgs.library.ucla.edu/cantaloupe/cantaloupe-{{ cantaloupe_version }}.zip`

Versions of Cantaloupe available via the UCLA URL are:

* `4.0.2`

Tags
------------

The following tags are available to run specific task sets:
* `cantaloupe-install` - download/install cantaloupe and create filesystem structure
* `cantaloupe-setup` - set-up of Apache Tomcat environment variables and HTTPD virtual host files
* `cantaloupe-config` - puts in place cantaloupe configuration file
* `cantaloupe-delegate` - puts in place cantaloupe delegate script (only if enabled)

Dependencies
------------

The following roles must be run on the server prior to executing:

```
uclalib_role_rhel7repos
uclalib_role_epel
uclalib_role_uclalibrepo
uclalib_role_java
uclalib_role_apache
uclalib_role_tomcat
```

Example Variables Definition
-----------------------------------------------
The following is an example of what you could include in a host_vars file to use this role:

```
---

tomcat_major_version: 8
tomcat_version: 8.5.34
tomcat_applications:
  - app_name: cantaloupe
    shut_port: 8008
    conn_port: 8081
    rproxy_path: cantaloupe

cantaloupe_version: "4.0.2"

cantaloupe_delegate_script_url: "https://server.example.com/delegate.rb"

cantaloupe_log_dir: "/var/log/cantaloupe"

cantaloupe_fedora_url: "http://fedora.fedoraserver.com/fcrepo/rest"
cantaloupe_fedora_base_path: "/prod"

### Begin cantaloupe.properties variable definitions

cantaloupe_delegate_script_enabled: "true"

cantaloupe_temp_pathname: "/var/cantaloupe/tmp"

cantaloupe_endpoint_admin_enabled: "true"
cantaloupe_endpoint_admin_username: "username"
cantaloupe_endpoint_admin_secret: "password"

cantaloupe_source_static: "HttpSource"
cantaloupe_source_delegate: "true"

cantaloupe_FilesystemSource_lookup_strategy: "ScriptLookupStrategy"

cantaloupe_HttpSource_lookup_strategy: "ScriptLookupStrategy"

cantaloupe_processor_fallback: "JaiProcessor"

cantaloupe_cache_server_source: "FilesystemCache"
cantaloupe_cache_server_source_ttl_seconds: "86400"
cantaloupe_cache_server_derivative_enabled: "true"
cantaloupe_cache_server_derivative: "FilesystemCache"
cantaloupe_cache_server_derivative_ttl_seconds: "86400"
cantaloupe_cache_server_worker_enabled: "true"
cantaloupe_cache_server_worker_interval: "43200"
cantaloupe_FilesystemCache_pathname: "/var/cantaloupe/cache"

cantaloupe_log_application_RollingFileAppender_enabled: "true"
cantaloupe_log_application_RollingFileAppender_pathname: "{{ cantaloupe_log_dir }}/application.log"
cantaloupe_log_application_RollingFileAppender_policy: "TimeBasedRollingPolicy"
cantaloupe_log_application_RollingFileAppender_TimeBasedRollingPolicy_filename_pattern: "{{ cantaloupe_log_dir }}/application-%d{yyyy-MM-dd}.log"
cantaloupe_log_application_RollingFileAppender_TimeBasedRollingPolicy_max_history: "30"

cantaloupe_log_error_RollingFileAppender_enabled: "true"
cantaloupe_log_error_RollingFileAppender_pathname: "{{ cantaloupe_log_dir }}/error.log"
cantaloupe_log_error_RollingFileAppender_policy: "TimeBasedRollingPolicy"
cantaloupe_log_error_RollingFileAppender_TimeBasedRollingPolicy_filename_pattern: "{{ cantaloupe_log_dir }}/error-%d{yyyy-MM-dd}.log"
cantaloupe_log_error_RollingFileAppender_TimeBasedRollingPolicy_max_history: "30"

### End cantaloupe.properties variable definitions
```

Example Playbook
----------------
```
---

- name: uclalib_cantaloupeiiif.yml
  become: yes
  become_method: sudo
  hosts: all
  user: ansible

  roles:
    - { role: uclalib_role_rhel7repos }
    - { role: uclalib_role_epel }
    - { role: uclalib_role_uclalibrepo }
    - { role: uclalib_role_java, oracle_java_version: '1.8.0_191' }
    - { role: uclalib_role_apache }
    - { role: uclalib_role_tomcat }
    - { role: uclalib_role_cantaloupe }
```
