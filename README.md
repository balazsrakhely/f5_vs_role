# Ansible role cetinhu.f5.vs

Create a virtual server in F5 

## Task: main.yaml

### Parameters

| Parameter | Type | Flags | Default | Description |
| --- | --- | --- | --- | --- |
| f5_cred.f5_user | string | mandatory | | F5 provider user |
| f5_cred.f5_password | string | mandatory | | F5 provider password |
| f5_cred.f5_hosts | string | mandatory | | F5 provider hosts (comma separated list, 2 items) |
| vs_name | string | mandatory | | The name of the virtual server, full name convention: <vs_name>_<vs_protocol><vs_port>_vs, e.g.: myvirtualserver_tcp443_vs |
| vs_operation | string | mandatory | | Specifies what virtual server operation to do. Can be 'create', 'update' or 'remove' | 
| vs_address | string | optional | | Mandatory on 'create'. The address of the virtual server |
| vs_vlans | string | optional | | Mandatory on 'create'. Enabled VLANs for the virtual server. Comma separated list |
| vs_port | string | optional | | Mandatory on 'create'. Port of the virtual server. If 0, the virtual server listens on any port. (can be a number or the name of the port) |
| vs_protocol | string | optional | | Mandatory on 'create'. The network protocol for the traffic on the virtual server |
| partition | string | optional | Common | The partition in which the resources should be created |
| pool_name | string | optional | | The name of the pool the virtual server should use |
| provider_server_port | int | optional | 443 | The port the hosts are accessible on |
| provider_validate_certs | bool | optional | true | If false, SSL certificates are not validated. Use this only on personally controlled sites using self-signed certificates. |

### Functionality

Pre-Steps:

1. Validate presence of mandatory provider credentials

2. Validate the presence of mandatory virtual server parameters (vs_name, vs_operation, partition) 

3. From the provided 2 hosts, select the active one and use it in later steps

Steps:

If 'vs_operation' equals 'create':

1. Validate the presence of mandatory virtual server parameters (vs_port, vs_protocol, vs_address, vs_vlans)

2. Create the virtual server with the provided parameters. If virtual server exists, update its parameters.

If 'vs_operation' equals 'update' or 'remove':

1. Provide option to omit the 'vs_port' and 'vs_protocol' parameters and input the whole name. If port and/or protocol is not defined, or the name already 
  uses them as suffix, use the 'vs_name' input parameter as is, and do not construct a new name with port and protocol. If port and protocol are still 
  provided and the name doesn't have them as suffix, the name construction still happens. 


### Examples

```yaml
- name: Create virtual server
  ansible.builtin.include_role:
    name: cetinhu.f5.vs
  vars:
    f5_cred:
        f5_user: apiuser
        f5_password: apipassword
        f5_hosts: 10.10.10.10,10.10.10.11
    vs_name: myvirtualserver
    vs_operation: create
    vs_address: 192.168.10.10
    vs_vlans: VLAN1,VLAN2
    vs_port: 443
    vs_protocol: tcp
    partition: My_partition
    pool_name: mypool
```

```yaml
- name: Remove virtual server with only name
  ansible.builtin.include_role:
    name: cetinhu.f5.vs
  vars:
    f5_cred:
        f5_user: apiuser
        f5_password: apipassword
        f5_hosts: 10.10.10.10,10.10.10.11
    vs_operation: remove
    vs_name: myvirtualserver_tcp443_vs
    partition: My_partition
```

```yaml
- name: Remove virtual server with port and protocol
  ansible.builtin.include_role:
    name: cetinhu.f5.vs
  vars:
    f5_cred:
        f5_user: apiuser
        f5_password: apipassword
        f5_hosts: 10.10.10.10,10.10.10.11
    vs_operation: remove
    vs_name: myvirtualserver
    vs_port: 443
    vs_protocol: tcp
    partition: My_partition
```

```yaml
- name: Update pool on virtual server
  ansible.builtin.include_role:
    name: cetinhu.f5.vs
  vars:
    f5_cred:
        f5_user: apiuser
        f5_password: apipassword
        f5_hosts: 10.10.10.10,10.10.10.11
    vs_operation: update
    vs_name: myvirtualserver_tcp443_vs
    partition: My_partition
    pool_name: new_pool
```

```yaml
- name: Remove pool on virtual server
  ansible.builtin.include_role:
    name: cetinhu.f5.vs
  vars:
    f5_cred:
        f5_user: apiuser
        f5_password: apipassword
        f5_hosts: 10.10.10.10,10.10.10.11
    vs_operation: update
    vs_name: myvirtualserver_tcp443_vs
    partition: My_partition
    pool_name: ""
```

```yaml
- name: Remove pool on virtual server
  ansible.builtin.include_role:
    name: cetinhu.f5.vs
  vars:
    f5_cred:
        f5_user: apiuser
        f5_password: apipassword
        f5_hosts: 10.10.10.10,10.10.10.11
    vs_operation: update
    vs_name: myvirtualserver_tcp443_vs
    partition: My_partition
```
