![Ansible Lint](https://github.com/johanneskastl/ansible-role-ldap_data_kania_ollenburg/workflows/Ansible%20Lint/badge.svg)

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

*General usage*
This role uses the rootDN of both the actual database and the one for cn=config to create or delete objects, to obviously you need to supply the following information:

- `rootdn_password`: (required) the password for the database's rootDN
- `rootdn_name`: the name of the database's rootDN, default is `cn=admin`
- `rootdn_cn_config`: the name of the rootDN used for `cn=config`, default is `cn=admin,cn=config`
- `rootdn_cn_config_password`: the password for the rootDN used for `cn=config`, default is to use the same as for the database's rootDN

*Role logic*
This role has several parts, than can be enabled by setting some variables:
- `apply_ACLs`: (default: true) Whether or not to apply the ACLs
- `add_debugging_script_and_ldif_files`: (default: false) Whether debugging script and LDIF files should be created
- `remove_everything`: If you set this, the role will remove *EVERYTHING* it created, this includes files as well as directory entries. Only exception is the baseDN, as that cannot be deleted (easily).

Please note:
Due to a UID mismatch the inital testing users from chapter 3.5 will only be deployed if `add_initial_testing_users` is set to true. This will prevent the creation of the users from chapter 7.1 used for ACL testing.

*Naming details*

- `base_dn`: the desired baseDN, default value is `dc=example,dc=net`

*Password Hashes*
- `sssd_user_password_hash`: (required) hashed password for the sssd-user user
- `chapter_7_1_password_hash`: (required) hashed password for the users in chapter 7.1
- `chapter_3_5_password_hash`: (optional) hashed password for the users in chapther 3.5 (that only get created if `add_initial_testing_users` is set to true by the user)

*Cleartext passwords*
In case you set the `add_debugging_script_and_ldif_files` variable, this role will create a debugging script and lots of LDIF files to help with debugging. You can then delete and manually create users, groups or set/delete the ACLs.

The debugging script needs the *cleartext* passwords of the users. This should obviously *not be used on productive servers with real passwords*!

- `rootdn_password`: cleartext password of the database's rootDN
- `sssd_user_password`: cleartext password of the sssd-user
- `chapter_7_1_password`: cleartext password of the regular users from chapter 7.1

Dependencies
------------

None

Example Playbook
----------------

A simple playbook that will only do the minimum will look like this:
```
- hosts: servers
  roles:
    - role: 'johanneskastl.ldap_data_kania_ollenburg'
```

To delete everything this role created, use a playbook like this:
```
- hosts: servers
  roles:
    - role: 'johanneskastl.ldap_data_kania_ollenburg'
      vars:
       remove_everything: 'true'
```

A playbook that will not only create the groups and users in LDAP but also create the debugging script and LDIF files will be a little more work:
```
- hosts: servers
  roles:
    - role: 'johanneskastl.ldap_data_kania_ollenburg'
      # create ACL debugging script
      add_debugging_script_and_ldif_files: 'true'
      rootdn_name: 'cn=Manager'
      # Password Hashes
      sssd_user_password_hash: '{SSHA}1234567890abcdef'
      chapter_7_1_password_hash: '{SSHA}567890abcdef1234'
      # Passwords
      rootdn_password: 'totallysupersecret'
      sssd_user_password: 'shouldalsobesecret'
      chapter_7_1_password: 'secret'
```

License
-------

BSD-3-Clause

Author Information
------------------

I am Johannes Kastl, reachable via kastl@b1-systems.de.
