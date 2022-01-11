# Ansible Role For Firewall

Setup Firewall configration with specific ports.

# Examples

Directory tree.

```
.
├── README.md
├── ansible.cfg
├── authorized_keys
│   └── aintek
│        └── centos
│        └── ubuntu
├── group_vars
│   ├── all.yml
│   ├── webserver_centos.yml
│   └── webserver_ubuntu.yml
├── inventory
└── webservers.yml
```


## Inventory file

```
[webservers:children]
webserver_ubuntu
webserver_centos

[webserver_ubuntu]
ubuntu ansible_host=10.10.10.10 ansible_port=22

[webserver_centos]
centos ansible_host=10.10.10.11 ansible_port=22
```

## Group Vars / Common settings(all.yml)

`all.yml` sets common variables.

```
# Common settings
become: yes
ansible_user: root

# Private_key is saved in local host only!
ansible_firewall_private_key_file: ""
```

## Group Vars / Ubuntu(webserver_ubuntu.yml)

`webserver_ubuntu.yml` is `webservers` host's children.

```
ansible_user: ubuntu
become: yes
ansible_become_password: 'ThisIsSecret!'

firewall_ufw_rules:
  - rule: allow
    interface: eth0
    to_port: 22
    protocol: tcp
  - rule: allow
    interface: eth1
    to_port: 10050
    protocol: tcp
    from_ip: 10.10.10.12
```

## Group Vars / CentOS(webserver_centos.yml)

`webserver_ubuntu.yml` is `webservers` host's children.

```
firewall_firewalld_rules:
  - permanent: yes
    immediate: yes
    interface: eth0
    zone: public
    port: 22
    protocol: tcp
    state: enabled
  - permanent: yes
    immediate: yes
    interface: eth1
    zone: internal
    port: 10050
    protocol: tcp
    from_ip: 10.10.10.12
```

# How to DryRun and Apply

DryRun

```
ansible-playbook -i inventory --private-key="~/.firewall/your_private_key" -CD webservers.yml --tags firewall

ansible-playbook -i inventory --private-key="~/.firewall/your_private_key" -CD webservers.yml --tags firewall
```

Apply

```
ansible-playbook -i inventory --private-key="~/.firewall/your_private_key" -D webservers.yml --tags firewall

ansible-playbook -i inventory --private-key="~/.firewall/your_private_key" -D webservers.yml --tags firewall
```
