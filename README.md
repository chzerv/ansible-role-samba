# Ansible Role: Samba
=========

This role installs and configures Samba (SMB) as a file server on Debian-based, RedHat-based and Archlinux systems.

Requirements
------------

None.

Role Variables
--------------

+ `samba_workgroup`: The workgroup which is -or will be- used in your network.
+ `samba_server_string`: A description for the server. Can be anything you like.
+ `samba_server_role`: Defines in which mode Samba will operate. Possible values are:
  - `standalone server` [Default]
  - `member server`
  - `classic primary domain controller`
  - `classic backup domain controller`
  - `active directory domain controller`
+ `samba_realm`: Specifies the Kerberos or Active Directory (AD) realm the host is part of.

+ `samba_hosts_allow`: A space separated list of hosts which are allowed to access the service.
+ `samba_server_min_protocol`: The minimum protocol version that clients are allowed to use.
+ `samba_server_min_protocol` The maximum protocol version that is supported by the server.
+ `samba_interfaces`: A space separated list of additional interfaces to be used by Samba. If you have multiple network interfaces, they must be listed here. The interfaces can be given as:
  - An IP/mask pair,
  - a broadcast/mask pair,
  - an IP address or
  - the interface's name (e.g., eth0)

+ `samba_security`: Controls how clients authenticate to Samba. Possible values are:
  - `user` [Default]: Users can login using username and password.
  - `domain` Users will be validated against a Windows NT Primary or or Backup Domain Controller.
  - `ads`: Samba will act as a domain member in an ADS realm.
+ `samba_passdb_backend`: Specifies the backend to be used for storing user information. Possible values are:
  - `smbpasswd`: Information are stored in plaintext.
  - `tdbsam` [Default]: TDP based password storage. One can also provide a path to where the file will be saved, e.g., `samba_passdb_backend: tdbsam:/etc/samba/passdb.tdb`
  - `ldapsam`: LDAP based password storage. One can also provide an LDAP URL to connect to, like so:
  `samba_passdb_backend: ldapsam:"ldap://ldap-1.example.com"`
  
+ `samba_map_to_guest`: Specifies what to do with unregistered users. Possible values are:
  - `Never`: A registered user is required. [Default]
  - `Bad User`: If the user doesn't exist, it will be treated as a guest user, and `samba_guest_account` will be used as its account.
  - `Bad Password`: Logins with an invalid password will be treated as guest logins, and `samba_guest_account` will be used as the default account. 
  - `Bad Uid`: Only applicable when Samba is configured in some type of domain mode security. Logins that are successfully authenticated by the domain but have no valid Unix user account are treated like guest logins.
+ `samba_guest_account`: The user account to be used for guest users. Note that this user has to already exist.
+ `samba_username_map_file`: Specify a file which contains username mappings. Commonly used to map Windows user names to Unix users.
+ `samba_homes_accessible`: Whether to make user homes accessible or not. Defaults to `false`.
+ `samba_shares_root`: The root directory under which shares will be created.
+ `samba_shares`: A list containing share definitions. To define a share, you must at least provide a name for it. The following options are available when defining shares:

| Option | Required | Default Value | Description |
| ---    | ---      | ---           | ---         |
| `comment` | no    | -             | A comment for the share |
| `name` | yes      | -             | The name of the share |
| `path` | no       | samba_shares_root/name | The path where the share will be stored to |
| `public` | no | no | If `yes`, no password is required to connect to the service |
| `valid_users` | no | - | A list of users that are given read access to the share |
| `group` | no | - | A UNIX group name that will be used as the default primary group for permissions |
| `write_list` | no | - | A list of users that are given read-write access to the share |
| `browsable` | no | yes | Controls whether the service can be seen in browse lists |
| `writable` | no | no | Controls whether the users of the share are able to write to it |
| `printable` | no | no | Controls whether users can open, write to and submit spool files on the share directory |
| `guest_only` | no | no | If `yes`, only guest connections are allowed. For this option to work, the `public` option must be set to `true` |
| `create_mask` | no | 0644 | The permissions to be given to a newly created file |
| `directory_mask` | no | 07555 | The permissions to be given to a newly created directory |


Dependencies
------------

None

## Example Playbook

```yaml
- hosts: server
  vars_files:
    - vars/main.yml

  roles:
    - { role: chzerv.samba }
```

The `vars/main.yml` file:

```yaml

```

## License

MIT / BSD

## Author Information

Chris Zervakis
