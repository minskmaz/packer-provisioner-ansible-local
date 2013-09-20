# Ansible Local Provisioner

Type: ansible-local

The local Ansible provisioner configures Ansible to run on the machine by
Packer from local playbook and role files. Playbooks and roles can be
uploaded from your local machine to the remote machine. Ansible is run in
local mode via the ansible-playbook command.

## Basic Example

The example below is fully functional and expects the configured playbook
file to exist relative to your working directory:

```JSON
{
  "builders": [{
    "type": "amazon-ebs",
    "access_key": "AKIAIOSFODNN7EXAMPLE",
    "secret_key": "wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY",
    "region": "us-east-1",
    "source_ami": "ami-05355a6c",
    "instance_type": "t1.micro",
    "ssh_username": "ec2-user",
    "ssh_timeout": "5m",
    "ami_name": "ansible-local-{{timestamp}}"
  }],
  "provisioners": [
    {
      "type": "shell",
      "inline": [
        "sleep 30",
        "sudo yum install ansible --enablerepo=epel --enablerepo=epel-testing -y"
      ]
    },
    {
      "type": "ansible-local",
      "playbook_file": "local.yml"
    }
  ]
}
```

You can also upload roles and additional playbooks:

```JSON
{
  "provisioners": [
    {
      "type": "ansible-local",
      "playbook_file": "local.yml",
      "playbook_paths": [
        "playbooks/mysql.yml"
      ],
      "role_paths": [
        "roles/nodejs"
      ]
    }
  ]
}
```

And use them like this:

```YAML
# local.yml
---
- include: playbooks/mysql.yml
- hosts: 127.0.0.1
  sudo: yes
  connection: local
  tasks:
    - name: "ensure nginx is installed"
      yum: name="nginx" state=installed enablerepo="epel"
  roles:
    - nodejs
```

## Configuration Reference

## Execute Command

By default, Packer uses the following command to execute Ansible:

    ansible-playbook {{.PlaybookFile}} -c local -i "127.0.0.1,"
