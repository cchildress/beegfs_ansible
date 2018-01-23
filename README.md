# BeeG FS Ansible
These playbooks set up a BeeG FS cluster on Debian based Linux nodes. It has been tested on Ubuntu 16.04.

## Setup

### First things first
* Install Python and Ansible (tested on Ansible 2.4.0.0 with Python 2.7.14)
* Record hostnames / IP addresses of machines to be in the cluster.
* Verify that you have a deploy user with sudo privileges that can auth using ssh keys.
* Make sure every node in the cluster has a [supported](http://docs.ansible.com/ansible/latest/intro_installation.html#managed-node-requirements) Python version installed.

### Configure the inventory file
Edit `inventory/hosts` to include the connection parameters for your nodes. If you have DNS set up you can probably remove the `ansible_host` information.

You should assign nodes to the following Ansible groups:
* beegfs-nodes - All nodes that will participate in BeeG FS (clients and servers).
* beegfs-mgmtd - The node that will run the management agent.
* beegfs-utils - All nodes that should have the BeeG FS utilities installed (defaults to all nodes).
* beegfs-meta - All nodes that will run the BeeG FS metadata service (defaults to all nodes).
* beegfs-storage - All nodes that will run the BeeG FS storage service (defaults to all nodes).
* beegfs-client - All nodes that will have a BeeG FS mount point (defaults to all nodes).

### Configure group variables
* `inventory/group_vars/beegfs-nodes.yml` - `beegfs_mgmtd_host`: Sets the IP or DNS name of the management node. Currently this assumes the eth0 address of the management node is correct.
* `inventory/group_vars/beegfs-meta.yml`  - `beegfs_meta_use_mount`: Whether to format a block device for the metadata storage or not.
* `inventory/group_vars/beegfs-meta.yml`  - `beegfs_meta_block_dev`: The block device to format if `beegfs_meta_use_mount` is set.
* `inventory/group_vars/beegfs-storage.yml` - `beegfs_storage_use_mount`: Whether to format a block device for the data storage or not.
* `inventory/group_vars/beegfs-storage.yml` - `beegfs_storage_block_dev`: The block device to format if `beegfs_storage_use_mount` is set.
* `inventory/group_vars/beegfs-client.yml` - `beegfs_mount_dir`: The mount path for the BeeG FS client.

### Other important variables
* `beegfs_apt_key`: The URL of the BeeG FS apt GPG key.
* `beegfs_repo_data`: The line to put in a sources.list file. This is where you set the BeeG FS major version number.
* `beegfs_mgmtd_dir`: Where to store management agent data.
* `beegfs_meta_dir`: Where to store metadata.
* `beegfs_storage_dir`: Where to store block storage.

## Running the playbooks
To run a full deploy use `ansible-playbook -i inventoy/hosts playbooks/deploy-beegfs.yml`.

To deploy only a specific part of BeeG FS you can use one of the `playbooks/deploy-beegfs-<name>.yml` plays instead.

### Tags
Every task has a `beegfs` tag to make this easier to integrate with a larger repository.
Every task within a role has a tag that matches the role name.

## Other notes
I've disabled the retry files in ansible.cfg. If you actually use those you'll want to turn them back on.
