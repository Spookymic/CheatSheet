# Cheatsheet Cisco (Switch)

## Modes de commande
| Mode | Invite | Accès |
|---|---|---|
| Utilisateur | `Switch>` | accès limité (lecture) |
| Privilégié | `Switch#` | `enable` |
| Configuration globale | `Switch(config)#` | `configure terminal` |
| Configuration interface | `Switch(config-if)#` | `interface fa0/1` |
| Configuration VLAN | `Switch(config-vlan)#` | `vlan 10` |
| Configuration ligne | `Switch(config-line)#` | `line console 0` |

| Commande | Effet |
|---|---|
| `enable` | passer en mode privilégié |
| `configure terminal` (ou `conf t`) | configuration globale |
| `exit` | revenir au niveau précédent |
| `end` (ou `Ctrl+Z`) | revenir au mode privilégié |
| `Ctrl+C` | interrompre |
| `?` | aide contextuelle |
| `Tab` | complétion |
| `show running-config` | config en cours |
| `show startup-config` | config enregistrée |
| `write memory` (ou `copy running-config startup-config`) | enregistrer |
| `reload` | redémarrer |

## Configuration de base
```cisco
hostname SW-01
no ip domain-lookup
enable secret mon_mot_de_passe
line console 0
  password mdp_console
  login
  logging synchronous
line vty 0 4
  password mdp_telnet
  login
banner motd #Accès réservé#
```

### Gestion à distance
```cisco
interface vlan 1
  ip address 192.168.1.10 255.255.255.0
  no shutdown
ip default-gateway 192.168.1.1
```
```cisco
username admin privilege 15 secret mdp
line vty 0 4
  transport input ssh
  login local
ip ssh version 2
crypto key generate rsa modulus 2048
```

## Interfaces
| Commande | Effet |
|---|---|
| `interface gigabitethernet0/1` | accéder à l'interface |
| `shutdown` / `no shutdown` | désactiver / activer |
| `description lien vers SW-02` | description |
| `speed 100` / `duplex full` | vitesse et duplex |
| `switchport mode access` | port accès |
| `switchport mode trunk` | port trunk |
| `switchport access vlan 10` | VLAN du port accès |
| `switchport trunk allowed vlan 10,20` | VLANs autorisés sur le trunk |
| `switchport trunk native vlan 99` | VLAN natif |
| `switchport access vlan` (si port accès) | — |
| `no switchport` | passer en mode routé (L3) |

## VLAN
```cisco
vlan 10
  name USERS
vlan 20
  name SERVERS
```
| Commande | Effet |
|---|---|
| `show vlan brief` | liste des VLAN et ports |
| `show vlan id 10` | détail d'un VLAN |
| `no vlan 10` | supprimer un VLAN |
| `switchport trunk encapsulation dot1q` | encapsulation (sur 2950/3560) |
| `vtp domain ...` / `vtp mode ...` | VTP (souvent à éviter : mode transparent) |

## Redondance de liens (STP)
| Commande | Effet |
|---|---|
| `spanning-tree mode rapid-pvst` | mode Rapid PVST |
| `spanning-tree vlan 10 root primary` | rendre ce switch racine pour le VLAN |
| `spanning-tree vlan 10 root secondary` | racine secondaire |
| `spanning-tree portfast` | port en accès rapide (machines finales) |
| `spanning-tree bpduguard enable` | protection BPDU |
| `show spanning-tree` | état STP |
| `show spanning-tree root` | switch racine |
| `show spanning-tree summary` | résumé |

### Agrégation de liens (EtherChannel)
```cisco
interface range gigabitethernet0/1-2
  channel-group 1 mode active
interface port-channel 1
  switchport mode trunk
```

## Sécurité des ports
| Commande | Effet |
|---|---|
| `switchport port-security` | activer |
| `switchport port-security maximum 1` | 1 MAC max |
| `switchport port-security mac-address sticky` | apprendre les MAC automatiquement |
| `switchport port-security violation restrict` | comportement en cas de violation |
| `show port-security` | état |
| `show port-security address` | MAC apprises |

Options violation : `protect` (ignore), `restrict` (log), `shutdown` (désactive le port, défaut).

## IP et diagnostics
| Commande | Effet |
|---|---|
| `show ip interface brief` | état des interfaces |
| `show interfaces` | statistiques (erreurs, collisions) |
| `show interfaces status` | état et VLAN des ports |
| `show interfaces trunk` | trunks actifs |
| `show mac address-table` | table MAC |
| `show cdp neighbors` | voisins Cisco découverts |
| `show lldp neighbors` | voisins LLDP |
| `ping 192.168.1.1` | test de connectivité |
| `traceroute 192.168.1.1` | tracer le chemin |
| `debug ...` | débogage (prudence en production) |
| `show version` | version IOS, uptime |
| `show running-config` | config courante |
| `show cdp` | état CDP |

## Sauvegarde et restauration
| Commande | Effet |
|---|---|
| `copy running-config startup-config` | sauvegarder |
| `copy running-config tftp:` | sauvegarder sur un serveur TFTP |
| `copy tftp: running-config` | restaurer depuis TFTP |
| `show flash:` | fichiers en mémoire flash |
| `archive log config` | journalisation des modifications |

## Dépannage rapide
| Problème | Vérifications |
|---|---|
| Pas de lien (port X) | `show interfaces status`, câble, duplex |
| Pas de communication inter-VLAN | routing L3 manquant ou trunk mal configuré |
| Port désactivé (erreur) | `show interfaces status err-disabled` → `shutdown` / `no shutdown` |
| STP rejette un port | vérifier `show spanning-tree`, configurer portfast/bpduguard |
| VLAN inaccessible | `show vlan brief`, vérifier le trunk et le VLAN natif |

### Réactiver un port en err-disabled
```
interface gigabitethernet0/1
  shutdown
  no shutdown
```
Ou configurer `errdisable recovery cause all`.

## Rappels utiles
- Le mode d'utilisateur ne permet que les commandes `show` de base.
- Toujours enregistrer avec `write memory` avant de redémarrer.
- `no ip domain-lookup` évite les tentatives DNS lors d'une faute de frappe.
- Un trunk transporte plusieurs VLAN (802.1Q), le VLAN natif n'est pas taggé.
- En cas de doute, `?` et `Tab` sont vos meilleurs amis.
