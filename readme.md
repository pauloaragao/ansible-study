# Ansible Study Project

## What is Ansible?
Ansible is an open-source automation tool for IT tasks such as configuration management, application deployment, and orchestration. It uses simple YAML files (playbooks) to describe automation jobs and connects to remote machines via SSH, making it agentless and easy to use.

## Project Structure
This repository demonstrates how to use Ansible to manage a Raspberry Pi from a local environment. The main files and folders are:

- `ansible.cfg`: Ansible configuration file.
- `hosts.yaml` or `hosts`: Inventory file listing managed hosts.
- `playbook.yaml`: Main playbook with tasks for user creation, SSH setup, nginx installation, and static page deployment.
- `.env`: (optional) Stores SSH password for use with sshpass.
- `index.html`, `nginx.conf`, `id_rsa.pub`, `aula.pub`: Example files used in playbooks and tasks.
- `variaveis.yaml` or `vars.yaml`: Variables file for playbooks.

## How to Use
1. Configure your inventory file (`hosts.yaml` or `hosts`) with the IP address and user of your Raspberry Pi.
2. Edit `ansible.cfg` to set defaults (user, key checking, etc).
3. Place all required files (public keys, configs, HTML) in the project directory.
4. Run playbooks using:
	```bash
	ansible-playbook -i hosts playbook.yaml --extra-vars="@variaveis.yaml" --ask-become-pass
	```
5. Adjust variables in `variaveis.yaml` as needed for your environment.

## Notes
- All tasks requiring root privileges use `become: true`.
- SSH authentication can be done via password (`sshpass` + `.env`) or SSH key.
- The playbooks are modular and can be extended for other services or configurations.

---


# Ansible + Raspberry Pi: Study Guide

This repository demonstrates how to manage a Raspberry Pi using Ansible from a local environment.

## 1. Project Structure

- `ansible.cfg`: Ansible configuration file.
- `hosts.yaml`: Inventory in YAML format.
- `.env`: (optional) Stores SSH password for use with sshpass.

## 2. Hosts Inventory (hosts.yaml)
Example:
```yaml
all:
	hosts:
		192.168.1.21:
	vars:
		ansible_user: pauloaragao
	children:
		raspberry:
			hosts:
				192.168.1.21:
			vars:
				ansible_user: pauloaragao
```

## 3. Ansible Configuration (ansible.cfg)
Example:
```ini
[defaults]
host_keychecking = False
#remote_user = pauloaragao
# For password usage via .env and sshpass:
# 1. Create a .env file with SSHPASS=your_password
# 2. Run: set -a; source .env; set +a; sshpass -e ansible ...
```

## 4. SSH Authentication

- **With password:**
	- Use `sshpass` and a `.env` file with `SSHPASS=your_password`.
	- Example command:
		```sh
		set -a; source .env; set +a; sshpass -e ansible raspberry -i hosts.yaml -m ping
		```
- **With SSH key:**
	- Set up `private_key_file` in `ansible.cfg` and ensure the key is authorized on the Raspberry Pi.

## 5. Useful Commands

- **Ping:**
	```sh
	ansible raspberry -i hosts.yaml -m ping
	```
- **Reboot (requires sudo):**
	```sh
	ansible raspberry -i hosts.yaml -m reboot -b --ask-become-pass
	```
- **Install package (requires sudo):**
	```sh
	ansible raspberry -i hosts.yaml -m apt -a "name=vim state=present" -b --ask-become-pass
	```
- **Manage services:**
	- Start:
		```sh
		ansible all -i hosts.yaml -m service -a "name=nginx state=started" -b --ask-become-pass
		```
	- Stop:
		```sh
		ansible all -i hosts.yaml -m service -a "name=nginx state=stopped" -b --ask-become-pass
		```

## 6. Tips and Troubleshooting

- Always use `-b` for commands that require root (apt, reboot, service, etc).
- If you get an inventory error, check the path and format of the file (`hosts.yaml`).
- For APT repository issues, review the URLs in `/etc/apt/sources.list` and `/etc/apt/sources.list.d/` on the Raspberry Pi.
- For SSH authentication, prefer keys over passwords for automation.

---
Documentation created for study and automation of Raspberry Pi with Ansible.