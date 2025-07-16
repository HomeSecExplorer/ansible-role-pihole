# Ansible Role: Pi-hole

[![CI](https://github.com/HomeSecExplorer/ansible-role-pihole/actions/workflows/ci.yml/badge.svg)](https://github.com/HomeSecExplorer/ansible-role-pihole/actions/workflows/ci.yml)
![Ansible Galaxy](https://img.shields.io/badge/ansible-galaxy-blue?logo=ansible)
![License: MIT](https://img.shields.io/badge/license-MIT-green.svg)

---

**Author:** HomeSec Explorer  
**License:** MIT  
**Tags:** pihole, dns, adblock, local_dns, selfhosted, homelab, privacy

## Description

This role installs, configures, and manages [Pi-hole](https://pi-hole.net/) using Ansible.
It supports version 6.x and sets up the complete config file - including DNS,DHCP, NTP, and web interface - while also managing blocklists and domainlists directly via the Pi-hole database.

By default, Pi-hole is configured to use a local [cloudflared](https://developers.cloudflare.com/cloudflared/) instance as its upstream DNS-over-HTTPS (DoH) resolver.
You can deploy and manage this DoH proxy using the [`HomeSecExplorer.cloudflared`](hhttps://github.com/HomeSecExplorer/ansible-role-cloudflared) role, or customize your DNS upstreams via the `hseph_upstreams` variable.
E.g. cloudflare:

```yaml
hseph_upstreams:
  - '1.1.1.1'
  - '1.0.0.1'
```

Ideal for self-hosted networks, homelabs, or as a privacy-focused DNS resolver.

---

## Requirements

- Ansible `>= 2.13`
- Root (or sudo) privileges on the target host
- Internet access for Pi-hole system

## Supported Operating Systems

> See the [Pi-hole system requirements](https://docs.pi-hole.net/main/prerequisites/) for official OS support details.

- Debian 11 (Bullseye), 12 (Bookworm)
- Ubuntu 22.04 (Jammy), 24.04 (Noble)

> ⚠️ The OS compatibility check (`hseph_os_check`) ensures supported platforms for this role - not official Pi-hole support.

---

## Role Variables  (examples)

> ⚙️ For all changeable variables see `defaults/main.yml`.

### Authentication

```yaml
hseph_pw_hash: ''            # Hashed password for web UI (takes precedence)
hseph_pw_plain: 'Passw0rd'   # Plaintext fallback (do not use in production)
```

### Control Flags

```yaml
hseph_install: true          # Install Pi-hole if not present
hseph_update: true           # Run Pi-hole update if already installed
hseph_configure: true        # Apply Pi-hole configuration (TOML, DNS, blocklists)
hseph_os_check: true         # Validate if the target OS is supported before proceeding
```

### Blocklists

```yaml
hseph_pihole_blocklist:
  - url: 'https://raw.githubusercontent.com/StevenBlack/hosts/master/hosts'
    enable: '1'
    comment: 'default'
  - url: 'https://adaway.org/hosts.txt'
    enable: '1'
    comment: 'Ads'
```

### Domainlist

```yaml
hseph_pihole_domainlist:
  - domain: 'example.com'
    type: '1'
    enabled: '1'
    comment: 'Block example.com'
```

### Local DNS Entries

> Automatically generates DNS records for inventory hosts (when enabled and using `hostname ansible_host=IP` format)

```yaml
hseph_local_dns_entry: true
hseph_dns_for_inventory: true
hseph_dns_file:
  - template: 'example_dns.conf.j2'
    dest: '00.{{ hseph_dns_domain }}.conf'
```

- Example inventory:
  - ```yaml
    [dns_servers]
    pihole1 ansible_host=192.168.1.252
    ```

---

## Available Tags

- `install` – Install Pi-hole and dependencies
- `uninstall` – Uninstall Pi-hole and dependencies
- `update` – Update Pi-hole to the latest version
- `config` – Apply Pi-hole settings (TOML, DNS, blocklists)

---

## Install this role

From Ansible Galaxy (recommended):

- ```bash
  ansible-galaxy install HomeSecExplorer.pihole
  ```

Or manually (via Git):

- ```bash
  git clone https://github.com/HomeSecExplorer/ansible-role-pihole.git roles/HomeSecExplorer.pihole
  ```

## Example Playbook

```yaml
- name: Deploy Pi-hole with full configuration
  hosts: dns_servers
  become: true
  roles:
    - role: HomeSecExplorer.pihole
```

---

## License

MIT

## Author Information

HomeSec Explorer  
🔗 [YouTube Channel](https://www.youtube.com/@HomeSecExplorer)

If this role was helpful, drop a ⭐ on GitHub, subscribe on YouTube or Sponsor me!
