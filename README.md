# Using inventory plugins with Ansible Automation Platform 2

Custom inventory scripts are gone in AAP2.

Instead you need to use an inventory plugin.

Major vendors provide plugins to retrieve items from their CMDB.

## Netbox CMDB

In this example, we are using the community supported inventory plugin for Netbox, an open source CMDB.

Documentation can be found [here](https://netbox-ansible-collection.readthedocs.io/en/latest/plugins/nb_inventory_inventory.html#ansible-collections-netbox-netbox-nb-inventory-inventory).

In order to use this example:

- create a custom credentials type in AAP (Administration > Credential Types > Add)

Input configuration:

```
fields:
  - id: NETBOX_API
    type: string
    label: 'Netbox API endpoint (ex: https://netbox.example.org)'
    secret: true
  - id: NETBOX_TOKEN
    type: string
    label: Netbox token
    secret: true
required:
  - NETBOX_API
  - NETBOX_TOKEN
```

Injector configuration:

```
env:
  NETBOX_API: '{{ NETBOX_API }}'
  NETBOX_TOKEN: '{{ NETBOX_TOKEN }}'
```

Then:

- fork this repository
- create a project in AAP2 pointing to your fork and branch "vaulted_token"
- create an inventory
- create a new source in your inventory with type "Sources from a Project"
- choose the project you just created and synced
- choose your custom credential
- choose "/ (project root)" for the inventory file
- tick "update on launch" if you want your inventory to be refreshed any time you call the inventory
- choose your custom execution environment that contains the Netbox collection
- save and sync
- in the hosts tab you should see your inventory items

## Building a customer Netbox Execution environment

1. Install a Red Hat Enterprise Linux or Fedora machine
2. Install podman and ansible-builder
3. Create the following files in a directory you call netbox-ee
execution-environment.yml
```
---
version: 3
images:
  base_image:
    name: registry.redhat.io/ansible-automation-platform-25/ee-supported-rhel8:latest

dependencies:
  galaxy: requirements.yml
  python: requirements.txt
  system: bindep.txt

options:
  package_manager_path: /usr/bin/microdnf
```

requirements.yml:
```
---
collections:
  - name: netbox.netbox
```

requirements.txt:
```
pytz
pynetbox
```

bindep.txt:
```
gcc
systemd-devel
python3.11-devel
```

5. Build the execution environment
```
$ ansible-builder build -v3 -t netbox-inventory-ee
```

6. Make execution environment available in Ansible Automation Platform.  


## Other resources

ServiceNow CMDB: https://www.ansible.com/blog/using-an-inventory-plugin-from-a-collection-in-ansible-tower
