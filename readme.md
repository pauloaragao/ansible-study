# Como acessar SSH
Para colocar senha do SSH
-b --ask-become-pass

# Service Ansible
Como enviar um serviço.

ansible all -i hosts -m service -a "name=nginx state=started" -b --ask-become-pass

ansible all -i hosts -m service -a "name=nginx state=stopped" -b --ask-become-pass