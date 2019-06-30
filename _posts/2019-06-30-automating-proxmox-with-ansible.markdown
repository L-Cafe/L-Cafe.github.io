---
title:      "Automating Proxmox with Ansible"
date:       2019-06-30
tags:
category:
comment:    header sizes are 2000x750
header:
  image:    assets/images/2019-06-30-automating-proxmox-with-ansible/header.jpg
  teaser:   assets/images/2019-06-30-automating-proxmox-with-ansible/header.jpg
  caption:  "Photo credit: [**Unsplash**](https://unsplash.com)"
---

Ansible is an automation platform for managing machines. When combined with Proxmox, it becomes a ridiculously powerful tool to orchestrate virtual datacenters.

One advantage of Ansible is that it uses a declarative approach. You tell Ansible what state you want the machine to be in, and Ansible handles it appropriately to ensure this. For example, you can tell Ansible to ensure the machine has a specific list of packages up to date, and Ansible won’t try to update the entire system or reinstalling the packages if they are already in their latest version.

## Ansible basics

### Inventory

Ansible can save hosts in a central, system-wide repository called “Inventory”.

Although inventories offer more functionality than just storing hosts, the rest of functionality is not relevant to this guide and will be omitted. The [official documentation](https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html) is always the authoritative source of information, so check it out if you want to learn more about Ansible inventories.

Ansible stores a global inventory on `/etc/ansible/hosts`, but it is possible to specify other paths for inventories supplying the `-i <path>` argument from the Ansible command line.

The inventory has an INI-like syntax.

For this example, I will use the global `/etc/ansible/hosts` system inventory, and it looks like this:

```
[proxmox]
main ansible_host=192.168.1.100 ansible_port=2288 ansible_user=root ansible_connection=ssh
```

- `main` is the name of the individual machine, while `[proxmox]` represents the name of the group of machines. I could add more machines to this group by appending lines below the line with `main`. The rest of attributes are optional.
- `ansible_host` is the machine address. Either DNS name, IPv4 or whatever is necessary to address that specific host, in case it differs from the name.
- `ansible_port` defines the port to connect on.
- `ansible_user` defines the user to connect as. The default is `root`. You will want this user to [have root privileges](https://docs.ansible.com/ansible/latest/user_guide/become.html).
- `ansible_connection` defines the protocol to connect with the machine.

Now that I have defined a host, I can use the name `main` on Ansible playbooks, to target that Proxmox node.

## Playbooks

Ansible “scripts” are written in Playbooks, which contain the instructions to execute, as well as associated information such as credentials.

If additional credentials or hostnames are needed, they must be defined in the appropriate inventory.

Having defined the hosts in the step before, we can now create a file named `learning-ansible.yml`. Ansible playbooks use the YAML syntax, as evidenced by the filename extension.

### Proxmox specifics

The [documentation](https://docs.ansible.com/ansible/latest/modules/proxmox_module.html) specifies a few mandatory fields.

Now, in the `learning-ansible.yml`, it is possible to perform many operations as if done through the Proxmox Web GUI.

```
- name: Delete old Proxmox VMs
  hosts: main
  tasks:
          - name: Stop LXC ID 300
            proxmox:
                    api_host   : 192.168.1.100
                    api_user   : ansible_pve@pve
                    api_password   : <PASSWORD>
                    node   : proxmox_master
                    vmid   : 300
                    state   : stopped
          - name: Delete LXC ID 300
            proxmox:
                    api_host   : 192.168.1.100
                    api_user   : ansible_pve@pve
                    api_password   : <PASSWORD>
                    node   : proxmox_master
                    vmid   : 300
                    state   : absent
- name: Create Proxmox VMs
  hosts: main
  tasks:
          - name: Spin up LXC Debian machine with ID 300
            proxmox:
                    api_host   : 192.168.1.100
                    api_user   : ansible_pve@pve
                    api_password   : <PASSWORD>
                    node   : proxmox_master
                    vmid   : 300
                    hostname   : debian.example.com
                    ostemplate   : ‘local:vztmpl/debian-9.0-standard_9.0-2_amd64.tar.gz’
                    cpus   : 1
                    cpuunits   : 500
                    memory   : 128
                    disk   : 8
                    netif : ‘{"net0":"name=eth0,gw=192.168.1.100,ip=192.168.1.101/24,bridge=vmbr2"}’
                    onboot   : yes
                    unprivileged   : yes
                    pubkey   : ‘<YOUR PUBKEY>‘
                    state   : present
          - name: Start debian.example.com LXC VM (ID 300)
            proxmox:
                    api_host   : 192.168.1.100
                    api_user   : ansible_pve@pve
                    api_password   : <PASSWORD>
                    node   : proxmox_master
                    vmid   : 300
                    state   : started
```

This example Playbook will:

1. Stop and delete an LXC container
2. Set up a new container with the same VMID
3. Start the container

It is important to note a few things:

- It‘s possible to store and use variables. Here, the only variable used is the `main` host.
- Names can be arbitrary, but a good idea is to make them descriptive, so you know what the playbook is doing at all times, and, in case of errors, which step.
- As noted on the documentation, it is possible to set the password as an environment variable. This is the preferred way to share passwords in a team.
- Setting a public key on the newly created LXC machine will allow Ansible to connect after setting the VM up to perform additional tasks.
- LXC machines are easy to automate because they can be programmatically set up through templates, but KVM machines require interactive input unless they are installed from a snapshot or through [cloud-init](https://pve.proxmox.com/wiki/Cloud-Init_Support).
- On a multi-node Proxmox setup, it is also possible to command different Proxmox nodes to perform different tasks. This is specified on the `node` line.

Now that the VM has been set up (in this example, with IP 192.168.1.101), it is now possible to configure and install anything through Ansible.

In my case, I am using a modified version of the Ansible playbook, with more tasks after setting up the VM successfully, to clone a few GitHub projects, and bring them online. This way, I can refresh services weekly, and keep running the latest version.

Additionally, it is possible to set up automated backups and have Ansible pull those backups into the new machine, so no settings are lost when refreshing images.
