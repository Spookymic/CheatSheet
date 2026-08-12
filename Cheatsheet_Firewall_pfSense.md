# Cheatsheet Firewall / pfSense

## Généralités
- **pfSense** : distribution pare-feu open source basée sur FreeBSD et **pf**.
- Interface web : `https://IP` (login `admin` / mot de passe défini à l'installation).
- **OPNsense** : fork de pfSense, même philosophie.
- Règles évaluées dans l'ordre ; la **première règle correspondante** s'applique.
- Implicitement tout est **deny** s'il n'y a pas de règle correspondante.

## Interfaces et zones
| Élément | Rôle |
|---|---|
| WAN | vers Internet (réseau non fiable) |
| LAN | réseau local (fiable) |
| OPT1/OPT2... | zones supplémentaires (DMZ, VPN...) |
| VLAN | segmentation virtuelle (802.1Q) |

## Règles de pare-feu (Firewall → Rules)
| Paramètre | Détail |
|---|---|
| Action | Pass / Block / Reject |
| Protocol | TCP / UDP / ICMP / any |
| Source | adresse / réseau / alias |
| Destination | adresse / port / alias |
| Interface | zone d'application |
| Log | journaliser les connexions correspondantes |

Règle type WAN :
```
Pass  TCP  any → 203.0.113.10:80  (serveur web en DMZ/port forward)
Block all  (dernière règle par défaut)
```

## NAT (Firewall → NAT)
| Type | Rôle |
|---|---|
| **Port Forward** | rediriger un port WAN vers un serveur interne (ex. 443 → 10.0.0.10:443) |
| **1:1** | mappage complet d'une IP publique vers une IP interne |
| **Outbound** | NAT de sortie (masquerading du LAN vers Internet) |

### Exemple Port Forward
```
WAN  TCP  any → 10.0.0.10:443   (destination = serveur, port = HTTPS)
```
Penser à ajouter la règle *Pass* correspondante sur WAN.

## Alias (Firewall → Aliases)
| Type | Exemple |
|---|---|
| Host | `ServeursWeb` = 10.0.0.10, 10.0.0.11 |
| Network | `LAN` = 192.168.1.0/24 |
| Port | `WebPorts` = 80, 443 |
| Group | regroupement de plusieurs alias |

## VPN
### OpenVPN
| Élément | Détail |
|---|---|
| VPN → OpenVPN → Servers | serveur VPN |
| VPN → OpenVPN → Clients | clients (VPN client pour accéder à l'extérieur) |
| Exporter | VPN → OpenVPN → Client Export (fichiers .ovpn) |
| Cipher | AES-256-GCM, TLS 1.2+ |

### WireGuard (pfSense 2.5+)
| Élément | Détail |
|---|---|
| VPN → WireGuard | tunnels (endpoint, clés privées/publiques, allowed IPs) |
| Interfaces | assigner le tunnel WireGuard comme interface |
| Règles | autoriser le trafic sur l'interface WireGuard |

## Segmentation VLAN
| Étape | Détail |
|---|---|
| 1 | Interfaces → Assignments : créer le VLAN (tag 802.1Q) sur l'interface physique |
| 2 | Assigner une interface OPT au VLAN |
| 3 | Firewall → Rules : règles du VLAN |
| 4 | Le routage inter-VLAN se fait automatiquement si règles *Pass* |

## DNS et DHCP (Services)
| Service | Rôle |
|---|---|
| **DHCP Server** | plages LAN/VLAN, réservations MAC→IP |
| **DNS Resolver** | résolveur local (Unbound), DNSSEC |
| **DNS Forwarder** | relais DNS simple |
| **Dynamic DNS** | mise à jour DNS pour IP dynamique |

## Sauvegarde et restauration
| Action | Détail |
|---|---|
| Diagnostics → Backup/Restore | exporter la configuration (`.xml`) |
| Restaurer | re-importer un fichier `.xml` |
| Reset usine | console → option « reset factory defaults » |

## Dépannage (outils intégrés)
| Outil (Diagnostics) | Rôle |
|---|---|
| Ping | tester une cible |
| Traceroute | chemin réseau |
| DNS Lookup | résolution |
| Packet Capture | capture de trames (port, IP) |
| States | table des états (connexions actives) |
| Route | table de routage |
| ARP | table ARP |
| pfTop | statistiques pf en temps réel |

Commandes console : `reboot`, `shutdown`, `pfctl -sr` (liste des règles), `pfctl -ss` (états).

## Notions iptables / nftables (Linux)
### iptables (legacy)
| Commande | Effet |
|---|---|
| `iptables -L -n -v` | lister les règles |
| `iptables -A INPUT -p tcp --dport 22 -s 10.0.0.0/8 -j ACCEPT` | autoriser SSH depuis le LAN |
| `iptables -A INPUT -p tcp --dport 80 -j ACCEPT` | autoriser HTTP |
| `iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT` | autoriser les retours |
| `iptables -P INPUT DROP` | politique par défaut : bloquer |
| `iptables -t nat -A PREROUTING -p tcp --dport 80 -j DNAT --to-destination 10.0.0.10:80` | redirection de port |
| `iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE` | NAT de sortie |
| `iptables -A INPUT -m conntrack --ctstate INVALID -j DROP` | bloquer les paquets invalides |
| `iptables -S` / `iptables-save` | afficher / sauvegarder les règles |
| `iptables -F` | vider toutes les règles |
| `service iptables save` | persister (RHEL) |

### nftables (moderne)
| Commande | Effet |
|---|---|
| `nft list ruleset` | lister les règles |
| `nft add table inet filter` | créer une table |
| `nft add chain inet filter input '{ type filter hook input priority 0; policy drop; }'` | chaîne INPUT en drop |
| `nft add rule inet filter input tcp dport 22 accept` | autoriser SSH |
| `nft add rule inet filter input ct state established,related accept` | autoriser les retours |
| `nft flush ruleset` | vider les règles |

### ufw (frontend iptables/nftables, Ubuntu/Debian)
| Commande | Effet |
|---|---|
| `ufw enable` / `ufw disable` | activer / désactiver |
| `ufw status verbose` | état détaillé |
| `ufw allow 22/tcp` | autoriser SSH |
| `ufw allow from 10.0.0.0/24 to any port 3306` | autoriser MySQL depuis le LAN |
| `ufw deny 23/tcp` | bloquer Telnet |
| `ufw delete allow 22/tcp` | supprimer une règle |
| `ufw app list` | profils d'applications |
| `ufw default deny incoming` | politique par défaut |

### firewalld (RHEL/Fedora)
| Commande | Effet |
|---|---|
| `firewall-cmd --state` | état |
| `firewall-cmd --list-all` | règles actives |
| `firewall-cmd --add-service=http --permanent` | autoriser HTTP (persistant) |
| `firewall-cmd --add-port=8080/tcp --permanent` | autoriser un port |
| `firewall-cmd --add-source=10.0.0.0/24 --permanent` | autoriser un réseau |
| `firewall-cmd --reload` | recharger |

## Bonnes pratiques
- Bloquer **tout par défaut**, n'ouvrir que le nécessaire.
- Séparer les zones (LAN / DMZ / VPN), ne jamais exposer le LAN direct.
- Utiliser des **alias** pour centraliser les adresses/ports.
- Activer la journalisation sur les règles bloquées (sans saturer les logs).
- Mettre à jour régulièrement pfSense (branche stable).
- Utiliser **VPN (WireGuard/OpenVPN)** pour l'accès distant, pas du port forwarding RDP.
- Tester la table de règles (`pfctl -sr` / `iptables -L -n -v`) après modification.
