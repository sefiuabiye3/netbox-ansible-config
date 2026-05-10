# netbox-ansible-config
netbox-ansible config


sudo dnf install -y python39 python39-pip
python3.9 -m pip install ansible
ansible-galaxy collection install netbox.netbox

Create isolated environment:

	mkdir -pv netbox-venv
	python3 -m venv /netbox-venv
	source /netbox-venv/bin/activate

Ungrade pip, and install the python library for NetBox
	python3 -m pip install --upgrade pip
	pip3 install pynetbox

Install ansible, netaddr, ansible-pylibssh, and pytz.
	pip install ansible
	pip install netaddr
	pip install ansible-pylibssh
	pip install pytz

Install the Ansible Galaxy collection for NetBox.
	ansible-galaxy collection install netbox.netbox

Install ansible collection to manage Cisco IOS.
	ansible-galaxy collection install cisco.ios

Ansible needs a token to use the NetBox API.
	Select API Tokens from the user menu.
	Select Add a Token from the API Token screen.
	Just create the token. Because no key is provide, one will be generate automatically.
	Copy the token from NetBox

Add NetBox to Ansible inventory
	NetBox to your Ansible inventory. Create a file named netbox_inventory.yml

Bearer nbt_0TzV3PyfbOfa.qaxwuUzUiDZTmpyHXHs3SzWNEtrFjoEetjxGOjIz
Bearer nbt_WwYJvdiKYpbn.6LsJU1PDzB2iWdJJwfOR9iO1TDZzJc1V8rJCswC1
---
plugin: netbox.netbox.nb_inventory
api_endpoint: http://127.0.0.1/
token: 'Bearer nbt_0TzV3PyfbOfa.qaxwuUzUiDZTmpyHXHs3SzWNEtrFjoEetjxGOjIz'
validate_certs: False
config_context: False

group_by:
  - device_roles
compose:
  ansible_network_os:platform.slug

	Create ansible local config file named ansible.cfg

[defaults]
inventory = /home/sabioye/Project/netbox/netbox-ansible/netbox_inventory.yml
host_key_checking = false
retry_files_enabled = false
forks = 4

This tells Ansible to use the NetBox API to retrieve the inventory. Try it with: ansible-inventory --list -i netbox_inventory.yml

Backup the running configuration
	Create a folder name backups
		mkdir backups

	Create the playbook file name backup_network_device.yml

---
- name: Playbook to backup configs on Cisco Routers
  connection: network_cli
  hosts: device_roles_router
  gather_facts: False

  tasks:
    - name: Show Running configuration on Device
      cisco.ios.ios_command:
        commands:
	  - show run
       register: config
