# Ansible Role: Packer RHEL/CentOS Configuration for Vagrant VirtualBox

This role configures RHEL/CentOS (either minimal or full install) in preparation for it to be packaged as part of a .box file for Vagrant/VirtualBox deployment using [Packer](http://www.packer.io/).

The role may be made more flexible in the future, so it could work with other Linux flavors and/or other Packer builders besides VirtualBox, but I'm currently only focused on VirtualBox, since the main use case right now is developer VMs.

## Requirements

Prior to running this role via Packer, you need to make sure Ansible is installed via a shell provisioner. An example array of provisioners for your Packer .json template would be something like:

    "provisioners": [
      {
        "type": "shell",
        "execute_command": "echo 'vagrant' | {{.Vars}} sudo -S -E bash '{{.Path}}'",
        "script": "scripts/ansible.sh"
      },
      {
        "type": "ansible-local",
        "playbook_file": "ansible/main.yml",
        "role_paths": [
          "/Users/jgeerling/Dropbox/VMs/roles/geerlingguy.packer-rhel",
        ]
      }
    ],

The files should contain, at a minimum:

**scripts/ansible.sh**:

    #!/bin/bash -eux
    # Add the EPEL repository, and install Ansible.
    rpm -ivh http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
    yum -y install ansible

**ansible/main.yml**:

    ---
    - hosts: all
      sudo: yes
      gather_facts: yes
      roles:
        - geerlingguy.packer-rhel

You might also want to add another shell provisioner to run cleanup, erasing free space using `dd`, but this is not required (it will just save a little disk space in the Packer-produced .box file).

If you'd like to add additional roles, make sure you add them to the `role_paths` array in the template .json file, and then you can include them in `main.yml` as you normally would.

## Role Variables

None.

## Dependencies

None.

## Example Playbook

    - hosts: all
      roles:
        - { role: geerlingguy.packer-rhel }

## License

MIT / BSD

## Author Information

This role was created in 2014 by Jeff Geerling (@geerlingguy), author of Ansible for DevOps. You can find out more about the book at http://ansiblefordevops.com/, and learn about the author at http://jeffgeerling.com/.