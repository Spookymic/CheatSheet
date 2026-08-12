# Cheatsheet Docker

## Images
| Commande | Effet |
|---|---|
| `docker pull image` | télécharger une image |
| `docker images` | lister les images locales |
| `docker build -t nom:tag .` | construire depuis un Dockerfile |
| `docker rmi image` | supprimer une image |
| `docker tag image nom:tag` | tagger une image |
| `docker push image` | publier sur un registre |
| `docker search nom` | chercher dans Docker Hub |
| `docker history image` | historique des couches |
| `docker image prune` | nettoyer les images inutilisées |

## Conteneurs
| Commande | Effet |
|---|---|
| `docker run image` | lancer un conteneur |
| `docker run -it image bash` | lancer en mode interactif |
| `docker run -d image` | lancer en arrière-plan (détaché) |
| `docker run -p 8080:80 image` | mapper un port (hôte:conteneur) |
| `docker run -v /hote:/ctr image` | monter un volume |
| `docker run --name monctr image` | nommer le conteneur |
| `docker run --rm image` | supprimer le conteneur à l'arrêt |
| `docker run -e VAR=valeur image` | variable d'environnement |
| `docker run --network réseau image` | choisir le réseau |
| `docker ps` / `docker ps -a` | conteneurs actifs / tous |
| `docker stop ctr` / `docker start ctr` | arrêter / redémarrer |
| `docker restart ctr` | redémarrer |
| `docker rm ctr` | supprimer un conteneur |
| `docker rm $(docker ps -aq)` | supprimer tous les conteneurs |
| `docker exec -it ctr bash` | entrer dans un conteneur |
| `docker exec -it ctr sh` | entrer (si bash absent) |
| `docker logs ctr` | logs du conteneur |
| `docker logs -f ctr` | suivre les logs |
| `docker cp ctr:/chemin /hote` | copier depuis le conteneur |
| `docker cp /hote ctr:/chemin` | copier vers le conteneur |
| `docker stats` | consommation CPU / RAM |
| `docker inspect ctr` | détails (IP, config...) |
| `docker top ctr` | processus du conteneur |
| `docker update --restart=always ctr` | redémarrage automatique |

## Dockerfile
```dockerfile
FROM python:3.11-slim              # image de base
WORKDIR /app                       # dossier de travail
COPY . /app                        # copier les fichiers
RUN pip install -r requirements.txt # installer les dépendances
EXPOSE 8000                        # port exposé
ENV VAR=valeur                     # variable d'environnement
CMD ["python", "app.py"]           # commande par défaut
```
| Instruction | Rôle |
|---|---|
| `FROM` | image de base (obligatoire) |
| `RUN` | exécuter pendant la construction |
| `COPY` / `ADD` | copier des fichiers |
| `ENV` | variables d'environnement |
| `WORKDIR` | changer de répertoire de travail |
| `CMD` | commande par défaut (remplaçable) |
| `ENTRYPOINT` | commande fixe |
| `EXPOSE` | documentation des ports |
| `ARG` | variable de build |

## Volumes
| Commande | Effet |
|---|---|
| `docker volume create nom` | créer un volume |
| `docker volume ls` | lister |
| `docker volume rm nom` | supprimer |
| `docker run -v nom:/data image` | utiliser un volume nommé |
| `docker run -v /chemin/hote:/data image` | bind mount |

## Réseaux
| Commande | Effet |
|---|---|
| `docker network ls` | lister les réseaux |
| `docker network create nom` | créer un réseau |
| `docker network connect réseau ctr` | connecter un conteneur |
| `docker network disconnect réseau ctr` | déconnecter |
| `docker network rm nom` | supprimer |

Réseau par défaut : `bridge` (isolé), `host` (réseau hôte), `none`.

## Compose
```yaml
services:
  web:
    image: nginx
    ports:
      - "8080:80"
  db:
    image: postgres
    environment:
      POSTGRES_PASSWORD: secret
```
| Commande | Effet |
|---|---|
| `docker compose up -d` | démarrer en arrière-plan |
| `docker compose up --build` | démarrer en reconstruisant |
| `docker compose down` | arrêter et supprimer |
| `docker compose ps` | état des services |
| `docker compose logs -f` | logs |
| `docker compose exec service cmd` | exécuter dans un service |
| `docker compose config` | valider la config |

## Nettoyage
| Commande | Effet |
|---|---|
| `docker system prune` | supprimer tout ce qui est inutilisé |
| `docker system prune -a` | supprimer aussi les images non utilisées |
| `docker container prune` | conteneurs arrêtés |
| `docker image prune` | images orphelines |
| `docker volume prune` | volumes non utilisés |

## Dépannage
| Commande | Effet |
|---|---|
| `docker logs --tail 100 ctr` | 100 dernières lignes de logs |
| `docker inspect ctr \| grep -i ipaddress` | IP du conteneur |
| `docker system df` | espace utilisé |
| `docker events` | événements en direct |
