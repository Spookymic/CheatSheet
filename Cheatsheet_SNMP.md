# Cheatsheet SNMP

## Généralités
- **SNMP** = *Simple Network Management Protocol* : supervision d'équipements (routeurs, switchs, serveurs, imprimantes).
- Architectures : **Agent** (équipement supervisé) et **Manager** (station de supervision, ex. Nagios, Zabbix, PRTG).
- Fonctionne en **UDP** (majoritairement) : port **161** (requêtes) et port **162** (traps / notifications).
- **MIB** (*Management Information Base*) : base de données des objets supervisables, identifiés par un **OID** (*Object Identifier*).
- **OID** : arborescence numérique, ex. `1.3.6.1.2.1.1.1.0`.

## Versions SNMP
| Version | Communauté | Chiffrement | Authentification | Usage |
|---|---|---|---|---|
| v1 | texte clair | non | non | obsolète |
| v2c | texte clair (community string) | non | non | la plus répandue, usage LAN de confiance |
| v3 | utilisateur + mots de passe | oui (DES/AES) | oui (SHA/MD5) | recommandée, environnement non sécurisé |

Arborescence de l'OID `1.3.6.1.2.1.1.1.0` :
| Segment | Valeur | Signification |
|---|---|---|
| 1.3.6.1 | | racine ISO/Org/DOD/Internet |
| 2.1 | | mgmt (gestion) |
| 1.1 | | system |
| 1.1.1 | | sysDescr |
| 0 | | instance 0 |

## Commandes Net-SNMP (Linux)
| Commande | Effet |
|---|---|
| `snmpget -v2c -c public IP OID` | lire une valeur précise |
| `snmpgetnext -v2c -c public IP OID` | lire la valeur suivante |
| `snmpwalk -v2c -c public IP OID` | parcourir toute une branche (ex. `.1.3.6.1.2.1`) |
| `snmpbulkget -v2c -c public IP OID` | lecture en masse (plus rapide) |
| `snmpbulkwalk -v2c -c public IP OID` | parcours en masse |
| `snmpset -v2c -c private IP OID type valeur` | écrire une valeur (souvent réservée) |
| `snmptrap -v2c -c public IP "" OID` | envoyer un trap |
| `snmptrapd -f -Lo` | lister les traps reçus (mode démon) |
| `snmpstatus -v2c -c public IP` | état général de l'équipement |
| `snmpget -v3 -l authPriv -u user -a SHA -A motdepasse -x AES -X cle IP OID` | lecture en v3 chiffrée |

Options courantes :
| Option | Effet |
|---|---|
| `-v1` / `-v2c` / `-v3` | version SNMP |
| `-c community` | communauté (v1/v2c) |
| `-u user` | utilisateur (v3) |
| `-l authPriv` / `authNoPriv` / `noAuthNoPriv` | niveau de sécurité (v3) |
| `-a SHA` / `MD5` | algorithme d'authentification (v3) |
| `-A pass` | mot de passe d'authentification (v3) |
| `-x AES` / `DES` | algorithme de chiffrement (v3) |
| `-X pass` | clé de chiffrement (v3) |
| `-t timeout` | délai d'attente (secondes) |
| `-r retries` | nombre de tentatives |
| `-m MIB` | charger une MIB spécifique |
| `-On` | afficher les OID numériques |

## OID courants (MIB-II, 1.3.6.1.2.1)
| OID | Objet | Signification |
|---|---|---|
| `.1.3.6.1.2.1.1.1.0` | sysDescr | description de l'équipement |
| `.1.3.6.1.2.1.1.2.0` | sysObjectID | identifiant constructeur |
| `.1.3.6.1.2.1.1.3.0` | sysUpTime | temps de fonctionnement (centièmes de s) |
| `.1.3.6.1.2.1.1.4.0` | sysContact | contact |
| `.1.3.6.1.2.1.1.5.0` | sysName | nom de l'équipement |
| `.1.3.6.1.2.1.1.6.0` | sysLocation | localisation |
| `.1.3.6.1.2.1.2.1.0` | ifNumber | nombre d'interfaces |
| `.1.3.6.1.2.1.2.2.1.2` | ifDescr | description des interfaces |
| `.1.3.6.1.2.1.2.2.1.10` | ifInOctets | octets reçus |
| `.1.3.6.1.2.1.2.2.1.16` | ifOutOctets | octets émis |
| `.1.3.6.1.2.1.2.2.1.20` | ifOperStatus | état opérationnel (1=up, 2=down) |
| `.1.3.6.1.2.1.4.20.1.1` | ipAdEntAddr | adresses IP |
| `.1.3.6.1.2.1.25.1.1.0` | hrSystemUptime | uptime système |
| `.1.3.6.1.2.1.25.2.3.1.6` | hrStorageUsed | stockage utilisé |

Traps standard (v1) :
| Valeur | Trap | Signification |
|---|---|---|
| 0 | coldStart | équipement redémarré |
| 1 | warmStart | reconfiguration |
| 2 | linkDown | interface tombée |
| 3 | linkUp | interface remontée |
| 4 | authenticationFailure | échec d'authentification (mauvaise communauté) |
| 5 | egpNeighborLoss | perte d'un voisin EGP |

## Configuration agent Linux (net-snmp)
Fichier : `/etc/snmp/snmpd.conf`
```ini
# v2c
rocommunity public 127.0.0.1        # lecture seule depuis le localhost
rocommunity public 192.168.1.0/24   # lecture seule depuis le LAN
rwcommunity private                 # lecture/écriture (à éviter)

# v3 (authentification + chiffrement)
createUser snmpuser SHA "authpass" AES "privpass"
rouser snmpuser authPriv

# Traps
trap2sink 192.168.1.100 public      # envoyer les traps vers le manager
```
Relancer : `sudo systemctl restart snmpd`.
Test : `snmpwalk -v2c -c public localhost system`.

## Configuration Cisco
```text
snmp-server community public RO          ! communauté lecture seule
snmp-server community private RW         ! communauté lecture/écriture
snmp-server location "Salle serveurs"    ! localisation
snmp-server contact admin@domaine.fr     ! contact
snmp-server host 192.168.1.100 version 2c public   ! envoyer les traps
snmp-server enable traps                 ! activer les traps
snmp-server user snmpuser v3 auth sha "authpass" priv aes 128 "privpass"
show snmp                                ! vérifier la configuration
```

## Supervision Windows
| Étape | Action |
|---|---|
| 1 | Installer la fonctionnalité *SNMP* (serveur) |
| 2 | Services → SNMP → Propriétés → *Agent* (contact, localisation) |
| 3 | Onglet *Sécurité* : ajouter les noms de communauté reçus et les hôtes autorisés |
| 4 | Vérifier : `snmpwalk -v2c -c public IP` |
| | OID Windows : `.1.3.6.1.2.1.1` (system), `.1.3.6.1.4.1.311` (Microsoft) |

## Dépannage
| Commande | Rôle |
|---|---|
| `snmpwalk -v2c -c public -t 2 -r 3 IP .1` | test global, timeout augmenté |
| `snmpget -v2c -c wrongcom IP .1.3.6.1.2.1.1.1.0` | vérifier la communauté (échec attendu) |
| `netstat -an \| findstr :161` / `ss -ulpn` | port 161 ouvert ? |
| `tcpdump -i eth0 udp port 161` | capturer le trafic SNMP |
| `snmpget -On` | confirmer qu'un OID existe (évite la résolution de noms) |

## Bonnes pratiques
- Toujours utiliser **SNMPv3** dès que possible (communauté v2c lisible sur le réseau).
- Changer les communautés par défaut `public` / `private`.
- Limiter les sources autorisées (ACL / pare-feu) au réseau de supervision.
- Imposer un `rocommunity` (lecture seule) par défaut, `rw` uniquement si nécessaire.
- Superviser via **SNMPv3 + AES/SHA** pour les données sensibles.
- Surveiller les traps `authenticationFailure` : elles signalent une tentative d'accès.
