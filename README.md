# Server Configuration Automation

Ansible playbooks that configure multiple Linux servers in one command: base packages, firewall, Nginx, and application dependencies. A new server goes from bare OS to a verified, running web server in minutes instead of manual step-by-step setup.

**Stack:** Ansible · Linux (Ubuntu/Debian) · Nginx · UFW · Vagrant (optional local lab)

## What it does

| Role | Tasks |
|------|-------|
| `common` | Updates apt cache, installs base packages, sets timezone, configures UFW firewall (SSH + HTTP only) |
| `nginx` | Installs Nginx, removes the default site, deploys a templated vhost with a `/health` endpoint, validates config (`nginx -t`), reloads only when config changes |
| `app` | Installs app dependencies (Python 3, pip, venv, build tools), creates the app directory, deploys a landing page showing hostname and OS |

A final **verify** play checks each server's `/health` endpoint, so a failed setup fails the run.

The playbooks are **idempotent**: running them again changes nothing unless something drifted.

## Layout

```
ansible.cfg
site.yml                 main playbook
requirements.yml         Ansible collections
inventory/hosts.ini      your servers
group_vars/all.yml       packages, ports, paths
roles/{common,nginx,app}
Vagrantfile              optional 2-VM test lab
```

## Usage

### 1. Install Ansible
```bash
pip install ansible        # full package includes community.general
ansible-galaxy collection install -r requirements.yml
```

### 2. Add your servers
Edit `inventory/hosts.ini` with your server IPs, SSH user and key. Servers must be Ubuntu/Debian with SSH access and a sudo user.

### 3. Test connectivity
```bash
ansible webservers -m ping
```

### 4. Run
```bash
ansible-playbook site.yml                # everything
ansible-playbook site.yml --check --diff # dry run
ansible-playbook site.yml --tags nginx   # one role only
ansible-playbook site.yml --limit web1   # one server only
```

### Local lab (no cloud needed)
```bash
vagrant up                # creates web1 (192.168.56.11) and web2 (192.168.56.12)
```
Then set `ansible_ssh_private_key_file` in the inventory to the Vagrant key (`vagrant ssh-config` shows the path) and run the playbook. Visit `http://192.168.56.11` in a browser.

## Measuring the time savings
Time a manual setup of one server (install packages, configure firewall, install and configure Nginx, deploy files). Then time the playbook across all servers:
```bash
time ansible-playbook site.yml
```
Put your own measured numbers here.
