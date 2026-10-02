# Ansible Server Provisioning

Automates configuration of an Ubuntu EC2 instance using an Ansible
playbook, instead of manually SSHing in and running commands one at a
time (the approach used in the EC2 deployment project this builds on).

## What the playbook does

 targets a host in the  inventory group and:

- Updates the apt package cache
- Installs nginx, git, htop, and ufw
- Creates a non-root  user (added to the  group)
- Configures the  firewall to allow SSH and HTTP, then enables it
  with a deny-by-default policy
- Ensures nginx is running and enabled on boot

## Idempotency proof

The entire point of a configuration-management tool over ad-hoc shell
commands is that running the same playbook twice produces the same
result, with the second run doing nothing if the system already
matches the desired state.

**First run** — provisions everything from scratch:
ok=7 changed=5 unreachable=0 failed=0

**Second run**, no changes to the playbook or the server in between:
ok=7 changed=0 unreachable=0 failed=0

Every task correctly detected the target already matched the desired
state and made no further changes — proof the playbook converges
reliably rather than just re-running commands blindly.

## Usage

```bash
cp inventory.ini.example inventory.ini
# edit inventory.ini with your own host and key path

ansible all -i inventory.ini -m ping      # confirm connectivity
ansible-playbook -i inventory.ini site.yml
```
