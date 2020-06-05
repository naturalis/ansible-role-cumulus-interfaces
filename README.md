# Ansible Role: Cumulus interfaces

This role will setup OSPF (FRR) on spine (and uplink) switches. Also the
interfaces will be configured and PoE will be configured on leaf switches.

Naturalis uses this role together with a private inventory.

## Requirements

None.

## Role Variables

Available variables are listed below, along with default values:

An example would be:

```yaml
#group_vars
ib_mgmt: 114
leaf_routes:
  - 0.0.0.0/0 10.114.0.1
fw_carp_address: xxx.xxx.xxx.xxx
routing:
  ospf:
    network: xxx.xxx.xxx.xxx/29
    fw-carp-address: xxx.xxx.xxx.xxx
    static-routes:
    - 10.141.0.0/16
    - 10.142.0.0/16
vlans:
  114:
    name: Management
    ipv4-virtual: 10.114.0.1/24
    ipv4-1: 10.114.0.2/24
    ipv4-2: 10.114.0.3/24
  115:
    name: Servers
    ipv4-virtual: 10.115.0.1/24
    ipv4-1: 10.115.0.2/24
    ipv4-2: 10.115.0.3/24

default_port_config: visitors
port_configs:
  servers:
    untagged: 115
  office:
    untagged: 119
    tagged:
    - 120
    qos: high
  printer:
    untagged: 121
  visitors:
    untagged: 125
```

```yaml
#host_var for spine switch
lo: 10.255.255.6
ospf:
  vlan267:
    vlan: 267
    router-address: xxx.xxx.xxx.xxx/30
    network: 'xxx.xxx.xxx.xxx/30'
mlag:
  primary: true
  members:
  - swp47
  - swp48
  address: 169.254.1.1/30
  peer-ip: 169.254.1.2
  backup-ip: 192.168.144.7
  sysmac: 44:38:39:FF:FA:01
  priority: 4096
svi:
  269:
    ipv4-virtual: xxx.xxx.xxx.xxx/29
    ipv4: xxx.xxx.xxx.xxx/29
aggregation_ports:
  swp44:
    trunk: false
    vlan: 267
    alias: uplink
  swp45:
    trunk: false
    vlan: 269
    alias: firewall-outside
  swp46:
    trunk: true
    alias: firewall-inside
  ser-a3:
    mlag: true
    mlag-id: 1
    members:
    - swp1
    trunk: true
  ser-a4:
    mlag: true
    mlag-id: 2
    members:
    - swp2
    trunk: true
```

```yaml
#host_var for leaf switch
lo: 10.255.255.25
mlag:
  primary: true
  members:
  - swp50
  address: 169.254.1.1/30
  peer-ip: 169.254.1.2
  backup-ip: 192.168.144.24
  sysmac: 44:38:39:FF:FA:02
  priority: 4096
aggregation_ports:
  uplink:
    mlag: true
    mlag-id: 1
    members:
    - swp52
    trunk: true
  downlink:
    mlag: true
    mlag-id: 2
    members:
    - swp49
    trunk: true
access_ports:
  1: servers
  2: office
```

## Extra variables

* `enable_omf`: By default this role disables Optimized Multicast Flooding /
  Forwarding (OMF) also known as IGMP / MLD Snooping. You can enable it by
  setting `enable_omf` to `yes`.

## Dependencies

None.

## Example Playbook

    - hosts: switches
      roles:
        - ansible-cumulus-interfaces

## License

Apache2
