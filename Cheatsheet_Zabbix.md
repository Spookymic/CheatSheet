# Cheatsheet Zabbix

## Généralités
- Solution de supervision open source (plus de 100k+ installations).
- Composants : **Server**, **Agent** (sur les hôtes), **Proxy** (relais distribué), **Frontend web**, **Database** (MySQL/PostgreSQL).
- Modèle de données : **Host → Templates → Items → Triggers → Actions**.
- Notifications : emails, webhooks, scripts, intégration (Slack, Telegram...).
- Ports : `10050` (agent écoute), `10051` (server écoute), `443/8080` (frontend).

## Concepts
| Concept | Rôle |
|---|---|
| **Host** | machine ou équipement supervisé |
| **Template** | modèle réutilisable d'items/triggers |
| **Item** | collecte d'une métrique (une valeur) |
| **Trigger** | condition qui déclenche un problème |
| **Problem / Recovery** | problème ouvert / résolu |
| **Action** | réaction à un problème (notification, commande) |
| **Discovery** | auto-découverte d'entités (disques, services...) |
| **Maintenance** | période sans alertes (planifiée) |
| **Proxy** | relais pour sites distants |
| **Media type** | canal de notification (email, script...) |

## CLI / API
| Commande | Effet |
|---|---|
| `zabbix_agentd -t system.cpu.load[avg1]` | tester un item sur l'agent |
| `zabbix_agentd -p` | afficher les items disponibles |
| `zabbix_sender -z IP_SERVER -s host -k cle -o valeur` | envoyer une valeur (agent actif) |
| `zabbix_get -s IP_AGENT -k system.uptime` | lire une valeur depuis le serveur |
| `systemctl status zabbix-server` | état du serveur |
| `systemctl restart zabbix-server` | redémarrer le serveur |
| `curl -s -X POST http://IP/api_jsonrpc.php -d '{"jsonrpc":"2.0","method":"apiinfo.version","params":{},"id":1}'` | version API |
| API token | Frontend → Users → API tokens |

### Exemple API (authentification)
```bash
TOKEN=$(curl -s -X POST http://IP/api_jsonrpc.php \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","method":"user.login","params":{"username":"Admin","password":"zabbix"},"id":1}' | jq -r .result)

curl -s -X POST http://IP/api_jsonrpc.php \
  -H "Content-Type: application/json" \
  -d "{\"jsonrpc\":\"2.0\",\"method\":\"host.get\",\"params\":{\"output\":[\"hostid\",\"host\"]},\"auth\":\"$TOKEN\",\"id\":1}"
```

## Clés agent (items courants)
| Clé | Signification |
|---|---|
| `system.cpu.load[avg1]` | charge CPU (1 min) |
| `system.cpu.util[,iowait]` | % d'attente E/S |
| `system.uptime` | uptime |
| `system.hostname` | hostname |
| `system.run[commande]` | exécuter une commande (actif uniquement) |
| `vm.memory.util` | % mémoire utilisée |
| `vfs.fs.size[/,pfree]` | % espace libre du disque `/` |
| `vfs.fs.size[/,used]` | espace utilisé |
| `vfs.fs.discovery` | découverte des points de montage |
| `net.if.in[eth0]` / `net.if.out[eth0]` | octets reçus / émis |
| `agent.ping` | test agent (1 si actif) |
| `system.users.num` | utilisateurs connectés |
| `web.page.get[URL]` | HTTP GET (statut) |
| `perf_counter["\Processo(...)"]` | compteurs Windows |

## Triggers (exemples)
| Expression | Signification |
|---|---|
| `{Template OS Linux:system.cpu.load[avg1].last()}>5` | charge > 5 |
| `{Host:vm.memory.util[].last()}>90` | mémoire > 90 % |
| `{Host:vfs.fs.size[/,pfree].last()}<10` | moins de 10 % libres sur `/` |
| `{Host:net.if.in[eth0].last()} > 100000000` | débit entrant > 100 Mo/s |
| `{Host:system.uptime.last()}<3600` | redémarrage récent |
| `{Host:agent.ping.nodata(300)}=1` | pas de données depuis 300 s (hôte down) |

Sévérités : Info → Warning → Average → High → Disaster.
Durées : `last()`, `avg(1m)`, `min(5m)`, `max(1h)`, `nodata(3m)`, `diff()`, `change()`.

## Exemples de templates intégrés
| Template | Usage |
|---|---|
| `Template OS Linux by Zabbix agent` | supervision Linux complète |
| `Template OS Windows by Zabbix agent` | supervision Windows |
| `Template Network Generic Device SNMPv2` | équipements réseau (SNMP) |
| `Template DB MySQL` / `PostgreSQL` | bases de données |
| `Template App Nginx` / `Apache HTTP Server` | serveurs web |
| `Template VM VMware` | supervision vSphere |
| `Template HTTP Service` | monitoring HTTP |

## Installation (Debian/Ubuntu, serveur)
```bash
# Dépôt officiel
wget https://repo.zabbix.com/zabbix/7.0/debian/pool/main/z/zabbix-release/zabbix-release_latest+debian12_all.deb
dpkg -i zabbix-release_latest+debian12_all.deb
apt update

# Serveur + frontend + base (MySQL)
apt install zabbix-server-mysql zabbix-frontend-php zabbix-nginx-conf zabbix-sql-scripts zabbix-agent2
```
1. Créer la base : `mysql -uroot -p -e "CREATE DATABASE zabbix CHARACTER SET utf8mb4; CREATE USER 'zabbix'@'localhost' IDENTIFIED BY 'mdp'; GRANT ALL ON zabbix.* TO 'zabbix'@'localhost';"`
2. Importer le schéma : `zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql -uzabbix -p zabbix`
3. Configurer `/etc/zabbix/zabbix_server.conf` (DBPassword), puis `systemctl restart zabbix-server zabbix-agent2 nginx php8.x-fpm`.
4. Ouvrir `http://IP/zabbix` et suivre l'assistant (utilisateur `Admin` / mot de passe par défaut `zabbix`).

## Installation agent (Linux)
```bash
apt install zabbix-agent
# /etc/zabbix/zabbix_agentd.conf
#   Server=IP_SERVEUR
#   ServerActive=IP_SERVEUR
#   Hostname=nom_hote
systemctl restart zabbix-agent
```

## Installation agent (Windows)
| Étape | Détail |
|---|---|
| 1 | Télécharger `zabbix_agent-<version>-windows-amd64.zip` |
| 2 | Décompresser dans `C:\zabbix` |
| 3 | `zabbix_agentd.conf` : `Server=IP_SERVEUR`, `Hostname=nom` |
| 4 | `zabbix_agentd.exe -c C:\zabbix\conf\zabbix_agentd.conf -i` (installer le service) |
| 5 | `zabbix_agentd.exe -c ... -s` (démarrer) |

## Supervision réseau (SNMP)
| Élément | Détail |
|---|---|
| Host | actif avec interface SNMP (port 161) |
| Template | `Template Network Generic Device SNMPv2` |
| Config | renseigner la communauté / version v3 dans l'interface |
| Items | `snmp.get[oid]`, `snmp_oid[...]` |
| Vérifier | `snmpwalk -v2c -c public IP .1.3.6.1.2.1` sur l'équipement |

## Notifications (Actions + Media types)
| Élément | Rôle |
|---|---|
| **Media type** | email (SMTP), script, Webhook (Slack/Telegram), OpsGenie... |
| **User** | associer un *media* (adresse email, token) |
| **Action** | conditions (trigger, host, tag) → opérations (message, escalade) |
| **Escalation** | relance si problème non résolu |
| Tag | filtrer les actions par tags (ex. `class:network`) |

## Proxy (distribution)
| Commande / étape | Détail |
|---|---|
| `apt install zabbix-proxy-sqlite3` | installer le proxy (base locale) |
| `/etc/zabbix/zabbix_proxy.conf` | `Server=IP_SERVEUR`, `Hostname=proxy1` |
| `systemctl restart zabbix-proxy` | démarrer |
| Frontend | *Administration → Proxies → Create proxy* |
| Hosts distants | affecter au proxy au lieu du serveur |

## Dépannage
| Problème | Vérification |
|---|---|
| Hôte `Unreachable` | pare-feu port 10050, `zabbix_get -s IP -k agent.ping` |
| Items `Not supported` | syntaxe de clé, agent actif/passif, droits |
| Pas de données | `zabbix_agentd -t clé`, logs `/var/log/zabbix/zabbix_agentd.log` |
| Server ne tourne pas | `journalctl -u zabbix-server`, base MySQL ok ? |
| Pas d'emails | tester le Media type, vérifier l'action, erreurs dans les logs serveur |
| Données non stockées | vérifier les *housekeeping* / paramètres de rétention |
