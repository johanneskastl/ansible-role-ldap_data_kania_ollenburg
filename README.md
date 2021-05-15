ldap_data_kania_ollenburg
=========

OpenLDAP DIT based on the book by Stefan Kania and Andreas Ollenburg:
[OpenLDAP in der Praxis](https://www.hanser-kundencenter.de/fachbuch/artikel/9783446463875)

The baseDN matches the one from the book, but can easily be overridden to make this work in other demo setups (see below).

Requirements
------------

This role should be run against/on a LDAP server. Tested against OpenLDAP 2.4 on Debian 8/9/10, CentOS 7/8, openSUSE Leap 15.x and SLES15 currently.

The role will install any dependencies that Ansible's `ldap_attr` or `ldap_entry` modules need.

Role Variables
--------------

*Role logic*
This role has several parts, than can be enabled by setting some variables:
- `apply_ACLs`: (default: true) Whether or not to apply the ACLs
- `add_debugging_script_and_ldif_files`: (default: false) Whether debugging script and LDIF files should be created
- `remove_everything`: If you set this, the role will remove *EVERYTHING* it created, this includes files as well as directory entries. Only exception is the baseDN, as that cannot be deleted (easily).

Dependencies
------------

None

Example Playbook
----------------

A simple playbook that will only do the minimum will look like this:
```
- hosts: servers
  roles:
    - { role: 'johanneskastl.ldap_data_kania_ollenburg' }
```

To delete everything this role created, use a playbook like this:
```
- hosts: servers
  roles:
    - role: 'johanneskastl.ldap_data_kania_ollenburg'
      vars:
       remove_everything: 'true'
```

License
-------

BSD-3-Clause

Author Information
------------------

I am Johannes Kastl, reachable via kastl@b1-systems.de.
