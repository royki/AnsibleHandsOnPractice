# role_sentry

Installation of Sentry [Self-Hosted](https://develop.sentry.dev/self-hosted/#getting-started) in AWS EC2

## Requirements

1. Ansible Install on the machine
2. Run the Terraform Script before executing this role
3. Collect the `server_public_ip` from Terraform Script Output

**Example**

```shell
server_id = "i-xxxxxxxxxxx"
server_private_ip = "X.X.X.X"
server_public_ip = "Y.Y.Y.Y"
```

## Role Variables

1. Update follwoing value in `inventory` file.
   - `ansible_host`=Put the value from Terraform `server_public_ip`
   - `ansible_ssh_private_key_file`: `PATH_OF_KEY_PAIR.PEM`
2. Update following variables in `defaults/main.yml` file (Put your desired value).
   - `sentry_admin_email`
   - `sentry_admin_password`

### Execution

1. Ansible command
   - `ansible-playbook -i inventory sentry_onpremis_pb.yml -C` [check/dry-run before execution]
   - `ansible-playbook -i inventory sentry_onpremis_pb.yml` [ execution]
  
#### License

BSD
