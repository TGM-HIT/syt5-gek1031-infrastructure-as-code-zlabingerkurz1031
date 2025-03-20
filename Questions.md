# Fragestellungen

## Was versteht man unter Infrastructure as Code und welche Vorteile bietet es im Vergleich zur manuellen Infrastrukturkonfiguration?

- Bei Infrastructure as Code wird das manuelle aufsetzen  und in stand haltung von IT-Infrastruktur (Server, Datenbanken, …) auf mit Code ersetzt
- Erhoeht Konstitenz und replizierbarkeit aufgrund von der abwesenheit von Menschlichen Fehlern

## Nennen Sie Beispiele für IaC-Tools und erklären Sie, warum Ansible besonders für Configuration Management geeignet ist.

### Terraform

- **Business Source**: Kommerziell von HashiCorp unterstützt.
- **HCL**: Nutzt die deklarative Sprache HCL (HashiCorp Configuration Language) zur Definition der Infrastruktur.
- **API-abhängig**: Benötigt eine API-Verbindung zur Zielinfrastruktur (z. B. Cloud-Anbieter).
- **Besser für Day 0**: Besonders gut für den initialen Aufbau von Infrastruktur (z. B. Erstellen von VMs, Netzwerken, etc.).
- **Zustandsverwaltung**: Verfolgt den Zustand der Infrastruktur und sorgt dafür, dass die Ressourcen immer mit der gewünschten Konfiguration übereinstimmen.

### Ansible

- **Open Source**: Kostenlos und von der Community sowie Red Hat unterstützt.
- **YAML**: Verwendet YAML, eine einfache, menschenlesbare Sprache für Konfigurationsmanagement.
- **Agentenlos**: Arbeitet ohne zusätzliche Agenten durch direkte SSH-Verbindungen.
- **Besser für Day 1**: Ideal für die laufende Verwaltung, Konfiguration und Aktualisierung von Anwendungen und Systemen.
- **Keine Zustandsverwaltung**: Fokussiert auf das Ausführen von Aufgaben und nicht auf die Überwachung des Systemzustands.

### 

## Welche Rolle spielen Idempotenz und Deklarativität in IaC? Wie setzt Ansible diese Konzepte um?

### Idempotenz

Erkennt redundante aenderungen in skripts und fuehrt diese nicht aus →

```yaml
- name: Installiere Nginx
  apt:
    name: nginx
    state: present
```

### Deklarativität

Gibt nicht die Schritte sondern nur das Endergebnis an

```yaml
- name: Stelle sicher, dass der Nginx-Dienst läuft
  service:
    name: nginx
    state: started
```

## Erklären Sie den Aufbau eines Ansible Playbooks anhand eines Beispiels. Welche Schlüsselkomponenten (Tasks, Modules, Handlers) sind essenziell?

- **Hosts**: Definiert die Zielmaschinen, auf denen das Playbook ausgeführt wird.
- **Tasks**: Beschreibt die Aufgaben, die auf den Zielmaschinen ausgeführt werden sollen. Jede Task nutzt ein **Modul**, um eine spezifische Aktion durchzuführen
- **Modules**: Vorgefertigte Funktionen, die die Tasks ausführen. Beispiel: `yum` für das Installieren von Paketen, `service` für das Verwalten von Diensten.
- **Handlers**: Besondere Tasks, die nur ausgeführt werden, wenn sie durch eine andere Task benachrichtigt werden (z. B. Neustarten eines Dienstes, wenn eine Datei geändert wurde).

```yaml
---
- hosts: group1
  tasks:
    - name: Install lldpad package
      yum:
        name: lldpad
        state: latest
      notify: Restart lldpad service

    - name: check lldpad service status
      service:
        name: lldpad
        state: started

  handlers:
    - name: Restart lldpad service
      service:
        name: lldpad
        state: restarted
```

## Wie funktioniert die Inventarisierung in Ansible? Welche Unterschiede gibt es zwischen statischen und dynamischen Inventaren?

Die Inventarisierung ist die Sammlung der Hosts, die von dem Playbook verwendet werden.

### Statisches Inventar

Manuelle statische Auflistung der Hosts in einer Text Datei.

```yaml
[webserver]
web1.example.com
web2.example.com

[database]
db1.example.com
db2.example.com
```

### Dynamisches Inventar

Skript das in der Laufzeit nach allen verfuegbaren Hosts sucht und diese zur Host-Liste hinzufuegt.

```yaml
ansible-playbook -i ec2.py my_playbook.yml
```

## Was sind Ansible Roles und wie fördern sie die Wiederverwendbarkeit von Code?

Ein Verzeichnis das Sammlungen von Tasks, Handerls, etc., die zusammen eine bestimmte Aufgabe ausfuehren, gruppiert. Role existiert extern von Playbook → kann einfach ausgetauscht oder in einen anderen Playbook verwendet werden.

## Welche Vor- und Nachteile hat Ansible gegenüber Terraform bei der Infrastrukturbereitstellung? Kann man diese beiden Tools überhaupt vergleichen?

### Terraform

- **Business Source**: Kommerziell von HashiCorp unterstützt.
- **HCL**: Nutzt die deklarative Sprache HCL (HashiCorp Configuration Language) zur Definition der Infrastruktur.
- **API-abhängig**: Benötigt eine API-Verbindung zur Zielinfrastruktur (z. B. Cloud-Anbieter).
- **Besser für Day 0**: Besonders gut für den initialen Aufbau von Infrastruktur (z. B. Erstellen von VMs, Netzwerken, etc.).
- **Zustandsverwaltung**: Verfolgt den Zustand der Infrastruktur und sorgt dafür, dass die Ressourcen immer mit der gewünschten Konfiguration übereinstimmen.

### Ansible

- **Open Source**: Kostenlos und von der Community sowie Red Hat unterstützt.
- **YAML**: Verwendet YAML, eine einfache, menschenlesbare Sprache für Konfigurationsmanagement.
- **Agentenlos**: Arbeitet ohne zusätzliche Agenten durch direkte SSH-Verbindungen.
- **Besser für Day 1**: Ideal für die laufende Verwaltung, Konfiguration und Aktualisierung von Anwendungen und Systemen.
- **Keine Zustandsverwaltung**: Fokussiert auf das Ausführen von Aufgaben und nicht auf die Überwachung des Systemzustands.

## Wie lässt sich Ansible in eine CI/CD-Pipeline (z. B. GitLab CI, Jenkins) integrieren?

GitLab verwendet eine `.gitlab-ci.yml`-Datei, um die CI/CD-Pipeline zu definieren. In dieser Datei legst du fest, welche Jobs (Tasks) ausgeführt werden sollen und in welcher Reihenfolge.

```yaml
stages:
  - build
  - deploy

build:
  stage: build
  script:
    - echo "Build-Phase läuft..."  # Hier könnten Build-Tasks definiert werden

deploy:
  stage: deploy
  script:
    - echo "Start der Bereitstellung mit Ansible..."
    - ansible-playbook -i inventory/hosts playbooks/deploy.yml
  only:
    - main  # Nur auf dem Hauptbranch ausführen
```

- **`stages:`**: Definiert die Phasen der Pipeline (z. B. Build, Test, Deploy).
- **`script:`**: Der Befehl, der innerhalb eines Jobs ausgeführt wird. Hier wird Ansible verwendet, um das Playbook auszuführen.
- **`only:`**: Gibt an, dass dieser Job nur auf einem bestimmten Branch (hier `main`) ausgeführt wird.

# Quellen

https://www.redhat.com/de/topics/automation/what-is-infrastructure-as-code-iac

https://spacelift.io/blog/ansible-vs-terraform

https://www.guru99.com/ansible-tutorial.html
