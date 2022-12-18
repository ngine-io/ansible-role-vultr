# Ansible Role: Vultr Cloud

[![CI](https://github.com/ngine-io/ansible-role-vultr/workflows/CI/badge.svg?event=push)](https://github.com/ngine-io/ansible-role-vultr/actions?query=workflow%3ACI)

Manages compute resources on [Vultr Cloud](https://www.vultr.com/).

## Role Variables


## Dependencies

None

## Examples

### Playbook

A typical playbook would look like this.

```yaml
---
- name: Provision Cloud servers
  hosts: all
  serial: 5
  gather_facts: false
  roles:
    - role: ngine_io.vultr_cloud
      delegate_to: localhost

  post_tasks:
    - name: Wait for SSH access
      delegate_to: localhost
      wait_for:
        host: "{{ ansible_host }}"
        port: 22
        timeout: 60
```

### Common Configurations

Typical common configs in a top level group (e.g. `group_vars/all.yml`):

```yaml
# file: group_vars/all.yml

# API: creds
vultr__api_key: "..."


# Regoin: default ewr
vultr__region: ams

vultr__instance_os: "Rocky Linux 9 x64"

# Enable IPv6
vultr__instance_ipv6_enabled: true

vultr__firewall_groups:
  - name: proxy
    rules:
      - cidr: 0.0.0.0/0
        port: "443"

      - cidr: 0.0.0.0/0
        port: "80"

      - cidr: 1.2.3.4/32
        port: "8000:8999"

# Create a private networks for backend access.
vultr__vpcs:
  - name: default
    cidr: "10.99.1.0/24"
```

### Instances config via Group / Host Vars

Assuming an inventory similar to the following:

```ini
[proxy]
proxy1.example.com
proxy2.example.com

[cassandra]
cassandra1.example.com
cassandra2.example.com
cassandra3.example.com
```

#### Proxy Group Vars

Ensure all proxy instances use a different offering and the additional security group proxy:

```yaml
#file: group_vars/proxy.yml

# Default: vc2-1c-1gb
vultr__instance_plan: vc2-4c-8gb

# Assign firewall groups the instances (default: [default])
vultr__firewall_groups:
  - default
  - proxy

# Attach VPCs to the intances
vultr__instance_vpcs:
  - default
```

#### Cassandra Group Vars

```yaml
#file: group_vars/cassandra.yml

# Use CPU related offering
vultr__instance_plan: vhf-6c-24gb

# Attach VPCs to the intances
vultr__instance_vpcs:
  - default

```

----
> :information_source: **HINT**: get a list of available optons using the vultr cli.
> See https://github.com/vultr/vultr-cli.
----

## License

MIT

## Author Information

René Moser (@resmo)
