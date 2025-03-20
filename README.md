# Projektübersicht

## Anforderungen

- Ansible muss installiert sein.
- Die Hosts müssen erreichbar sein und die erforderlichen Pakete installieren können.

## Schritte zur Ausführung

1. **Ansible installieren**:
```bash
sudo apt install ansible
```


2. **Playbooks ausführen**:
* Loadbalancer konfigurieren
```bash
ansible-playbook -i inventory.ini loadbalancer/playbook.yml
```

* Webservers konfigurieren
```bash
ansible-playbook -i inventory.ini usecase/playbook.yml
```

## Funktionsweise

- **Loadbalancer**:
- Installiert Nginx.
- Konfiguriert Nginx als Loadbalancer für die Webservers.
- Startet den Nginx-Dienst.

- **Webservers**:
- Installiert Apache und PHP.
- Aktiviert Apache und PHP.
- Erstellt ein PHP-Skript, das die Server-IP anzeigt.
- Startet den Apache-Dienst neu.

# Resources
https://docs.ansible.com/ansible/latest/getting_started/index.html

https://docs.ansible.com/ansible/latest/collections/community/general/npm_module.html
