# Ansible Role: Samba

This role installs and configures Samba (SMB) as a file server on Debian-based, RedHat-based and Archlinux systems.

Requirements
------------

+ A properly configured firewall. Samba requires ports 137-139 and 445 to be open.
+ The users specified as Samba users must already exist.

Role Variables
--------------

+ `samba_users`: User(s) to be added as Samba users.
  ```yaml
  samba_users:
    - username: john
      password: amazing_password
  ```
+ `samba_workgroup`: The workgroup which is -or will be- used in your network.
+ `samba_server_string`: A description for the server. Can be anything you like.
+ `samba_server_role`: Defines in which mode Samba will operate. Possible values are:
  - `standalone server` [Default]
  - `member server`
  - `classic primary domain controller`
  - `classic backup domain controller`
  - `active directory domain controller`
+ `samba_realm`: Specifies the Kerberos or Active Directory (AD) realm the host is part of.

+ `samba_log_file`: The file where logs are saved. If left empty, logging will be done through `syslog`.
+ `samba_log_size`: Maximum size of the log file.
+ `samba_log_level`: Controls the verbosity of the logs, with level 0 being the minimum, and level 10 being the maximum.

+ `samba_apply_cve_2017_7494_mitigations`: Whether to apply a workaround for [CVE-2017-7494](https://nvd.nist.gov/vuln/detail/CVE-2017-7494). If SELinux is enabled, there is no need for it. Otherwise, the `nt pipe support = no` option is used, which breaks browsing shares from Windows.
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

+ `samba_shares_root_directory`: The root directory under which shares will be created.
+ `samba_shares_config_file`: The file where the configuration of shares will be stored into.
+ `samba_homes_accessible`: Whether to make user homes accessible or not. Defaults to `false`.
+ `samba_shares`: A list containing share definitions. To define a share, you must at least provide a name for it. The following options are available when defining shares:

| Option | Required | Default Value | Description |
| ---    | ---      | ---           | ---         |
| `comment` | no    | -             | A comment for the share |
| `name` | yes      | -             | The name of the share |
| `path` | no       | samba_shares_root/name | The path where the share will be stored to |
| `public` | no | no | If `yes`, no password is required to connect to the service |
| `valid_users` | no | - | A list of users that will be allowed to login to the share |
| `group` | no | - | A UNIX group name that will be used as the default primary group for permissions |
| `write_list` | no | - | A list of users that will be given read-write access to the share |
| `read_list`  | no | - | A list of users that will be given read-only access to the share |
| `browsable` | no | yes | Controls whether the service can be seen in browse lists |
| `read_only` | no | yes | Controls whether the users of the share are able to write to it |
| `printable` | no | no | Controls whether users can open, write to and submit spool files on the share directory |
| `guest_only` | no | no | If `yes`, only guest connections are allowed. For this option to work, the `public` option must be set to `true` |
| `create_mask` | no | 0644 | The permissions to be given to a newly created file |
| `directory_mask` | no | 07555 | The permissions to be given to a newly created directory |

**Notes:**

+ Names starting with `@` are interpreted as UNIX groups.


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
samba_hosts_allow: 192.168.1.14 192.168.1.13 192.168.1.12

samba_shares:
  - name: myShare
    comment: "Private Share"
    public: "no"
    read_only: "no"
    group: users
    write_list: "@users"
```

## License

MIT / BSD

## Author Information

Chris Zervakis
