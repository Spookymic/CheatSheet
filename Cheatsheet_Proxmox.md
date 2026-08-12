# Cheatsheet Proxmox

## Généralités
- **Proxmox VE (PVE)** : hyperviseur Debian, virt. **QEMU/KVM** + conteneurs **LXC**.
- Interface web : `https://IP:8006`.
- API : `https://IP:8006/api2/json` (token API possible).
- Cluster : nœuds groupés avec gestion HA et migration à chaud.
- Types de VM : **QEMU (VM)** et **LXC (CT)**.

## Interface web (accès rapide)
| Menu | Rôle |
|---|---|
| Datacenter | cluster, HA, stockage, pools, permissions |
| Nodes | nœuds du cluster |
| Containers / VMs | gestion des machines |
| Storage | stockages (local, local-lvm, NFS, Ceph...) |
| Backup | sauvegardes planifiées |

## Commandes CLI
| Commande | Effet |
|---|---|
| `pveversion` | version de Proxmox |
| `pvesh get /version` | version via API locale |
| `qm list` | lister les VMs (QEMU) |
| `qm start 100` / `qm stop 100` | démarrer / arrêter une VM |
| `qm reboot 100` | redémarrer une VM |
| `qm status 100` | statut d'une VM |
| `qm config 100` | configuration d'une VM |
| `qm create 100 --name web --memory 2048 --cores 2 --net0 virtio,bridge=vmbr0 --scsi0 local-lvm:20` | créer une VM |
| `qm clone 9000 100 --name web --full` | cloner un template |
| `qm template 9000` | convertir en template |
| `qm migrate 100 node2` | migrer une VM vers un autre nœud |
| `qm monitor 100` | console QEMU |
| `pct list` | lister les conteneurs LXC |
| `pct start 200` / `pct stop 200` | démarrer / arrêter un conteneur |
| `pct create 200 local:vztmpl/debian-12.tar.xz --hostname ct --net0 name=eth0,bridge=vmbr0 --storage local-lvm --cores 2 --memory 1024` | créer un conteneur |
| `pct enter 200` | entrer dans le conteneur |
| `pct config 200` | configuration du conteneur |
| `pvesh get /nodes/pve/qemu` | lister les VMs via API |
| `pvesm status` | état des stockages |
| `pvesm list local-lvm` | contenu d'un stockage |
| `pvesr run` | vérifier la réplication |
| `vzdump 100 --dumpdir /var/lib/vz/dump` | sauvegarde manuelle d'une VM |
| `vzdump 200 --mode snapshot` | sauvegarde d'un conteneur (snapshot) |
| `pct restore 200 /var/lib/vz/dump/vzdump-lxc-200-*.tar.zst` | restaurer un conteneur |
| `qmrestore /var/lib/vz/dump/vzdump-qemu-100-*.vma.zst 100` | restaurer une VM |
| `pvecm status` | état du cluster |
| `pvecm add IP_NODE1` | rejoindre un cluster |
| `ha-manager status` | état de la haute disponibilité |
| `ha-manager add vm:100` | activer la HA sur une VM |
| `ceph -s` | statut du cluster Ceph |
| `zfs list` | pools ZFS (si utilisé) |

Options utiles `qm create` : `--memory`, `--cores`, `--sockets`, `--net0 virtio,bridge=vmbr0`, `--scsi0 local-lvm:20` (disque), `--ide2 local:iso/iso.iso,media=cdrom` (ISO), `--boot order=scsi0;ide2`, `--ostype l26`, `--start` (démarrer à la création).

## Stockages
| Stockage | Type | Usage typique |
|---|---|---|
| `local` | répertoire | ISOs, templates LXC, sauvegardes |
| `local-lvm` | LVM-thin | disques des VMs (performance) |
| `local-zfs` | ZFS | disques VMs (si ZFS) |
| `nfs / cifs` | réseau | partage de sauvegardes |
| `ceph` | Ceph (RBD) | cluster de stockage distribué |

## Réseau
- **Bridges** : `vmbr0` (accès réseau des VM/CT), configuration dans `/etc/network/interfaces`.
- Bridges Linux : `bridge-utils`.
- VXLAN pour les réseaux overlay (cluster).

## Sauvegardes
| Élément | Détail |
|---|---|
| Répertoire | `/var/lib/vz/dump/` |
| Planification | Datacenter → Backup (PBS ou répertoire) |
| Modes | `snapshot` (n'arrête pas la VM), `suspend`, `stop` |
| Rotation | nombre de sauvegardes conservées |
| Proxmox Backup Server (PBS) | déduplication, chiffrement, restauration granulaire |

## Cluster et HA
| Élément | Détail |
|---|---|
| Cluster | jusqu'à 32 nœuds, horloge NTP obligatoire |
| Quorum | majorité de nœuds (ou nœud témoin QDevice) |
| HA | relance automatique des VM sur un autre nœud en cas de panne |
| Réplication | copie des disques entre nœuds (ZFS uniquement) |
| Migration | à chaud (QEMU) sans interruption |

## Dépannage
| Problème | Commande / action |
|---|---|
| VM ne démarre pas | `qm start 100` puis `pveversion`, logs `/var/log/pve/tasks/` |
| Cluster sans quorum | `pvecm status`, vérifier la connectivité, NTP |
| Stockage plein | `pvesm status`, libérer / étendre |
| Port 8006 bloqué | vérifier le pare-feu / réseau |
| VM lente | vérifier `--scsi0` (thin), ressources allouées |
| Diagnostic système | `journalctl -u pve-cluster`, `systemctl status pveproxy` |
