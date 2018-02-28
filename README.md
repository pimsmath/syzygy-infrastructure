# Build a Hub for Callysto

## Terraform
The resources are controlled by terraform so we can destroy and recreate
everything quickly.
 
 * deb21990-9256-4ac3-ac7c-b7cb5275d619: m1.8c32g
 * deb21990-9256-4ac3-ac7c-b7cb5275d619: CentOS 7
 * 2 * 50G volumes for user homedir backing

### Setup
You will need to make some environment variables available for terraform to talk
to openstack. As an example, we need the variable `${var.os_cybera_password}`
inside terraform, we can do this by defining TF_VAR_os_cybera_password before
running terraform. There's an `init.sh` script in the terraform directory which
should be able to handle most of this
```
  $ . init.sh
  $ echo $TF_VAR_os_project_name
```


### First Time
You will need the openstack plugin and some other bits and pieces so run
`terraform init` in the terraform directory
```
  $ cd terraform
  $ terraform init
```

### Terraform apply
`terraform apply` will create the resources for you
```
  $ cd terraform
  $ terraform apply

...
Apply complete! Resources: 4 added, 0 changed, 0 destroyed.

Outputs:

ip = 162.246.156.221
```
That IP address should be associated with some DNS name before the ansible
playbooks are run.


## Ansible

We have encoded most of the setup for creating our JupyterHub instances as
ansible playbooks. Before actually running the playbooks, I usually run some
ad-hoc commands to get the system to a known state
```
  $ cd ansible
  $ ansible --become -i inventory.yml \ 
    -m 'yum' -a 'name=* state=latest' hub-dev.callysto.ca
  $ ansible --become -i inventory.yml \
    -m 'command' -a 'reboot' hub-dev.callysto.ca
```

