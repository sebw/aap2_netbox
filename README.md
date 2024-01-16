# Using inventory plugins with Ansible Automation Platform 2

Custom inventory scripts are gone in AAP2.

Instead you need to use an inventory plugin.

Major vendors provide plugins to retrieve items from their CMDB.

## Netbox CMDB

In this example, we are using the community supported inventory plugin for Netbox, an open source CMDB.

Documentation can be found [here](https://netbox-ansible-collection.readthedocs.io/en/latest/plugins/nb_inventory_inventory.html#ansible-collections-netbox-netbox-nb-inventory-inventory).

In order to use this example:

- fork this repository
- edit `netbox_inventory.yml` accordingly
- create a project in AAP2 pointing to your fork
- create an inventory
- create a new source in your inventory with type "Sources from a Project"
- choose "/ (project root)" for the inventory file
- tick "update on launch" if you want your inventory to be refreshed any time you call the inventory
- save and sync
- in the hosts tab you should see your inventory items

## Execution environment (EE)

AAP2 does everything through execution environments (they are just containers with ansible, collections and other dependencies needed to run any job).

The Netbox collection isn't present in standard AAP2 EE.

You need to create your own EE with the Netbox collection in it.

On a RHEL box:

```bash
dnf install --enablerepo=ansible-automation-platform-2.4-for-rhel-9-x86_64-rpms ansible-builder
```

Create an `execution-environment.yml` file:

```yaml
version: 3

images:
  base_image:
    name: your.private.automation.hub.host/ee-minimal-rhel8:latest

dependencies:
  galaxy:
    collections:
      - netbox.netbox

options:
  package_manager_path: /usr/bin/microdnf
```

Build:

```bash
ansible-builder build
```

Tag and push your new image in Private Automation Hub

```bash
podman login your.private.automation.hub.host (user and pass specified in the inventory file you edited before installing AAP or found in your kubernetes secrets)
podman tag localhost/ansible-execution-env your.private.automation.hub.host/ee-netbox-rhel8:latest
podman push your.private.automation.hub.host/ee-netbox-rhel8:latest
```

You can now add this new EE in AAP2 and use it for the dynamic inventory source.

## Other resources

ServiceNow CMDB: https://www.ansible.com/blog/using-an-inventory-plugin-from-a-collection-in-ansible-tower
