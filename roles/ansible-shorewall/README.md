Role Name
=========

Installs and configures Shorewall firewall (http://shorewall.net)

Requirements
------------

None

Role Variables
--------------

````
---
# defaults file for ansible-shorewall
config_shorewall: true
shorewall_interfaces:
  - name: 'eth0'
    zone: 'net'
    options:
      - 'dhcp'
      - 'tcpflags'
      - 'logmartians'
      - 'nosmurfs'
      - 'sourceroute=0'
  - name: 'eth1'
    zone: 'loc'
    options:
      - 'tcpflags'
      - 'nosmurfs'
      - 'routefilter'
      - 'logmartians'
shorewall_masquerade_info:
  enabled: true
  interface: 'eth0'
  sources:
    - '10.0.0.0/8'
    - '169.254.0.0/16'
    - '172.16.0.0/16'
    - '192.168.0.0/16'
shorewall_policies:
  - source: '$FW'
    dest: 'net'
    policy: 'ACCEPT'
    log_level: ''
    limit_burst: ''
  - source: 'loc'
    dest: 'net'
    policy: 'ACCEPT'
    log_level: ''
    limit_burst: ''
  - source: 'net'
    dest: 'all'
    policy: 'DROP'
    log_level: 'info'
    limit_burst: ''
  - source: 'all'
    dest: 'all'
    policy: 'REJECT'
    log_level: 'info'
    limit_burst: ''
shorewall_rules:
  - section: 'NEW'
    rules:
      - source: '$FW'
        dest: 'net'
        action: 'ACCEPT'
        proto: 'tcp'
        dest_ports:
          - '53'
      - source: '$FW'
        dest: 'net'
        action: 'ACCEPT'
        proto: 'udp'
        dest_ports:
          - '53'
      - source: 'loc'
        dest: '$FW'
        action: 'ACCEPT'
        proto: 'tcp'
        dest_ports:
          - '22'
          - '80'
          - '443'
      - source: 'net'
        dest: 'loc:192.168.202.200'
        action: 'DNAT'
        proto: 'tcp'
        dest_ports:
          - '80'
          - '443'
shorewall_startup: true
shorewall_zones:
  - name: 'fw'
    in_options:
    options:
    out_options:
    type: 'firewall'
  - name: 'net'
    in_options:
    options:
    out_options:
    type: 'ipv4'
  - name: 'loc'
    in_options:
    options:
    out_options:
    type: 'ipv4'
````

Dependencies
------------

None

Example Playbook
----------------

````
- hosts: all
  become: true
  vars:
  roles:
    - role: ansible-shorewall
  tasks:
````
License
-------

BSD

Author Information
------------------

Larry Smith Jr.
- @mrlesmithjr
- http://everythingshouldbevirtual.com
- mrlesmithjr [at] gmail.com
