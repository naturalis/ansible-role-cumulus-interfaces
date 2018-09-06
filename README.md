# Ansible Role: Cumulus interfaces

Used in [network](https://github.com/naturalis/network/) repo.

Runnable with:
```bash
ansible-playbook playbooks/cumulus_interfaces.yml -i environments/prod
```

This role will setup OSPF (FRR) on spine (and uplink) switches. Also the interfaces will be configured and PoE will be configured on leaf switches.

## Requirements

None.

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

An example would be:

```bash
routing:
  ospf:
    network: 145.136.246.224/29
    fw-carp-address: 145.136.246.232
    static-routes:
    - 10.141.0.0/16
    - 10.142.0.0/16

general:
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

node:
  netdw2-spine-a:
    lo: 10.255.255.1/32
    ospf:
      router-id: 145.136.246.218
      lnk-vlan: 269
      members:
      - swp47
    mlag:
      primary: true
      members:
      - swp49
      - swp50
      address: 169.254.1.1/30
      peer-ip: 169.254.1.2
      backup-ip: 192.168.200.10
      sysmac: 44:38:39:FF:01:12
      priority: 4096
    bridge:
      native: 201
      priority: 4096
    ports:
      ser-d:
        mlag: true
        mlag-id: 1
        members:
        - swp1
        trunk: true
      ser-h:
        mlag: true
        mlag-id: 2
        members:
        - swp2
        trunk: true
```

## Dependencies

None.

## Example Playbook

    - hosts: switches
      roles:
        - ansible-role-cumulus-interfaces

## License

Apache2
