uclalib_role_cantaloupe &nbsp;[![Build Status](https://travis-ci.org/UCLALibrary/uclalib_role_cantaloupe.svg?branch=master)](https://travis-ci.org/UCLALibrary/uclalib_role_cantaloupe)
=========

Ansible role to provision and configure a Cantaloupe IIIF image server

Requirements
------------

This installation of Cantaloupe requires Apache Tomcat installed using the `uclalib_role_tomcat` role.

Role Variables
--------------
Required Settings:
* `cantaloupe_version` - defines the version of Cantaloupe to install on the server
* `cantaloupe_url` - defines the URL path where Cantaloupe is downloaded
* `cantaloupe_install_path` - defines the filesystem path where Cantaloupe is installed
* `cantaloupe_symlink_path` - defines the filesystem symbolic link path for Cantaloupe
* `cantaloupe_properties_path` - defines the filesystem path to the `cantaloupe.properties` configuration file
* `cantaloupe_log_dir` - defines the filesystem path to the Cantaloupe log directory
* `cantaloupe_base_dir` - defines the filesystem path to the Cantaloupe tomcat directory
* `cantaloupe_tomcat_user` - defines the username the Cantaloupe tomcat process will run as
* `cantaloupe_min_mem` - defines the minimum heap memory space for the Cantaloupe tomcat process
* `cantaloupe_max_mem` - defines the maximum heap memory space for the Cantaloupe tomcat process

Optional Settings:
* `cantaloupe_delegate_script_url` - defines the URL path to obtain a Cantaloupe delegate script
* `cantaloupe_fedora_url` - defines the URL path to a Fedora repository server
* `cantaloupe_fedora_base_path` - defines the base path that identifies a Fedora repository (e.g. `/prod`)

In addition to the above role variables, every configuration parameter in the `cantaloupe.properties` file is a variable.

Reference the following:

* `main.yml` file in the `defaults` directory
* [Cantaloupe User Manual](https://medusa-project.github.io/cantaloupe/manual/4.0/getting-started.html)

to determine what options are available to customize.


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
