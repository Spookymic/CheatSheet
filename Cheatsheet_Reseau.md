# Cheatsheet Réseau

## Modèle OSI (7 couches)
| Couche | Nom | Exemples |
|---|---|---|
| 7 | Application | HTTP, HTTPS, DNS, SMTP, FTP |
| 6 | Présentation | SSL/TLS (chiffrement), codage |
| 5 | Session | TLS, NetBIOS, RPC |
| 4 | Transport | TCP, UDP |
| 3 | Réseau | IP, ICMP, OSPF, BGP |
| 2 | Liaison | Ethernet, Wi-Fi (802.11), PPP, VLAN |
| 1 | Physique | câbles, fibre, ondes |

Mnémonique FR : **P**lus **P**artons **S**ouvent **T**ravailler **R**igoureusement **L**es **P**apys → 7 → 1 (PPS TRLP).

## Modèle TCP/IP (4 couches)
| Couche | Exemples |
|---|---|
| Application | HTTP, DNS, SMTP |
| Transport | TCP, UDP |
| Internet | IP, ICMP |
| Accès réseau | Ethernet, Wi-Fi |

## Adressage IP
- **IPv4** : 32 bits, notation décimale pointée `192.168.1.1`.
- **IPv6** : 128 bits, notation hexadécimale `2001:db8::1`.

### Classes d'adresses privées
| Réseau | Masque | Plage |
|---|---|---|
| Classe A | /8 | `10.0.0.0 – 10.255.255.255` |
| Classe B | /12 | `172.16.0.0 – 172.31.255.255` |
| Classe C | /16 | `192.168.0.0 – 192.168.255.255` |
| Loopback | — | `127.0.0.1` |

### Masques courants
| Masque | CIDR | Hôtes utiles |
|---|---|---|
| 255.255.255.0 | /24 | 254 |
| 255.255.255.128 | /25 | 126 |
| 255.255.255.192 | /26 | 62 |
| 255.255.255.240 | /28 | 14 |
| 255.255.0.0 | /16 | 65 534 |
| 255.0.0.0 | /8 | 16 777 214 |

### Calcul d'un sous-réseau
Pour `192.168.1.0/26` :
- Pas = 256 − 192 = 64
- Sous-réseaux : .0, .64, .128, .192
- Chaque sous-réseau : adresse réseau, 62 hôtes (adresse réseau +1 à broadcast −1), broadcast = fin de plage.

Formule hôtes : `2^(32−CIDR) − 2`.

## TCP vs UDP
| Critère | TCP | UDP |
|---|---|---|
| Connexion | orienté connexion | sans connexion |
| Fiabilité | garantie (acquittements) | pas de garantie |
| Ordre | maintenu | non garanti |
| Contrôle de flux | oui | non |
| Usage | HTTP, HTTPS, SSH, FTP, SMTP | DNS, DHCP, VoIP, vidéo, jeu |

## Ports courants
| Port | Protocole | Service |
|---|---|---|
| 20/21 | TCP | FTP |
| 22 | TCP | SSH |
| 23 | TCP | Telnet (non sécurisé) |
| 25 | TCP | SMTP |
| 53 | TCP/UDP | DNS |
| 67/68 | UDP | DHCP |
| 80 | TCP | HTTP |
| 110 | TCP | POP3 |
| 143 | TCP | IMAP |
| 443 | TCP | HTTPS |
| 445 | TCP | SMB |
| 3389 | TCP | RDP |
| 8080 | TCP | proxy / alternative HTTP |

## DNS
| Type d'enregistrement | Rôle |
|---|---|
| A / AAAA | adresse IPv4 / IPv6 |
| CNAME | alias d'un nom |
| MX | serveur de messagerie |
| NS | serveur de noms |
| TXT | texte (SPF, DKIM, vérification) |
| PTR | résolution inverse |

Commandes : `nslookup domaine`, `dig domaine A`, `dig -x IP`.

## DHCP
| Étape | Message | Direction |
|---|---|---|
| 1 | DHCPDISCOVER | client → serveur |
| 2 | DHCPOFFER | serveur → client |
| 3 | DHCPREQUEST | client → serveur |
| 4 | DHCPACK | serveur → client |

Éléments fournis : adresse IP, masque, passerelle, DNS, bail (durée).

## Commandes de diagnostic
| Commande | Effet |
|---|---|
| `ping cible` | test ICMP de base |
| `traceroute` / `tracert` | chemin réseau |
| `ipconfig` / `ifconfig` / `ip a` | configuration IP |
| `ipconfig /all` | détails complets (Windows) |
| `ip route` / `route print` | table de routage |
| `nslookup` / `dig` | résolution DNS |
| `arp -a` | table ARP |
| `netstat` | connexions et ports |
| `ss -tulpn` | ports en écoute (Linux) |
| `tcping` / `Test-NetConnection` (PowerShell) | test de port |
| `telnet hôte port` | tester un port (ancien) |

## Wi-Fi
| Élément | Rôle |
|---|---|
| SSID | nom du réseau |
| BSSID | adresse MAC du point d'accès |
| WPA2/WPA3 | sécurité (éviter WEP) |
| 2,4 GHz | meilleure portée, plus d'interférences |
| 5 GHz | plus rapide, moins de portée |
| Roaming | changement de point d'accès |

## VLAN
- Isolation logique au sein d'un switch (802.1Q).
- Un trunk transporte plusieurs VLAN entre switches.
- Le VLAN natif n'est pas taggé.
- Communication inter-VLAN : router-on-a-stick ou switch L3.

## NAT
| Type | Rôle |
|---|---|
| NAT statique | une adresse publique ↔ une privée (1:1) |
| NAT dynamique | pool d'adresses publiques |
| PAT (NAT overload) | plusieurs privées → une publique (ports) |

## Sécurité réseau
| Élément | Rôle |
|---|---|
| Pare-feu | filtrage des flux |
| VPN | tunnel chiffré (IPsec, WireGuard, OpenVPN) |
| IDS/IPS | détection / prévention d'intrusions |
| DMZ | zone exposée entre interne et externe |
| 802.1X | authentification au port réseau |
| Segmentation | découpage en VLAN / sous-réseaux |

## Outils utiles
| Outil | Usage |
|---|---|
| Wireshark | capture et analyse de trames |
| nmap | scan de ports et de services |
| iperf3 | test de débit |
| MTR / winmtr | combinaison ping + traceroute |
| curl | requêtes HTTP/HTTPS |
| Speedtest | débit internet |

## Méthodologie de dépannage (top-down)
1. Vérifier les indicateurs physiques (câbles, LEDs, Wi-Fi activé).
2. `ipconfig`/`ip a` : adresse IP valide ? (169.254.* = DHCP KO)
3. Ping de la passerelle (connectivité locale).
4. Ping d'une IP distante (routage).
5. Résolution DNS : `nslookup` (noms accessibles ?).
6. Test applicatif (navigateur, port avec `Test-NetConnection`).
7. Traceroute pour localiser le problème.
8. Vérifier pare-feu, ACL, filtres.
