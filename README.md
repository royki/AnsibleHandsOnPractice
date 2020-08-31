# _Ansible_
------------------

- Web server with Load balancers 
- Custom Inventory
  - `ansible -i inventory --list-hosts all`
- With *ansible.cfg*
  - `ansible --list-hosts all`
  - `ansible --list-hosts webservers`
  - `ansible --list-hosts loadbalancers`
- Ansible Tasks(run adhoc commands)
  - `ansible -m ping all`
  -  *(ansible command) (module flag) (module name) (inventory)*
  - `ansible --list-hosts "*"`
  - `ansible --list-hosts app*`
  - `ansible --list-hosts webservers:loadbalancers # using group functions`
  - `ansible --list-hosts \!control # using negate function`
  - `ansible --list-hosts webservers[0:10]`
  - `ansible -m ping all`
  - With *uname*, `RC=0,Success`
  - `ansible -m shell -a "uname" webservers:local`
  - `ansible -m shell -a "uname" app1:local`
  - `ansible -m shell -a "uname" local`
  - Return code `RC=1,Failure`
  - `ansible -m command -a "/bin/false" \!loadbalancers`
- Playbook
  - processes & manage configuration
  - run synchronously & asynchronously
  - ability to create infrastructure as code and manage it all in source control
  - list out everything we need & want to apply to each instance
  - Group them accordingly to configuration usage
  - Ensure they are in a logically defined order
  - Run each tasks according to the order they are listed
  - Example: update, install, configure check status etc
  - Run each tasks according to the order they are listed
- Service Handlers
- Import playbook
  - import_playbook: playbook_name
- Ansible adhoc command to stop apache
  - `ansible -m service -a "name=apache2 state=stopped"--become loadbalancers` 
- Variables
  - Gathers useful facts about host, that can be used in playbooks
  - Use *status* module to see all the facts gathered during the *TASK[Gathering Facts]*
  - Use *jinja2* template and expressions
  - `ansible -m setup app1`
  - Variables from info returned from tasks; call the registered variables for later use
    - `register: dir_contents`
  - Use debug mode
- Roles
  - Ansible provides framework that makes each part of variables, tasks, templates & modules fully independent
  - Group tasks together in a way that is self containing.
  - Predefiend directory structure 
  - Break configurations into files
  - Reuse code by others for similar configurations
  - Use `ansible-galaxy webservers init` to create predefined framework of roles
- Check Mode("Dry Run"): Reports changes that Ansible would have to make on the end hosts rather than applying the changes
  - Run Ansible without affecting the remote system
  - Reports changes back
  - Use at one node at a time basic(example: configuration management)
  - `ansible-playbook settings-app.yaml --check` [`--check` flag to check the status]
- Error Handling in Playbooks
  - `changed_when: false`
  - `ignore_errors: yes`
  - TODO

### Ansible Async directive

- Async & Poll

```yaml
- task:
    - command: /opt/app.py
      async: 360
      poll: 60
      register: app_result

    - name: Check status of tasks
      async_status: jid={{ app_result.ansible_job_id }}
      register: job_result
      until: job_result.finished
      retires: 30
```

### Ansible Strategy

- Linear Strategy (Default) [Each task is finished first in each server before proceding to next task]
- Free Strategy [Independantly run task in all server]
  - `strategy: free`
- Batch Strategy [Based on Linear]
  - marked with `serial: 3` [Ansible ll run 1st 3 server to execute task and then next]
  - `serial: 30%`
- **Forks** - Ansible uses parallel processes/forks to remote host. By default ansible can create 5 forks at a time.

### Error Handling

- `any_error_fatal: true`
- Email after finishing deployment
  
```yaml
- mail:
    to: devops@scp.com
    subject: Job deployed
    body: Successfull
 ignore_errors: yes

- command: cat /var/log/server.log
  register: command_output
  failed_when: "'ERROR' in command_output.stdout"
```

### Jinja2 Templating

- String Manipulation
  - `{{ os_ name }}`
  - `{{ os_ name | upper }}`
  - `{{ os_ name | lower }}`
  - `{{ os_ name | title }}`
  - `{{ os_ name | replace("Ubuntu", "CentOs") }}`
  - `{{ os_ name | default("Ubuntu")}} {{ "CentOs" }}`
- List and Set
  - `{{ [1,2,3] | min }}`
  - `{{ [1,2,3] | max }}`
  - `{{ [1,2,3,3,4] | unique }}`
  - `{{ [1,2,3,3,4] | union[2,3] }}`
  - `{{ [1,2,3,3,4] | intersect[3,5,6] }}`
  - `{{ 100 | random }}`
  - `{{ ["My", "name", "is", "Bond"] | join("") }}`
- Filters in file
  - `{{ "/etc/hosts" | basename }}`
  - `{{ "c:\windows\hosts" | win_basename }}`

### Lookup

- `{{ lookup('csvfile', 'targer_file=file/hosts.csv delimiter=;') }}`
- Lookup file - `ini`, `dns`, `mongodb` etc

### Anisble Vault

- `ansible-vault encrypt inventory`
- `ansible-playbook playbook.yaml -i inventory --ask-vault-pass`
- Store vault password in a file
  - `ansible-playbook playbook.yaml -i inventory --vault-password-file ./vault_pass.txt`
  - `ansible-playbook playbook.yaml -i inventory --vault-password-file ./vault_pass.py`
- `ansible-vault view inventory`
- `ansible-vault create inventory`

## Dynamic Inventory

- `ansible-playbook playbook.yaml -i inventory.py`

## Ansible Inventory Command

- `ansible-inventory --list -y`
