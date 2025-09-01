
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