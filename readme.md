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

# Ansible + Raspberry Pi: Guia de Estudo

Este repositório demonstra como gerenciar um Raspberry Pi usando Ansible a partir de um ambiente local.

## 1. Estrutura do Projeto

- `ansible.cfg`: Configuração do Ansible.
- `hosts.yaml`: Inventário no formato YAML.
- `.env`: (opcional) Armazena senha SSH para uso com sshpass.

## 2. Inventário de Hosts (hosts.yaml)
Exemplo:
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

## 3. Configuração do Ansible (ansible.cfg)
Exemplo:
```ini
[defaults]
host_keychecking = False
#remote_user = pauloaragao
# Para uso de senha via .env e sshpass:
# 1. Crie um arquivo .env com SSHPASS=sua_senha
# 2. Execute: set -a; source .env; set +a; sshpass -e ansible ...
```

## 4. Autenticação SSH

- **Com senha:**
	- Use `sshpass` e um arquivo `.env` com `SSHPASS=sua_senha`.
	- Exemplo de comando:
		```sh
		set -a; source .env; set +a; sshpass -e ansible raspberry -i hosts.yaml -m ping
		```
- **Com chave SSH:**
	- Configure `private_key_file` no `ansible.cfg` e garanta que a chave está autorizada no Raspberry Pi.

## 5. Comandos Úteis

- **Ping:**
	```sh
	ansible raspberry -i hosts.yaml -m ping
	```
- **Reboot (necessita sudo):**
	```sh
	ansible raspberry -i hosts.yaml -m reboot -b --ask-become-pass
	```
- **Instalar pacote (necessita sudo):**
	```sh
	ansible raspberry -i hosts.yaml -m apt -a "name=vim state=present" -b --ask-become-pass
	```
- **Gerenciar serviços:**
	- Iniciar:
		```sh
		ansible all -i hosts.yaml -m service -a "name=nginx state=started" -b --ask-become-pass
		```
	- Parar:
		```sh
		ansible all -i hosts.yaml -m service -a "name=nginx state=stopped" -b --ask-become-pass
		```

## 6. Dicas e Troubleshooting

- Sempre use `-b` para comandos que exigem root (apt, reboot, service, etc).
- Se receber erro de inventário, confira o caminho e formato do arquivo (`hosts.yaml`).
- Para problemas com repositórios APT, revise as URLs em `/etc/apt/sources.list` e `/etc/apt/sources.list.d/` no Raspberry Pi.
- Para autenticação SSH, prefira chaves ao invés de senha para automação.

---
Documentação criada para fins de estudo e automação de Raspberry Pi com Ansible.