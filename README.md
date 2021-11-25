# Using inventory plugins with Ansible Automation Platform 2

Custom inventory scripts are gone in AAP2.

Instead you need to use an inventory plugin.

Major vendors provide plugins to retrieve items from their CMDB.

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
