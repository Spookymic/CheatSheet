# Cheatsheet Docker Swarm

## Concepts
- **Swarm** : mode de clustering natif de Docker pour l'orchestration de conteneurs.
- **Manager** : nœud qui gère le cluster et l'API. Il faut un nombre impair (1, 3, 5...) pour l'élection (consensus Raft).
- **Worker** : nœud qui exécute les conteneurs (services/tâches).
- **Service** : définition d'une application répliquée.
- **Task** : unité d'exécution d'un service (un conteneur).
- **Stack** : ensemble de services défini par un fichier Compose.

## Initialisation du cluster
| Commande | Effet |
|---|---|
| `docker swarm init` | initialiser le swarm (sur le 1er manager) |
| `docker swarm init --advertise-addr IP` | préciser l'adresse à annoncer |
| `docker swarm init --force-new-cluster` | réinitialiser en cas de perte de quorum |
| `docker swarm join --token TOKEN IP:2377` | joindre un nœud |
| `docker swarm join-token manager` | afficher le token manager |
| `docker swarm join-token worker` | afficher le token worker |
| `docker swarm leave` | quitter le swarm (sur un worker) |
| `docker swarm leave --force` | forcer le départ (manager) |
| `docker swarm update --autolock=true` | activer le verrouillage automatique (chiffrement Raft) |

## Nœuds
| Commande | Effet |
|---|---|
| `docker node ls` | lister les nœuds |
| `docker node inspect nom` | détails d'un nœud |
| `docker node update --availability drain nom` | drainer (vider) un nœud |
| `docker node update --availability active nom` | réactiver un nœud |
| `docker node update --availability pause nom` | mettre en pause |
| `docker node promote nom` | promouvoir en manager |
| `docker node demote nom` | rétrograder en worker |
| `docker node rm nom` | retirer un nœud (déconnecté) |
| `docker node ps nom` | tâches en cours sur un nœud |

> Le drain déplace les tâches vers d'autres nœuds avant une maintenance.

## Services
| Commande | Effet |
|---|---|
| `docker service create --name web -p 80:80 nginx` | créer un service |
| `docker service create --name web --replicas 3 nginx` | avec 3 réplicas |
| `docker service ls` | lister les services |
| `docker service ps nom` | lister les tâches d'un service |
| `docker service inspect nom` | détails |
| `docker service scale web=5` | changer le nombre de réplicas |
| `docker service update --image nginx:1.25 web` | mettre à jour l'image |
| `docker service update --replicas 2 web` | ajuster les réplicas |
| `docker service update --force web` | forcer le redéploiement |
| `docker service update --rollback web` | revenir à la version précédente |
| `docker service rm nom` | supprimer le service |
| `docker service logs nom` | logs du service |
| `docker service ls --format "{{.Name}}: {{.Replicas}}"` | formatage de sortie |

### Options de création fréquentes
```bash
docker service create \
  --name web \
  --replicas 3 \
  --publish published=80,target=8080 \
  --mount type=volume,source=donnees,target=/data \
  --constraint "node.role == manager" \
  --env VAR=valeur \
  --network monreseau \
  nginx
```

### Mise à jour avec options
| Option | Effet |
|---|---|
| `--update-delay 10s` | délai entre chaque mise à jour |
| `--update-parallelism 2` | mises à jour en parallèle |
| `--update-failure-action rollback` | rollback automatique en cas d'échec |
| `--rollback-delay 5s` | délai du rollback |

## Réseaux Swarm (overlay)
| Commande | Effet |
|---|---|
| `docker network create -d overlay --attachable reseau` | créer un réseau overlay |
| `docker network create -d overlay --attachable reseau --subnet=10.0.9.0/24` | avec subnet |
| `docker network ls` | lister les réseaux |
| `docker network inspect reseau` | détails (nœuds, conteneurs) |
| `docker service create --network reseau ...` | attacher un service au réseau |

> `--attachable` permet aussi aux conteneurs `docker run` d'utiliser le réseau.

## Secrets et configs
| Commande | Effet |
|---|---|
| `printf "mdp" \| docker secret create mdp_secret -` | créer un secret |
| `docker secret create nom fichier` | depuis un fichier |
| `docker secret ls` / `docker secret rm nom` | lister / supprimer |
| `docker service create --secret mdp_secret ...` | attacher un secret à un service |
| `docker config create nom fichier` | créer une config |
| `docker config ls` | lister les configs |
| `docker service create --config source=nom,target=/etc/conf ...` | attacher une config |

Secrets montés dans : `/run/secrets/nom_secret`.

## Stacks (Compose en Swarm)
```yaml
version: "3.8"
services:
  web:
    image: nginx
    deploy:
      replicas: 3
      update_config:
        delay: 10s
      restart_policy:
        condition: on-failure
    ports:
      - "80:80"
```
| Commande | Effet |
|---|---|
| `docker stack deploy -c compose.yml nom` | déployer une stack |
| `docker stack ls` | lister les stacks |
| `docker stack services nom` | services de la stack |
| `docker stack ps nom` | tâches de la stack |
| `docker stack rm nom` | supprimer la stack |
| `docker stack deploy -c compose.yml -c compose.prod.yml nom` | fusionner plusieurs fichiers |

Note : dans un fichier Compose pour Swarm, utiliser `deploy:` (pas `docker compose up`).

## Routage mesh (Ingress)
- Les services publiés sont accessibles depuis n'importe quel nœud via le **mesh routing** : le trafic entrant est routé automatiquement vers un nœud qui exécute la tâche.
- `--publish mode=host` : publie le port uniquement sur le nœud exécutant la tâche (pas de mesh).
- Le load balancing interne est géré par le réseau overlay.

## Verrouillage et sécurité
| Commande | Effet |
|---|---|
| `docker swarm unlock` | déverrouiller après redémarrage (si autolock) |
| `docker swarm unlock-key` | afficher la clé de déverrouillage |
| `docker swarm ca --rotate` | faire tourner les certificats CA |
| `docker node ls` (état Raft) | vérifier la santé du cluster |

## Dépannage
| Problème | Vérification |
|---|---|
| Pas de quorum | `docker node ls`, les managers sont-ils actifs ? |
| Tâche en échec | `docker service ps --no-trunc nom` pour voir l'erreur |
| Nœud absent | `docker node rm` après `leave` |
| Image introuvable | `docker service update --image nom:tag` |

## Sauvegarde
- Les données Raft sont dans `/var/lib/docker/swarm/` sur les managers.
- Sauvegarder régulièrement + sauvegarder le token (`docker swarm join-token`).
