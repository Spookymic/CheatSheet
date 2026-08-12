# Cheatsheet Ansible

## Généralités
- Agentless : fonctionne en **SSH** (Linux) / **WinRM** (Windows), aucun agent à installer.
- Inventaire : liste des machines cibles (`/etc/ansible/hosts` ou `inventory.ini`).
- **Playbook** : fichier YAML de tâches à exécuter (idempotentes).
- **Module** : brique unitaire (`apt`, `copy`, `service`, `user`...).
- **Handler** : tâche déclenchée par un `notify` (ex. redémarrer un service).
- Variables : définies dans le playbook, les fichiers `vars/`, les `group_vars`/`host_vars`.
- **Ansible Vault** : chiffrement des secrets.

## Inventaire
```ini
[webservers]
web1.example.com
web2.example.com ansible_user=admin

[dbservers]
db1.example.com

[all:vars]
ansible_python_interpreter=/usr/bin/python3
```
| Option | Effet |
|---|---|
| `ansible_host=IP` | IP de connexion si nom DNS différent |
| `ansible_user=user` | utilisateur SSH |
| `ansible_port=2222` | port SSH |
| `ansible_ssh_private_key_file=~/.ssh/id_ed25519` | clé privée |

## Commandes ad-hoc
| Commande | Effet |
|---|---|
| `ansible all -i inventory.ini -m ping` | tester la connexion |
| `ansible all -m setup` | facts de la machine |
| `ansible webservers -m apt -a "name=nginx state=latest" -b` | installer nginx |
| `ansible all -m command -a "uptime"` | exécuter une commande |
| `ansible all -m shell -a "df -h"` | shell (redirections, pipes) |
| `ansible all -m copy -a "src=fichier dest=/tmp/fichier"` | copier un fichier |
| `ansible all -m service -a "name=nginx state=restarted" -b` | redémarrer un service |
| `ansible all -m user -a "name=alice state=present groups=sudo" -b` | créer un utilisateur |
| `ansible all -m yum` / `-m dnf` | paquets RHEL / Fedora |
| `ansible all -m win_ping` | tester une machine Windows |

Options : `-b` (sudo), `-K` (demander le mot de passe sudo), `-u user`, `-i inventory`, `--become-user=`, `--limit=web1` (restreindre), `-v` / `-vvv` (verbosité).

## Playbook exemple
```yaml
---
- name: Installer et configurer nginx
  hosts: webservers
  become: true
  vars:
    site_port: 8080
  tasks:
    - name: Installation
      apt:
        name: nginx
        state: latest

    - name: Copie de la configuration
      template:
        src: nginx.conf.j2
        dest: /etc/nginx/nginx.conf
      notify: Redemarrer nginx

    - name: Démarrage du service
      service:
        name: nginx
        state: started
        enabled: true

  handlers:
    - name: Redemarrer nginx
      service:
        name: nginx
        state: restarted
```

## Boucles, conditions, handlers
```yaml
- name: Créer plusieurs utilisateurs
  user:
    name: "{{ item }}"
    state: present
  loop:
    - alice
    - bob

- name: Action conditionnelle
  command: systemctl start httpd
  when: ansible_os_family == "RedHat"

- name: Arrêter si déjà fait (idempotence)
  command: /usr/bin/mkdir -p /opt/app
  args:
    creates: /opt/app
  register: result
```

## Rôles (structure standard)
```
roles/
└── nginx/
    ├── tasks/main.yml     # tâches principales
    ├── handlers/main.yml  # handlers
    ├── templates/         # fichiers .j2
    ├── files/             # fichiers statiques
    ├── vars/main.yml      # variables du rôle
    ├── defaults/main.yml  # variables par défaut
    └── meta/main.yml      # métadonnées (dépendances)
```
Dans un playbook : `roles: [{ role: nginx, site_port: 8080 }]` ou l'arborescence `roles/`.

## Ansible Vault
| Commande | Effet |
|---|---|
| `ansible-vault create secret.yml` | créer un fichier chiffré |
| `ansible-vault encrypt fichier.yml` | chiffrer un fichier |
| `ansible-vault decrypt fichier.yml` | déchiffrer |
| `ansible-vault edit fichier.yml` | éditer un fichier chiffré |
| `ansible-vault view fichier.yml` | visualiser |
| `ansible-playbook play.yml --ask-vault-pass` | utiliser un vault dans un playbook |

## Dépannage
| Commande | Effet |
|---|---|
| `ansible-playbook play.yml --check` | mode dry-run |
| `ansible-playbook play.yml --syntax-check` | vérifier la syntaxe YAML |
| `ansible-playbook play.yml -v` / `-vvv` | verbosité croissante |
| `ansible-playbook play.yml --list-tasks` | lister les tâches |
| `ansible-playbook play.yml --tags=x` | exécuter uniquement les tâches taguées |
| `ansible-playbook play.yml --start-at-task="Tâche"` | reprendre à une tâche |
