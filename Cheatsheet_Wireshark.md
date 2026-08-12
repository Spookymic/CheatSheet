# Cheatsheet Wireshark

## Généralités
- Analyseur de trames réseau (fork de **Ethereal**).
- **Capture filter** : filtrage à la capture (BPF, pour réduire la taille du fichier).
- **Display filter** : filtrage d'affichage des trames déjà capturées.
- Types : `cap` / `pcap` / `pcapng` (format par défaut).

## Capturer
| Action | Raccourci / menu |
|---|---|
| Démarrer / arrêter la capture | `Ctrl+E` |
| Nouvelle capture | `Ctrl+N` |
| Ouvrir un fichier | `Ctrl+O` |
| Enregistrer | `Ctrl+S` |
| Préférences | `Ctrl+Shift+P` |
| Rechercher une trame | `Ctrl+F` |
| Suivre le flux TCP | Clic droit → Follow → TCP Stream |

## Filtres d'affichage (display filters)
| Filtre | Effet |
|---|---|
| `ip.addr == 192.168.1.10` | une adresse IP (src ou dst) |
| `ip.src == 192.168.1.10` / `ip.dst == ...` | source / destination |
| `tcp.port == 443` | un port TCP |
| `udp.port == 53` | un port UDP |
| `tcp.port == 443 || tcp.port == 80` | OU logique |
| `ip.addr == 192.168.1.10 && tcp.port == 22` | ET logique |
| `!tcp.port == 443` | négation |
| `http` | tout le trafic HTTP |
| `http.request.method == "GET"` | requêtes GET |
| `http.response.code == 500` | réponses en erreur 500 |
| `dns` / `dns.qry.name contains "exemple"` | trafic DNS |
| `icmp` | pings (echo request/reply) |
| `arp` | trames ARP |
| `tcp.flags.syn == 1 && tcp.flags.ack == 0` | paquets SYN |
| `tcp.flags.rst == 1` | connexions réinitialisées |
| `tcp.analysis.retransmission` | retransmissions (lenteurs) |
| `tcp.analysis.duplicate_ack` | accusés dupliqués |
| `frame.len > 1400` | trames de grande taille |
| `tcp.payload contains "GET"` | payload contenant une chaîne |
| `smtp` / `pop` / `imap` | trafic mail |
| `ssl` / `tls` | trafic chiffré (voir aléa) |
| `snmp` | trafic SNMP |
| `netbios` / `smb2` | partages Windows |
| `nbtscan` | scans NetBIOS |
| `eth.addr == aa:bb:cc:dd:ee:ff` | une adresse MAC |

Opérateurs : `==`, `!=`, `>`, `<`, `>=`, `<=`, `&&`, `||`, `!`, `contains`, `matches` (regex).

## Filtres de capture (BPF)
| Filtre | Effet |
|---|---|
| `host 192.168.1.10` | une adresse IP |
| `net 192.168.1.0/24` | un sous-réseau |
| `port 80` | un port |
| `portrange 8000-8100` | une plage de ports |
| `tcp` / `udp` / `icmp` | un protocole |
| `host 10.0.0.1 and tcp port 443` | combinaison |
| `not port 22` | exclusion |

## Onglets utiles
| Vue | Rôle |
|---|---|
| **Packet list** | liste des trames (n° , heure, src, dst, proto, info) |
| **Packet details** | arbre des protocoles de la trame sélectionnée |
| **Packet bytes** | hexadécimal + ASCII |
| **Statistics** | hiérarchie des protocoles, conversations, endpoints, flux |
| **Conversations** | échanges hôte à hôte |
| **IO Graph** | débit dans le temps |

## Actions courantes
| Action | Effet |
|---|---|
| Clic droit sur une IP → **Apply as Filter** | filtrer sur cette valeur |
| **Follow TCP Stream** | reconstituer une conversation complète (HTTP, FTP...) |
| **Export Objects → HTTP** | extraire les fichiers transférés en HTTP |
| Coloration | règles pour mettre en évidence (erreurs, retransmissions) |
| `Ctrl+M` | marquer une trame |
| `Ctrl+G` | aller à la trame marquée suivante |

## tshark (CLI)
| Commande | Effet |
|---|---|
| `tshark -i eth0` | capturer sur une interface |
| `tshark -i eth0 -f "port 80"` | capturer avec filtre BPF |
| `tshark -r capture.pcapng -Y "http"` | filtrer un fichier (display filter) |
| `tshark -r capture.pcapng -c 100` | afficher les 100 premières trames |
| `tshark -r capture.pcapng -T fields -e ip.src -e ip.dst -E header=y` | extraire des champs |
| `tshark -r capture.pcapng -z conv,tcp` | statistiques de conversations |
| `tshark -r capture.pcapng -z io,stat,30` | IO Graph en texte |
| `tshark -r capture.pcapng -Y http.request -T fields -e http.host -e http.request.uri` | requêtes HTTP extraites |
| `capinfos capture.pcapng` | infos sur le fichier |
| `mergecap -w out.pcapng a.pcapng b.pcapng` | fusionner des captures |
| `editcap -d capture.pcapng out.pcapng` | supprimer les doublons |

## TLS déchiffré
| Étape | Détail |
|---|---|
| Préférences → Protocols → TLS | renseigner la clé privée / le fichier de clés |
| Ou utiliser la clé de session | `(Pre)-Master-Secret log` d'un navigateur (SSLKEYLOGFILE) |
| Variable | `SSLKEYLOGFILE=~/tlskeys.log` avant de lancer le navigateur |

## Analyse réseau rapide
| Observation | Signification probable |
|---|---|
| Beaucoup de `Retransmission` | latence, congestion, perte de paquets |
| `Duplicate ACK` + `Retransmission` | pertes et lenteur TCP |
| `TCP RST` | connexion fermée brutalement (pare-feu, crash applicatif) |
| Broadcast ARP répétés | conflit d'adresse IP, recherche de MAC |
| SYN sans SYN-ACK | pare-feu bloque le port / service arrêté |
| Trafic `NetBIOS`/`LLMNR` | découverte Windows (voir sécurité) |
| Beaucoup de `DNS` vers `.local` | problèmes de résolution |

## Raccourcis clavier
| Touche | Action |
|---|---|
| `Ctrl+E` | démarrer / arrêter la capture |
| `Ctrl+F` | recherche (affichage / hexa / chaîne) |
| `Ctrl+M` | marquer la trame |
| `Ctrl+G` | trame marquée suivante |
| `Ctrl+Alt+Shift+T` | suivre le flux TCP |
| `Space` | basculer le paquet sélectionné |
| `Shift+↓ / ↑` | étendre / réduire l'arbre du paquet |
