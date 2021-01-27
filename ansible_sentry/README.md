# Ansible and Terraform

- Terraform Script to launch EC2 instance. - <https://github.com/royki/AWSHandsOnPractice/tree/terraform/resources/sentry>
- Automate the bootstrapping the sentry install on this instance to the point of hitting the sentry login screen
- Self-signed certificate is bootstrapped in ansible role (Domain is not setup as I don't have any domain)
- Please access the site via <https://IP>

- Provided Terraform Script to launch EC2 Instance
  - Important: **Need one existing keypair from us-east-1 region**
- Provided Ansible Role to install Sentry Self-Hosted Application

## Requirements

1. Terraform Install on the machine
2. Ansible Install on the machine

### Execute Terraform

- Set this value before execute terraform script
  - `key_name = "KEY_PAIR_NAME"`
- Launce AWS EC2 Instance
  - `terraform init`
  - `terraform plan`
  - `terraform apply` or `terraform apply --auto-approve`
  - To Destory EC2 Instance
    - `terraform destroy` or `terraform destroy --auto-approve`

### Execute Ansible Role *role_sentry*

1. Collect the `server_public_ip` from Terraform Script Output

**Example**

```shell
server_id = "i-xxxxxxxxxxx"
server_private_ip = "X.X.X.X"
server_public_ip = "Y.Y.Y.Y"
```

#### Role Variables

1. Update follwoing value in `inventory` file.
   - `ansible_host`=Put the value from Terraform `server_public_ip`
   - `ansible_ssh_private_key_file`: `PATH_OF_KEY_PAIR.PEM`
2. Update following variables in `defaults/main.yml` file (Put your desired value). It is needed to login Sentry Login
   - `sentry_admin_email`
   - `sentry_admin_password`

#### Ansible command

- `ansible-playbook -i inventory sentry_onpremis_pb.yml -C` [check/dry-run before execution]
- `ansible-playbook -i inventory sentry_onpremis_pb.yml` [ execution]

#### Access the Site

- Open <https://Public_IP_OF_EC2_INSTANCE>
- Login with your `sentry_admin_email` and `sentry_admin_password`

- After Execution of the Ansible Role, you'll see the following message to access the site
  
```shell
TASK [role_sentry : Go to the following url to access the site] **************************************************************************************************************************
ok: [snt-server] => {
    "msg": "https://IP"
}

```
